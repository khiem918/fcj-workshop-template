---
title: "Các bài blogs đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Tại đây sẽ là phần liệt kê, giới thiệu các blogs mà các bạn đã đăng trên [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). Ví dụ:

###  [Blog 1 - BIG BANG, ROLLING, BLUE/GREEN HAY CANARY — NÊN CHỌN CHIẾN THUẬT DEPLOY NÀO TRÊN AWS?](3.1-Blog1/)
Blog này so sánh 4 chiến thuật deploy phổ biến trên AWS — Big Bang, Rolling, Blue/Green và Canary — về cách hoạt động, ưu nhược điểm, và gợi ý nên chọn chiến thuật nào để giảm thiểu rủi ro và downtime.

###  [Blog 2 - ZERO-SECRET DEPLOYMENT VỚI SECRETS MANAGER + IAM ROLE + IMDSV2](3.2-Blog2/)
Bài viết dạng field-report về một sự cố thật: private key Firebase bị corrupt do **jq** escape newline sai cách, và quá trình refactor sang pipeline "zero-secret deployment" dùng AWS Secrets Manager, IAM Role và IMDSv2 — kèm nhắc lại vụ rò rỉ dữ liệu Capital One 2019 và cách IMDSv2 chặn đúng đường tấn công đó.

###  [Blog 3 - KHI LOAD BALANCER PUBLIC KHÔNG CHỊU "KHAI" IP NỘI BỘ CỦA NÓ — VÀ CÁCH TỰ DỰNG DNS ZONE NỘI BỘ CHO ALB/NLB](3.3-Blog3/)
Blog này giải thích vì sao DNS name của internet-facing ALB/NLB chỉ phân giải ra public IP, và trình bày một giải pháp automation dùng CloudTrail + EventBridge + Lambda + Route 53 Private Hosted Zone (dựa trên bài viết của AWS, tác giả Gonzalo Escarrá) để luôn giữ đúng record DNS nội bộ cho private IP của load balancer.

