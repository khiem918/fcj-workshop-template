---
title: "Worklog Tuần 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu tuần 1:

* Chốt phạm vi sản phẩm **VideoPlatformServer** và mô hình hoá dữ liệu cho toàn hệ thống.
* Dựng khung **api_service** trên NestJS 11 với GraphQL code-first.
* Hoàn thiện luồng xác thực người dùng và quản lý phiên đăng nhập.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Khảo sát yêu cầu, chốt phạm vi sản phẩm: upload → transcode → phát video → tìm kiếm <br> - Vẽ sơ đồ luồng dữ liệu và xác định ranh giới giữa hai service | 22/06/2026 | 22/06/2026 | |
| 3 | - Khởi tạo project NestJS 11 <br> - Cấu hình **ConfigModule** kèm validation cho biến môi trường <br> - Bật GraphQL code-first, tự sinh **schema.gql** | 23/06/2026 | 23/06/2026 | |
| 4 | - Thiết kế schema Prisma cho PostgreSQL: <br>&emsp; + **User**, **Video**, **VideoInformation** <br>&emsp; + **Comment**, **LikeVideo**, **Subscribe**, **WatchHistory** <br>&emsp; + Các enum trạng thái: **UploadVideoStatus**, **ProcessingStatus**, **VideoVisibility** <br> - Chạy migration đầu tiên | 24/06/2026 | 25/06/2026 | |
| 5 | - Module **auth**: xác thực Firebase ID token <br> - **SessionService** lưu phiên trên Redis <br> - Guard và decorator **@CurrentUser** dùng cho resolver | 25/06/2026 | 26/06/2026 |  |
| 6 | - Viết unit test cho **auth.service**, **auth.resolver**, **session.service** <br> - **Thực hành:** dựng môi trường local bằng Docker Compose (PostgreSQL, Redis, RabbitMQ) | 26/06/2026 | 26/06/2026 | |

### Kết quả đạt được tuần 1:

* Chốt được phạm vi sản phẩm và tách hệ thống thành hai service có trách nhiệm rõ ràng: **api_service** phụ trách nghiệp vụ và dữ liệu quan hệ, **search_service** phụ trách tìm kiếm ngữ nghĩa.
* Hoàn thành schema dữ liệu với 11 bảng và 8 enum trạng thái. Việc tách **Video** và **VideoInformation** thành hai bảng giúp phần metadata thay đổi thường xuyên không làm khoá bảng chính.
* **api_service** chạy được ở local, tự sinh GraphQL schema từ code nên không phải duy trì file schema thủ công.
* Xác thực hoạt động đầy đủ: token do Firebase cấp được verify ở phía server, phiên đăng nhập lưu trên Redis nên có thể thu hồi mà không cần chờ token hết hạn.
* Toàn bộ thành phần phụ thuộc (PostgreSQL, Redis, RabbitMQ) đã chạy được bằng một lệnh **docker compose up**, rút ngắn thời gian thiết lập môi trường cho các tuần sau.
* Bài học rút ra: thiết kế enum trạng thái ngay từ đầu quan trọng hơn tôi nghĩ — luồng transcode ở tuần 2 dựa hoàn toàn vào các trạng thái này.
