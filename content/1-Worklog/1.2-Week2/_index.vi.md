---
title: "Worklog Tuần 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Hoàn thiện đường đi của một video: từ lúc người dùng upload tới lúc phát được.
* Xử lý transcode bất đồng bộ để request upload không bị chặn.
* Chuyển video sang định dạng MPEG-DASH đa độ phân giải.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Module **s3**: sinh presigned URL để client upload thẳng lên S3 <br> - Tách prefix **public/** và **private/** theo **VideoVisibility** | 29/06/2026 | 29/06/2026 | |
| 3 | - Dựng hàng đợi job bằng BullMQ trên Redis <br> - Cấu hình số lần thử lại và backoff cho job transcode | 30/06/2026 | 30/06/2026 |  |
| 4 | - **FfmpegService**: transcode sang MPEG-DASH <br>&emsp; + Nhiều mức độ phân giải theo enum **Resolution** <br>&emsp; + Sinh manifest và các segment <br> - Dọn file tạm sau khi xử lý xong | 01/07/2026 | 02/07/2026 | |
| 5 | - **VideoProcessingHandler**: cập nhật **ProcessingStatus** theo từng bước <br> - Định nghĩa exception riêng cho lỗi transcode <br> - Đẩy kết quả DASH lên S3 | 02/07/2026 | 03/07/2026 | |
| 6 | - Viết test cho **ffmpeg.service**, **video-processing.queue**, **video-processing.handler** <br> - **Thực hành:** đo thời gian transcode với video ở nhiều kích thước khác nhau | 03/07/2026 | 03/07/2026 | |

### Kết quả đạt được tuần 2:

* Upload không còn đi qua server ứng dụng: client lấy presigned URL rồi đẩy thẳng file lên S3, nhờ đó **api_service** không phải giữ file lớn trong bộ nhớ.
* Transcode chạy nền qua BullMQ. API trả về ngay sau khi tạo job, còn tiến độ được phản ánh qua bảng **VideoProcessing** và enum **ProcessingStatus**.
* Video được chuyển sang MPEG-DASH nhiều độ phân giải, cho phép trình phát tự chọn mức bitrate phù hợp với đường truyền.
* Job thất bại được thử lại có backoff thay vì hỏng vĩnh viễn; lỗi transcode được bọc trong exception riêng nên phân biệt được lỗi hệ thống với lỗi file đầu vào.
* Đo được thời gian transcode thực tế theo kích thước file — đây chính là số liệu dùng để chọn cấu hình CPU/RAM cho ECS task ở tuần 5.
* Bài học rút ra: transcode là công việc tốn CPU nhất trong hệ thống, nên tách khỏi tiến trình phục vụ request là quyết định phải làm sớm chứ không phải tối ưu về sau.
