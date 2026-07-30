---
title: "Worklog Tuần 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Xây dựng **search_service** bằng Python FastAPI với tìm kiếm lai: từ khoá kết hợp ngữ nghĩa.
* Thiết lập giao tiếp hai chiều giữa hai service qua gRPC.
* Đồng bộ metadata video bất đồng bộ qua RabbitMQ.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Khởi tạo **search_service** theo kiến trúc phân lớp: **app**, **core**, **domain**, **infrastructure** <br> - Dựng container dependency injection và cấu hình tập trung | 06/07/2026 | 06/07/2026 | |
| 3 | - Tích hợp Qdrant làm vector database <br> - Sinh embedding cho tiêu đề và mô tả video <br> - **semantic_worker** xử lý việc index nền | 07/07/2026 | 08/07/2026 |  |
| 4 | - Định nghĩa **proto/video.proto** <br>&emsp; + **VideoMetaDataService** — **api_service** phục vụ, cổng 50051 <br>&emsp; + **DeleteVideoService** — **search_service** phục vụ, cổng 50052 <br> - Sinh stub cho cả TypeScript và Python | 08/07/2026 | 09/07/2026 |  |
| 5 | - Đồng bộ metadata qua RabbitMQ với routing key **video.metadata.trans** và **video.metadata.res** <br> - Viết **consumer** và **dlq_consumer** cho hàng đợi lỗi | 09/07/2026 | 10/07/2026 |  |
| 6 | - Hoàn thiện endpoint **/api/v1/search** và **/api/v1/health** <br> - **Thực hành:** kiểm thử tích hợp hai service chạy song song bằng Docker Compose | 10/07/2026 | 10/07/2026 | |

### Kết quả đạt được tuần 3:

* **search_service** chạy được với kiến trúc phân lớp rõ ràng, phần logic nghiệp vụ không phụ thuộc trực tiếp vào FastAPI hay Qdrant nên thay thế hạ tầng về sau không phải sửa lõi.
* Tìm kiếm trả về kết quả kết hợp giữa khớp từ khoá và độ tương đồng vector, nhờ đó truy vấn mô tả nội dung vẫn tìm được video dù không trùng chữ nào trong tiêu đề.
* gRPC hai chiều hoạt động: **api_service** cung cấp metadata cho **search_service**, còn **search_service** gọi ngược lại khi cần xoá video khỏi chỉ mục. File **.proto** dùng chung là nguồn sự thật duy nhất cho cả hai ngôn ngữ.
* Metadata được đồng bộ bất đồng bộ qua RabbitMQ, kèm dead-letter queue nên thông điệp lỗi không biến mất âm thầm mà được giữ lại để xử lý.
* Kết thúc giai đoạn phát triển sản phẩm: cả hai service chạy song song ở local và phục vụ được toàn bộ luồng từ upload đến tìm kiếm.
* Bài học rút ra: dùng cả gRPC lẫn message queue trong cùng một hệ thống chỉ hợp lý khi tách bạch mục đích — gRPC cho lời gọi cần trả lời ngay, hàng đợi cho việc chấp nhận trễ và cần bảo đảm không mất.
