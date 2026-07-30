---
title: "Tự đánh giá"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Trong sáu tuần tham gia chương trình **First Cloud AI Journey**, từ **15/06/2026** đến **31/07/2026**, tôi thực hiện trọn vẹn một dự án từ dòng mã đầu tiên cho tới hệ thống chạy thật trên Internet: **VideoPlatformServer**.

Ba tuần đầu tôi xây dựng phần sản phẩm: hai service viết bằng hai ngôn ngữ khác nhau, giao tiếp hai chiều qua **gRPC** và **RabbitMQ**, kèm luồng transcode video bất đồng bộ sang **MPEG-DASH** và tìm kiếm lai kết hợp từ khoá với vector. Ba tuần sau tôi đưa toàn bộ lên AWS: thiết kế mạng riêng không có NAT Gateway, dựng tầng dữ liệu quản trị, triển khai ba service lên **ECS Fargate**, mở đường ra Internet qua **ALB** và **CloudFront**, và tự động hoá triển khai bằng **GitHub Actions** với **OIDC**.

Điều tôi thu được lớn nhất không phải danh sách dịch vụ đã dùng, mà là hiểu được vì sao chúng phải ghép với nhau theo đúng thứ tự đó. Một ví dụ cụ thể: tôi mất khá nhiều thời gian để tìm ra lý do ECS task xác thực được với ECR nhưng vẫn đứng im ở bước tải image — hoá ra thiếu gateway endpoint cho S3, vì các layer của image nằm trên S3 chứ không nằm trong ECR. Lỗi đó hiện ra như một timeout mạng thông thường. Nó dạy tôi rằng trong hạ tầng, thông báo lỗi thường chỉ về phía triệu chứng chứ không về phía nguyên nhân.

Về tác phong, tôi bám đúng kế hoạch sáu tuần đã đặt ra, hoàn thành cả phần lập trình lẫn phần triển khai, và ghi lại toàn bộ quá trình thành tài liệu workshop song ngữ để người khác có thể làm lại từ đầu.

Để phản ánh một cách khách quan quá trình thực tập, tôi xin tự đánh giá bản thân dựa trên các tiêu chí dưới đây:

| STT | Tiêu chí | Mô tả | Tốt | Khá | Trung bình |
| --- | --- | --- | --- | --- | --- |
| 1 | **Kiến thức và kỹ năng chuyên môn** | Hiểu biết về ngành, áp dụng kiến thức vào thực tế, kỹ năng sử dụng công cụ, chất lượng công việc | ✅ | ☐ | ☐ |
| 2 | **Khả năng học hỏi** | Tiếp thu kiến thức mới, học hỏi nhanh | ✅ | ☐ | ☐ |
| 3 | **Chủ động** | Tự tìm hiểu, nhận nhiệm vụ mà không chờ chỉ dẫn | ✅ | ☐ | ☐ |
| 4 | **Tinh thần trách nhiệm** | Hoàn thành công việc đúng hạn, đảm bảo chất lượng | ✅ | ☐ | ☐ |
| 5 | **Kỷ luật** | Tuân thủ giờ giấc, nội quy, quy trình làm việc | ☐ | ✅ | ☐ |
| 6 | **Tính cầu tiến** | Sẵn sàng nhận feedback và cải thiện bản thân | ✅ | ☐ | ☐ |
| 7 | **Giao tiếp** | Trình bày ý tưởng, báo cáo công việc rõ ràng | ☐ | ✅ | ☐ |
| 8 | **Hợp tác nhóm** | Làm việc hiệu quả với đồng nghiệp, tham gia nhóm | ☐ | ✅ | ☐ |
| 9 | **Ứng xử chuyên nghiệp** | Tôn trọng đồng nghiệp, đối tác, môi trường làm việc | ✅ | ☐ | ☐ |
| 10 | **Tư duy giải quyết vấn đề** | Nhận diện vấn đề, đề xuất giải pháp, sáng tạo | ✅ | ☐ | ☐ |
| 11 | **Đóng góp vào dự án/tổ chức** | Hiệu quả công việc, sáng kiến cải tiến, ghi nhận từ team | ☐ | ✅ | ☐ |
| 12 | **Tổng thể** | Đánh giá chung về toàn bộ quá trình thực tập | ✅ | ☐ | ☐ |

### Điểm mạnh

* **Đi được trọn vẹn một vòng đời hệ thống.** Từ thiết kế schema dữ liệu, viết mã, đóng gói container, dựng mạng, tới triển khai tự động — không dừng lại ở phần nào.
* **Chọn giải pháp có lý do, không chọn theo thói quen.** Ví dụ dùng VPC Endpoint thay NAT Gateway là để cô lập private subnet chứ không phải để tiết kiệm; tôi tính ra rằng với tám endpoint thì phương án này thực ra đắt hơn, và vẫn chọn nó một cách có ý thức.
* **Chẩn đoán được lỗi hạ tầng mà thông báo lỗi không chỉ đúng chỗ**, thay vì thử ngẫu nhiên cho tới khi chạy được.
* **Ghi lại đầy đủ.** Toàn bộ quá trình được viết thành workshop song ngữ có thể làm lại từ con số không.

### Cần cải thiện

* **Chưa dùng Infrastructure as Code.** Toàn bộ hạ tầng được dựng bằng tay qua console. Hệ thống chạy được, nhưng không tái lập được bằng một lệnh, và không có lịch sử thay đổi. Nếu làm lại, tôi sẽ dùng CloudFormation hoặc CDK ngay từ tuần đầu tiên của giai đoạn hạ tầng.
* **Tính sẵn sàng bị hy sinh cho tốc độ.** RDS chạy đơn vùng sẵn sàng và toàn bộ ECS task nằm trong một vùng sẵn sàng duy nhất, dù tôi đã tạo sẵn subnet ở vùng thứ hai. Đây là lựa chọn có ý thức để kịp tiến độ, nhưng nó là điểm yếu thật và tôi ghi nhận đúng như vậy chứ không giấu đi.
* **Ước lượng chi phí quá muộn.** Tôi chỉ tính kỹ chi phí VPC Endpoint sau khi đã dựng xong tám cái. Lẽ ra việc so sánh chi phí phải nằm ở bước thiết kế, không phải bước tổng kết.
* **Kiểm thử mới dừng ở tầng ứng dụng.** Phần mã có unit test, nhưng phần triển khai thì chưa có kiểm thử tự động nào ngoài health check của load balancer.
* **Kỹ năng làm việc nhóm và giao tiếp ít được rèn.** Do đây là dự án cá nhân, tôi hầu như không phải bảo vệ quyết định kỹ thuật của mình trước người khác — đó là kỹ năng tôi cần chủ động tìm cơ hội luyện thêm.
* **Tính kỷ luật về thời gian.** Có những phần tôi làm tập trung quá mức vào ngày cuối tuần thay vì phân bổ đều, khiến khối lượng công việc giữa các ngày chênh lệch lớn.
