---
title: "Worklog Tuần 4"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Chuyển sang giai đoạn hạ tầng: thiết lập tài khoản AWS an toàn và dựng nền tảng mạng.
* Thiết kế VPC với private subnet không có đường ra Internet.
* Chuẩn bị tên miền và chứng chỉ cho hệ thống.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Thiết lập tài khoản AWS: bật MFA, tạo IAM user thay cho root <br> - Viết chính sách quyền tối thiểu cho việc triển khai <br> - Cài và cấu hình AWS CLI <br> - Ước tính chi phí bằng AWS Pricing Calculator | 13/07/2026 | 13/07/2026 | <https://000002.awsstudygroup.com/vi/> <br> <https://000048.awsstudygroup.com/vi/> <br> <https://000011.awsstudygroup.com/vi/> <br> <https://000007.awsstudygroup.com/vi/> |
| 3 | - Tạo VPC **10.0.0.0/16** <br> - Hai public subnet ở hai vùng sẵn sàng và một private subnet <br> - Internet Gateway và hai route table tương ứng | 14/07/2026 | 14/07/2026 | <https://000003.awsstudygroup.com/vi/> |
| 4 | - Thiết kế chín security group theo mô hình tham chiếu lẫn nhau: <br>&emsp; + **alb-sg**, **ecs-tasks-sg**, **qdrant-sg** <br>&emsp; + **grpc-api-sg**, **grpc-search-sg** <br>&emsp; + **rds-sg**, hai **redis-sg**, **rabbitmq-sg** | 15/07/2026 | 15/07/2026 | <https://000003.awsstudygroup.com/vi/> |
| 5 | - Tạo tám interface VPC endpoint và một gateway endpoint cho S3 <br> - So sánh chi phí giữa phương án endpoint và NAT Gateway <br> - Bật Private DNS cho toàn bộ interface endpoint | 16/07/2026 | 17/07/2026 | <https://000003.awsstudygroup.com/vi/> <br> <https://000034.awsstudygroup.com/vi/> |
| 6 | - Tạo Route 53 hosted zone và bản ghi cho tên miền <br> - Yêu cầu chứng chỉ ACM ở cả **ap-southeast-1** và **us-east-1** <br> - Tạo private hosted zone cho service discovery nội bộ | 17/07/2026 | 17/07/2026 | <https://000010.awsstudygroup.com/vi/> |

### Kết quả đạt được tuần 4:

* Tài khoản AWS được thiết lập theo hướng an toàn ngay từ đầu: không dùng root cho công việc hằng ngày, quyền triển khai được giới hạn đúng phạm vi cần thiết.
* Nền tảng mạng hoàn chỉnh với private subnet thực sự không có đường ra Internet — không NAT Gateway, mọi lời gọi tới dịch vụ AWS đều đi qua VPC endpoint và ở lại trong mạng riêng.
* Chín security group tham chiếu lẫn nhau thay vì mở theo dải IP, nên khi tài nguyên thay đổi địa chỉ thì quy tắc vẫn đúng mà không phải sửa gì.
* Hiểu được lý do một image ECR cần tới ba endpoint mới kéo về được: **ecr.api** cho xác thực, **ecr.dkr** cho giao thức registry, và S3 cho chính các layer.
* Tên miền và chứng chỉ đã sẵn sàng, trong đó chứng chỉ cho CloudFront bắt buộc phải nằm ở **us-east-1** — chi tiết dễ bỏ sót và chỉ phát hiện khi tạo distribution thì đã muộn.
* Bài học rút ra: VPC endpoint không hẳn rẻ hơn NAT Gateway khi số lượng endpoint lớn. Cái nó đổi lại là sự cô lập, và đó mới là lý do chọn chứ không phải giá.
