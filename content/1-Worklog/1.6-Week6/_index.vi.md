---
title: "Worklog Tuần 6"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

* Đưa hệ thống ra Internet qua ALB và CloudFront với tên miền riêng.
* Tự động hoá triển khai bằng GitHub Actions dùng OIDC, không lưu access key.
* Kiểm thử toàn hệ thống và hoàn thiện tài liệu báo cáo.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo hai target group kiểu **IP addresses** với health check riêng cho từng service <br> - Dựng Application Load Balancer nghe HTTPS trên hai public subnet <br> - Thêm listener rule định tuyến đường dẫn tìm kiếm sang **search-service** | 27/07/2026 | 27/07/2026 | <https://000006.awsstudygroup.com/vi/> |
| 3 | - Gắn ECS service vào target group, đặt health check grace period <br> - Tạo Origin Access Control và CloudFront distribution với hai origin <br> - Cập nhật bucket policy và tạo bản ghi alias trên Route 53 | 28/07/2026 | 28/07/2026 | <https://000082.awsstudygroup.com/vi/> |
| 4 | - Đăng ký GitHub làm OpenID Connect identity provider trong IAM <br> - Tạo **GitHubActionsDeployRole** với trust policy giới hạn theo repository và nhánh <br> - Khai báo repository variable cho workflow | 29/07/2026 | 29/07/2026 | <https://000044.awsstudygroup.com/vi/> <br> <https://000048.awsstudygroup.com/vi/> |
| 5 | - Viết workflow triển khai: build image → chạy migration → render task definition → cập nhật service → chờ ổn định <br> - Bật deployment circuit breaker để tự động rollback | 30/07/2026 | 30/07/2026 | <https://000017.awsstudygroup.com/vi/> <br> <https://000023.awsstudygroup.com/vi/> |
| 6 | - Kiểm thử toàn tuyến: DNS, ALB, CloudFront, upload, transcode, tìm kiếm <br> - Rà soát kiến trúc theo AWS Well-Architected Framework <br> - Hoàn thiện workshop và báo cáo thực tập | 31/07/2026 | 31/07/2026 | <https://000034.awsstudygroup.com/vi/> <br> <https://000013.awsstudygroup.com/vi/> |

### Kết quả đạt được tuần 6:

* Hệ thống truy cập được từ Internet qua tên miền riêng với HTTPS đầu cuối. Chuỗi đường đi hoàn chỉnh: trình duyệt → Route 53 → CloudFront → ALB → ECS task, còn file video được phục vụ thẳng từ S3 qua CloudFront.
* Bucket S3 giữ nguyên trạng thái riêng tư hoàn toàn. Origin Access Control cho phép CloudFront đọc được mà không cần mở bất kỳ quyền công khai nào.
* Triển khai tự động hoàn toàn: mỗi lần push lên **main**, pipeline tự build, chạy migration rồi cuộn cả hai service sang phiên bản mới. Không có access key nào được lưu ở GitHub — thông tin đăng nhập là tạm thời và chỉ tồn tại trong thời gian job chạy.
* Migration chạy trước và chặn bước deploy, nên khi migration thất bại thì phiên bản cũ vẫn tiếp tục chạy đúng với schema cũ thay vì để mã mới gặp schema nó không hiểu.
* Circuit breaker kết hợp với **wait-for-service-stability** khiến một image hỏng vừa làm workflow thất bại vừa tự động đưa service về phiên bản trước.
* Rà soát Well-Architected và ghi nhận trung thực các điểm còn hạn chế: RDS chạy đơn vùng sẵn sàng và toàn bộ task đang nằm trong một vùng sẵn sàng duy nhất — chấp nhận được với quy mô thực tập nhưng cần khắc phục nếu đưa vào sử dụng thật.
* Bài học lớn nhất của cả kỳ: phần khó không nằm ở việc viết mã, mà ở việc mô tả chính xác thành phần nào được phép nói chuyện với thành phần nào — và giữ cho mô tả đó đúng sau mỗi lần triển khai.
