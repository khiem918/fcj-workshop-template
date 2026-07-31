---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# KHI LOAD BALANCER PUBLIC KHÔNG CHỊU "KHAI" IP NỘI BỘ CỦA NÓ — VÀ CÁCH TỰ DỰNG DNS ZONE NỘI BỘ CHO ALB/NLB

Hôm nay mình chia sẻ một bài toán networking khá "khó chịu" mà anh em làm hạ tầng lớn hay gặp: internet-facing Load Balancer chỉ cho bạn biết public IP, trong khi cái bạn cần lại là private IP. Nội dung dựa trên bài viết của AWS (tác giả Gonzalo Escarrá, Solutions Architect mảng Networking).

## Vấn đề nằm ở đâu?

ELB ra mắt từ năm 2009 và đến giờ vẫn là dịch vụ nền tảng của gần như mọi kiến trúc trên AWS. Hiện tại ALB và NLB có hai kiểu triển khai chính:

* **internet-facing** → đặt ở public subnet
* **internal** → đặt ở subnet không public

Dù chọn kiểu nào, bạn cũng nhận được một DNS name sinh ngẫu nhiên, dạng:

```
nlb-1234567890abcdef0.elb.us-east-1.amazonaws.com
```

Điểm mấu chốt: với internet-facing LB, DNS name do AWS quản lý **CHỈ** phân giải ra các public IP. Không hề có record nào trỏ tới địa chỉ nội bộ của load balancer.

Bình thường thì không sao. Nhưng nó thành vấn đề trong hai tình huống rất thực tế:

* Bạn dùng thiết bị/gateway của bên thứ ba để inspect traffic ingress, và rule trên thiết bị đó dựa theo DNS. Thiết bị nằm giữa ALB/NLB và Internet Gateway, nhưng khi query DNS thì nó lại nhận về public IP — không phải cái nó cần để định tuyến nội bộ.
* Bạn muốn đi tới chính internet-facing LB đó qua AWS Direct Connect, dùng private VIF hoặc transit VIF. Traffic đi trong đường riêng, nhưng DNS vẫn chỉ trả public IP.

Nói ngắn gọn: bạn cần **"split DNS"** — cùng một tên, nhưng bên trong VPC thì phân giải ra IP nội bộ.

## Ý tưởng giải pháp

Tự dựng các Private Hosted Zone trên Route 53 để chứa record nội bộ cho những internet-facing load balancer của bạn, kèm theo automation để record luôn được cập nhật tự động.

Vì sao phải automation? Vì IP của ENI mà load balancer sử dụng có thể thay đổi: LB được tạo mới, bị xóa, hoặc scale ra thêm ENI. Cập nhật tay là bất khả thi.

## Kiến trúc tổng thể

Giải pháp giả định bạn đang chạy môi trường multi-account (dùng AWS Organizations), và luồng hoạt động như sau:

CloudTrail (ở account workload) → EventBridge rule bắt các sự kiện vòng đời của load balancer → đẩy về custom event bus ở account shared services → Lambda tạo / sửa / xóa record trong Route 53 Private Hosted Zone → DynamoDB lưu state (mapping ARN ↔ CNAME ↔ ENI) để khỏi phải gọi API cross-account nhiều lần.

## Cần triển khai những gì?

**Ở account shared services:**

* 2 Private Hosted Zone cho mỗi Region:
  * ALB dùng: **internal.region.elb.amazonaws.com**
  * NLB dùng: **internal.elb.region.amazonaws.com**
* 2 Lambda function:
  * **r53-scavenger**: chạy theo lịch, quét và nạp private IP của các ALB/NLB đang tồn tại vào private hosted zone
  * **r53-updater**: chạy khi có sự kiện tạo / sửa / scale / xóa, giữ cho record luôn đúng
* 1 bảng DynamoDB để theo dõi thay đổi
* 1 custom EventBridge bus để nhận event từ các member account

**Ở các account workload (nơi có load balancer):**

* 1 IAM role để Lambda assume vào, phục vụ việc discovery record lần đầu
* 1 EventBridge rule để bắt các sự kiện CloudTrail

Chạy lần đầu: dùng AWS Step Functions chạy workflow **r53-scavenger** một lần duy nhất bằng tay, để nạp toàn bộ tên và IP của các load balancer đang có sẵn vào hosted zone. Sau đó mọi thay đổi sẽ tự động do **r53-updater** xử lý.

Toàn bộ có thể deploy bằng CloudFormation template mà AWS cung cấp trong bài gốc.

## Chi tiết hay: vì sao cần tới 2 EventBridge rule?

Đây là phần mình thấy đáng học nhất. Lý do: các thao tác của Elastic Load Balancing ghi log CloudTrail từ **hai** nguồn khác nhau tùy hành động.

**Rule 1** — bắt sự kiện tạo/xóa load balancer, source là **aws.elasticloadbalancing**:

```json
{
  "source": ["aws.elasticloadbalancing"],
  "detail": {
    "eventName": ["CreateLoadBalancer", "DeleteLoadBalancer"]
  }
}
```

Lưu ý: ngay lúc CREATE, ta chưa biết ENI sẽ mang IP nào — mới chỉ biết là LB vừa được tạo. Nên record ban đầu được ghi bằng một giá trị placeholder.

**Rule 2** — bắt sự kiện tạo/xóa network interface, source là **aws.ec2**. Nhưng EC2 thì tạo ENI suốt ngày, nên phải lọc thêm theo **sourceIPAddress** và **userAgent** để chỉ lấy đúng ENI do ELB tạo ra:

```json
{
  "source": ["aws.ec2"],
  "detail": {
    "eventSource": ["ec2.amazonaws.com"],
    "eventName": ["CreateNetworkInterface", "DeleteNetworkInterface"],
    "sourceIPAddress": ["elasticloadbalancing.amazonaws.com"],
    "userAgent": ["elasticloadbalancing.amazonaws.com"]
  }
}
```

Cả hai rule cùng đẩy event về custom bus ở account trung tâm. Resource policy của bus được cấu hình chỉ cho phép **PutEvents** từ các account thuộc cùng Organization (điều kiện **aws:PrincipalOrgID**). Nếu cần, bạn có thể mở rộng cho cross-organization hoặc từng account cụ thể.

Lambda **r53-updater** sau đó dùng một cross-account role để gọi **DescribeLoadBalancer** và **DescribeNetworkInterfaces** nhằm dựng record. Khi LB được tạo, bảng DynamoDB sẽ có cả **cname** (bản public) lẫn **cnameint** (bản nội bộ). Khi LB scale, record trong Route 53 sẽ được bổ sung thêm IP cho các ENI mới.

## Vài điều kiện cần lưu ý

* Giải pháp dựa trên AWS Organizations, vì nó dùng organization ID làm principal cho quyền trên EventBridge bus.
* Thiết kế này hoạt động trong phạm vi một Region duy nhất, với một cặp hosted zone.

## Kết quả đạt được

Sau khi triển khai, bạn có thể lấy private IP mà internet-facing ALB/NLB đang dùng chỉ bằng một câu query DNS đơn giản. Nhờ đó:

* Firewall/appliance nội bộ tra cứu dễ hơn rất nhiều
* Có thể dùng split DNS để cùng một tên phân giải về chính load balancer đó, nhưng theo đường nội bộ

#### Ảnh bài post

![img](/images/3-BlogsPosted/Screenshot_20260731_205503.png)

> Đây là một ví dụ hay về cách kết hợp CloudTrail, EventBridge và Lambda để tự động hóa một mảnh ghép networking mà AWS không cung cấp sẵn — private DNS cho chính load balancer public của mình.
