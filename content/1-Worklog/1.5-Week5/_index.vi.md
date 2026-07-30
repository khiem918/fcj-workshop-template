---
title: "Worklog Tuần 5"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Dựng tầng dữ liệu quản trị trên AWS: cơ sở dữ liệu, bộ nhớ đệm, message broker và lưu trữ.
* Đóng gói hai service thành container image và đưa lên ECR.
* Triển khai ba service lên ECS Fargate.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo RDS PostgreSQL **db.t3.micro** đặt trong private subnet <br> - Hai cụm ElastiCache Valkey, mỗi service một cụm <br> - Amazon MQ chạy RabbitMQ trên **mq.m7g.medium** | 20/07/2026 | 20/07/2026 | <https://000005.awsstudygroup.com/vi/> |
| 3 | - Tạo S3 bucket, chặn toàn bộ truy cập công khai <br> - EFS kèm access point để Qdrant lưu vector lâu dài <br> - Lưu cấu hình nhạy cảm vào SSM Parameter Store dạng **SecureString** mã hoá bằng KMS | 21/07/2026 | 21/07/2026 | <https://000057.awsstudygroup.com/vi/> <br> <https://000033.awsstudygroup.com/vi/> <br> <https://000031.awsstudygroup.com/vi/> |
| 4 | - Tạo hai repository ECR, bật quét lỗ hổng khi push <br> - Viết Dockerfile nhiều tầng cho cả hai service <br> - Build và push image lên ECR <br> - Soạn task definition và **ecsTaskExecutionRole** | 22/07/2026 | 22/07/2026 | <https://000015.awsstudygroup.com/vi/> |
| 5 | - Tạo ECS cluster và triển khai lần lượt: <br>&emsp; + **qdrant** trước, gắn EFS <br>&emsp; + **api-service** và **search-service** sau <br> - Thiết lập service discovery nội bộ qua Cloud Map | 23/07/2026 | 24/07/2026 | <https://000016.awsstudygroup.com/vi/> <br> <https://000067.awsstudygroup.com/vi/> |
| 6 | - Chạy **prisma migrate deploy** bằng một ECS task chạy một lần <br> - Dùng ECS Exec để chẩn đoán task <br> - **Xử lý sự cố:** task xác thực ECR thành công nhưng đứng ở bước tải image | 24/07/2026 | 24/07/2026 | <https://000058.awsstudygroup.com/vi/> |

### Kết quả đạt được tuần 5:

* Toàn bộ tầng dữ liệu nằm trong private subnet và chỉ nhận kết nối từ security group của ECS task, không có thành phần nào lộ ra Internet.
* Không còn thông tin nhạy cảm nào nằm trong image hay biến môi trường tĩnh: cấu hình được đọc từ Parameter Store lúc task khởi động, và task role chỉ được phép giải mã đúng nhánh tham số của chính nó.
* Ba service chạy ổn định trên Fargate. Qdrant được triển khai trước vì hai service còn lại phụ thuộc vào nó, và dữ liệu vector nằm trên EFS nên task khởi động lại không mất chỉ mục.
* Migration cơ sở dữ liệu chạy như một task riêng thay vì nhét vào lúc container khởi động, nhờ vậy nhiều task cùng khởi động không chạy migration chồng lên nhau.
* **Xử lý sự cố đáng nhớ nhất:** task kéo image thất bại dù ECR đã xác thực thành công. Nguyên nhân là thiếu gateway endpoint cho S3 — các layer của image được lưu trên S3 chứ không nằm trong ECR. Lỗi hiện ra như một timeout mạng nên mất khá nhiều thời gian mới lần ra.
* Bài học rút ra: thứ tự triển khai quan trọng ngang với cấu hình. Bắt đầu từ thành phần không phụ thuộc ai giúp mỗi lỗi phát sinh chỉ có một nguyên nhân khả dĩ.
