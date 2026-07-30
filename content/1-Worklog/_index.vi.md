---
title: "Nhật ký công việc"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Chương trình thực tập được thực hiện trong **6 tuần**, từ **22/06/2026** đến **31/07/2026**, xoay quanh một sản phẩm duy nhất: **VideoPlatformServer** — nền tảng chia sẻ video có tìm kiếm ngữ nghĩa, được xây dựng rồi triển khai lên AWS.

Sáu tuần được chia thành hai giai đoạn cân bằng nhau:

* **Tuần 1 – 3: xây dựng sản phẩm.** Phát triển hai service từ con số không — **api_service** trên NestJS 11 với GraphQL, và **search_service** trên Python FastAPI — cùng toàn bộ luồng nghiệp vụ: xác thực, upload, transcode bất đồng bộ sang MPEG-DASH, và tìm kiếm lai kết hợp từ khoá với vector.
* **Tuần 4 – 6: hạ tầng và triển khai.** Thiết kế mạng riêng trên AWS, dựng tầng dữ liệu quản trị, đưa cả ba service lên ECS Fargate, mở đường ra Internet qua ALB và CloudFront, cuối cùng là tự động hoá triển khai bằng GitHub Actions với OIDC.

Cách chia này là chủ ý: ba tuần đầu tạo ra một hệ thống chạy được ở local, để ba tuần sau tập trung hoàn toàn vào bài toán hạ tầng mà không phải vừa sửa mã vừa sửa cấu hình cùng lúc.

**Tuần 1:** [Thiết kế dữ liệu và dựng khung api_service](1.1-week1/)

**Tuần 2:** [Upload lên S3 và transcode bất đồng bộ sang MPEG-DASH](1.2-week2/)

**Tuần 3:** [search_service, tìm kiếm ngữ nghĩa và giao tiếp liên service](1.3-week3/)

**Tuần 4:** [Nền tảng AWS: IAM, VPC, security group và VPC endpoint](1.4-week4/)

**Tuần 5:** [Tầng dữ liệu quản trị và triển khai lên ECS Fargate](1.5-week5/)

**Tuần 6:** [ALB, CloudFront và CI/CD tự động với GitHub Actions](1.6-week6/)
