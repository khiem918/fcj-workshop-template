---
title: "Chia sẻ, đóng góp ý kiến"
date: 2024-01-01
weight: 7
chapter: false
pre: " <b> 7. </b> "
---

### Đánh giá chung

**1. Môi trường làm việc**

Điều tôi thấy đặc biệt nhất ở chương trình là được tự chọn đề tài và tự chịu trách nhiệm với nó từ đầu đến cuối. Không ai giao cho tôi một danh sách việc phải làm; tôi phải tự quyết định xây gì, dựng theo thứ tự nào, và tự gánh hậu quả khi quyết định sai.

Đây vừa là điểm mạnh vừa là điểm khó. Mạnh, vì nó buộc tôi phải hiểu thật chứ không làm theo hướng dẫn. Khó, vì khi đi sai hướng thì không có ai chặn lại — sai lầm chỉ lộ ra lúc hệ thống không chạy, và khi đó thường đã tốn vài ngày.

**2. Sự hỗ trợ của mentor / team admin**

Đội ngũ admin đã hỗ trợ tôi giải đáp các thắc mắc trong quá trình tìm hiểu AWS. Với người lần đầu tiếp cận một hệ sinh thái rộng như vậy, việc có người trả lời đúng lúc giúp tôi tránh được nhiều hướng đi sai ngay từ đầu.

**3. Sự phù hợp giữa công việc và chuyên ngành học**

Phần lập trình nằm đúng trong chuyên ngành: thiết kế cơ sở dữ liệu, API, xử lý bất đồng bộ, kiểm thử. Phần hạ tầng thì gần như hoàn toàn mới. Ở trường, mạng máy tính được dạy như lý thuyết; đến khi phải tự quyết định subnet nào đặt ở đâu, security group nào tham chiếu security group nào, thì cùng một kiến thức đó chuyển thành một thứ khác hẳn.

Sự chênh lệch này chính là giá trị lớn nhất tôi nhận được. Không có chương trình, tôi sẽ không có lý do gì để dựng một hệ thống thật trên AWS bằng tiền và thời gian của mình.

**4. Cơ hội học hỏi & phát triển kỹ năng**

Những thứ tôi học được và có thể chỉ ra cụ thể:

* Thiết kế mạng riêng trên AWS: subnet, route table, và vì sao một private subnet thực sự thì không được có đường ra Internet.
* Mô hình security group tham chiếu lẫn nhau thay vì mở theo dải IP — thay đổi hẳn cách tôi nghĩ về phân quyền mạng.
* VPC Endpoint và cái giá thật của nó, gồm cả việc nó không rẻ hơn NAT Gateway như tôi tưởng lúc đầu.
* Triển khai container lên ECS Fargate, và vì sao thứ tự triển khai lại quan trọng ngang với cấu hình.
* Xác thực liên hệ thống bằng OIDC thay vì access key tĩnh — với tôi đây là phần đáng giá nhất về mặt bảo mật.
* Cách viết tài liệu kỹ thuật để người khác làm lại được, chứ không phải để chứng minh mình đã làm.

**5. Văn hóa & tinh thần đồng đội**

Việc chương trình yêu cầu mỗi người công khai worklog và blog theo tuần tạo ra một áp lực lành mạnh: biết rằng công việc của mình sẽ có người đọc thì cách mình ghi chép cũng khác đi. Đọc phần trình bày của người khác cũng là cách tôi biết mình đang ở đâu.

**6. Chính sách / phúc lợi cho thực tập sinh**

Hệ thống tài liệu tại [cloudjourney.awsstudygroup.com](https://cloudjourney.awsstudygroup.com/) là điểm cộng lớn, đặc biệt vì có bản tiếng Việt. Với người lần đầu tiếp xúc AWS, việc đọc khái niệm mới bằng tiếng mẹ đẻ rồi mới đối chiếu sang tài liệu gốc tiếng Anh giúp tiết kiệm rất nhiều thời gian.

### Một số câu hỏi khác

**Điều tôi hài lòng nhất trong thời gian thực tập?**

Khoảnh khắc gõ tên miền của mình vào trình duyệt và hệ thống trả về đúng kết quả — sau khi đi qua Route 53, CloudFront, ALB, rồi tới container do chính mình đóng gói. Trước đó tôi chỉ từng chạy ứng dụng trên máy cá nhân.

**Điều tôi nghĩ chương trình cần cải thiện cho các khoá sau?**

Nên có một buổi hoặc một tài liệu về **ước lượng chi phí trước khi dựng**. Tôi chỉ nhận ra tám VPC Endpoint tốn hơn một NAT Gateway sau khi đã dựng xong cả tám. Với người tự trả tiền cho tài khoản thực tập, đây là thứ cần biết trước chứ không phải biết sau.

**Nếu giới thiệu cho bạn bè, tôi có khuyên họ tham gia không?**

Có, nhưng kèm một lưu ý: chương trình này chỉ phát huy tác dụng nếu người tham gia chọn một đề tài đủ thật. Nếu chọn một bài toán nhỏ và an toàn, sáu tuần sẽ trôi qua mà không học được gì đáng kể.

### Đề xuất & mong muốn

* **Bổ sung workshop về VPC Endpoint.** Trong lúc tìm tài liệu tham khảo, tôi nhận thấy hệ thống cloudjourney có workshop cho VPC, VPC Peering và Transit Gateway, nhưng chưa có bài riêng về VPC Endpoint — trong khi đây lại là thành phần bắt buộc với bất kỳ ai chạy container trong private subnet, và cũng là nguyên nhân của lỗi khó chẩn đoán nhất mà tôi gặp.
* **Thêm một checklist dọn dẹp tài nguyên.** Nhiều dịch vụ vẫn tính tiền khi không dùng. Một danh sách kiểm tra chuẩn sẽ tránh cho người học những hoá đơn bất ngờ sau khi kết thúc chương trình.
* **Khuyến khích dùng Infrastructure as Code sớm hơn.** Nếu được nhắc từ đầu, tôi đã dựng hạ tầng bằng CloudFormation thay vì bằng tay qua console.
* **Về việc tiếp tục:** tôi muốn tiếp tục phát triển hệ thống này sau kỳ thực tập, trước hết là khắc phục điểm yếu đơn vùng sẵn sàng và chuyển toàn bộ hạ tầng sang dạng mã.
