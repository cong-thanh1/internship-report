---
title: "Bản đề xuất"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# SmartStudy AI
## Trợ lý học tập serverless dựa trên tài liệu

### 1. Tóm tắt điều hành

SmartStudy AI là nền tảng học tập trên web giúp sinh viên học từ chính tài liệu PDF của mình. Người dùng có thể tạo tài khoản, tải lên và quản lý tài liệu, đặt câu hỏi dựa trên nội dung tài liệu, tạo bài luyện tập, nộp đáp án và xem điểm cùng lời giải thích.

Ứng dụng sử dụng kiến trúc serverless trên AWS cho việc lưu trữ web, xác thực, API, lưu trữ tài liệu, xử lý bất đồng bộ, dữ liệu ứng dụng và giám sát. Do tài khoản dự án không thể sử dụng Amazon Bedrock, tác vụ AI được chuyển sang model Qwen 2.5 7B chạy bằng Ollama trên máy chủ AI local tự quản lý. Repository của dự án có thành phần Cloudflare relay để hỗ trợ kết nối tới dịch vụ AI local; chi tiết triển khai sẽ được xác minh khi hoàn thiện tài liệu kỹ thuật.

### 2. Vấn đề cần giải quyết

Sinh viên thường phải học từ nhiều tài liệu dài và rời rạc. Việc đọc, tóm tắt, tự tạo câu hỏi ôn tập và theo dõi kết quả theo cách thủ công tốn nhiều thời gian. Chatbot thông thường cũng có thể trả lời ngoài nội dung tài liệu, khiến người học khó kiểm chứng câu trả lời có thực sự dựa trên nguồn đã cung cấp hay không.

SmartStudy giải quyết các vấn đề trên bằng một không gian học tập thống nhất cho phép:

* Tải lên và tổ chức tài liệu PDF.
* Đặt câu hỏi dựa trên tài liệu đã tải lên.
* Nhận câu trả lời kèm tham chiếu tới nội dung liên quan.
* Tự động tạo bài luyện tập.
* Lưu hội thoại, lượt làm bài, điểm số và lời giải thích.

### 3. Giải pháp đề xuất

SmartStudy cung cấp các luồng chính sau:

1. Người dùng đăng ký hoặc đăng nhập thông qua Amazon Cognito.
2. Ứng dụng web gửi yêu cầu backend qua Amazon API Gateway.
3. Tài liệu được lưu trên Amazon S3 và đưa vào Amazon SQS để xử lý bất đồng bộ.
4. AWS Lambda xử lý tài liệu và lưu metadata, chunk, hội thoại, quiz cùng lượt làm bài trong Amazon DynamoDB.
5. Yêu cầu AI được chuyển đến model Ollama local thông qua cơ chế relay của dự án.
6. Amazon CloudWatch thu thập log, metric và alarm vận hành.

### 4. Kiến trúc giải pháp sơ bộ

#### Kiến trúc đề xuất ban đầu

Sơ đồ dưới đây ghi lại kiến trúc được đề xuất trước khi triển khai. Sơ đồ được giữ lại để thể hiện định hướng thiết kế ban đầu; một số dịch vụ trong sơ đồ sau đó đã bị loại bỏ hoặc thay thế do giới hạn dịch vụ và yêu cầu vận hành thực tế.

![Kiến trúc đề xuất ban đầu của SmartStudy AI](/internship-report/images/2-Proposal/initial-proposed-architecture.jpg)

*Hình 1: Kiến trúc đề xuất ban đầu trước khi có các thay đổi trong quá trình triển khai. Sơ đồ này không đại diện cho hệ thống được triển khai cuối cùng.*

#### Kiến trúc sau điều chỉnh

![Kiến trúc triển khai cuối cùng của SmartStudy AI](/internship-report/images/2-Proposal/final-deployed-architecture.png)

*Hình 2: Kiến trúc triển khai cuối cùng, gồm workload serverless trên AWS và môi trường AI Ollama tự quản lý.*

Dự án duy trì tài nguyên staging và production riêng biệt tại region `us-east-1`. Branch staging trên GitHub được dùng cho quá trình tích hợp; các thay đổi đã được kiểm tra được merge vào branch main và triển khai production thông qua AWS Amplify.

### 5. Dịch vụ và công nghệ sử dụng

#### Dịch vụ AWS

* **AWS Amplify Hosting:** Build và triển khai frontend từ GitHub.
* **Amazon Cognito:** Quản lý đăng ký, đăng nhập và token người dùng.
* **Amazon API Gateway:** Cung cấp HTTP API cho backend.
* **AWS Lambda:** Xử lý API, tiếp nhận tài liệu và logic pre-sign-up.
* **Amazon S3:** Lưu tài liệu PDF và asset của AWS CDK.
* **Amazon SQS:** Tách luồng upload khỏi quá trình xử lý tài liệu bất đồng bộ.
* **Amazon DynamoDB:** Lưu tài liệu, document chunk, hội thoại, tin nhắn, quiz, bài thi, lượt làm bài, bản tóm tắt và trạng thái AI job.
* **Amazon CloudWatch:** Cung cấp log, metric và alarm cho Lambda và SQS.
* **AWS IAM:** Phân quyền giữa người dùng và các dịch vụ AWS.
* **AWS CDK và AWS CloudFormation:** Khai báo và triển khai lặp lại hạ tầng staging, production.

#### Công nghệ hỗ trợ

* **GitHub:** Quản lý source code, phối hợp qua branch và cung cấp nguồn cho continuous deployment của Amplify.
* **Ollama:** Cung cấp model ngôn ngữ từ máy chủ AI local tự quản lý.
* **Qwen 2.5 7B:** Model ngôn ngữ local được sử dụng trong luồng AI.
* **Cloudflare relay:** Cung cấp lớp relay liên quan đến việc truy cập dịch vụ AI local. Cơ chế relay cụ thể cần được xác minh thêm từ cấu hình triển khai.

### 6. Thay đổi kiến trúc và các giới hạn

| Đề xuất ban đầu | Phương án triển khai | Lý do |
| --- | --- | --- |
| Custom domain bằng Route 53 | Domain mặc định `amplifyapp.com` | Nhóm không thể hoàn tất việc mua domain. |
| Amazon Bedrock | Ollama với Qwen 2.5 7B trên máy chủ AI local tự quản lý | Tài khoản dự án không được phép thực hiện operation cần thiết trên Bedrock. |
| AWS Secrets Manager | Cấu hình biến môi trường khi triển khai | Secrets Manager không được sử dụng trong bản triển khai cuối. |
| AWS CloudTrail | Giám sát bằng Amazon CloudWatch | CloudTrail không nằm trong phạm vi đã triển khai. |
| Xử lý tài liệu trực tiếp/đồng bộ | Processing queue trên Amazon SQS | Xử lý bất đồng bộ giúp tách tác vụ dài khỏi API phục vụ người dùng. |
| Một môi trường duy nhất | Tách staging và production | Giảm rủi ro khi kiểm thử thay đổi với dữ liệu và dịch vụ production. |

### 7. Kế hoạch triển khai

* **Lập kế hoạch:** Xác định vấn đề, tính năng, user flow và kiến trúc AWS ban đầu.
* **Điều chỉnh kiến trúc:** Kiểm tra khả năng sử dụng dịch vụ và thay thế các thành phần không khả dụng.
* **Phát triển ứng dụng:** Xây dựng frontend, backend API, xử lý tài liệu, phòng học AI và bài luyện tập.
* **Triển khai hạ tầng:** Khởi tạo tài nguyên staging và production bằng AWS CDK, CloudFormation.
* **Tích hợp:** Kết nối GitHub với Amplify và tích hợp Cognito, API Gateway, Lambda, S3, SQS, DynamoDB cùng dịch vụ AI local.
* **Kiểm thử:** Kiểm tra đăng ký, upload, xử lý tài liệu, hỏi đáp, tạo quiz, chấm điểm và xem kết quả.

### 8. Rủi ro và phương án giảm thiểu

| Rủi ro | Ảnh hưởng | Phương án giảm thiểu |
| --- | --- | --- |
| Máy chủ AI local hoặc dịch vụ Ollama không khả dụng | Chức năng học với AI và tạo quiz có thể gián đoạn | Duy trì máy chủ khi demo, theo dõi kết nối và xây dựng định hướng chuyển sang dịch vụ AI managed. |
| Relay hoặc kết nối Internet không ổn định | Yêu cầu AI chậm hoặc thất bại | Thiết lập timeout, xử lý lỗi và phản hồi retry rõ ràng. |
| Xử lý tài liệu thất bại | Tài liệu không sẵn sàng để học | Sử dụng SQS, trạng thái xử lý, cơ chế xử lý lỗi của ứng dụng và giám sát CloudWatch. |
| Thay đổi staging ảnh hưởng production | Gián đoạn dịch vụ hoặc sai lệch dữ liệu | Tách tài nguyên staging, production và triển khai qua quy trình merge trên GitHub. |
| Mức sử dụng AWS vượt dự kiến | Phát sinh chi phí ngoài kế hoạch | Theo dõi mức sử dụng và chỉ duy trì tài nguyên cần thiết cho đánh giá. |

### 9. Kết quả kỳ vọng

* Ứng dụng SmartStudy được triển khai và truy cập qua AWS Amplify.
* Đăng ký và đăng nhập an toàn bằng Amazon Cognito.
* Upload PDF và xử lý tài liệu bất đồng bộ ổn định.
* Hỏi đáp AI dựa trên tài liệu và tự động tạo bài luyện tập.
* Lưu lịch sử học tập và hỗ trợ xem lại kết quả.
* Hạ tầng AWS staging, production có thể triển khai lặp lại.
* Tạo nền tảng để thay Ollama local bằng dịch vụ AI managed trong phiên bản tương lai.

Bản đề xuất này được xây dựng từ tài nguyên AWS đã triển khai, video demo hoàn chỉnh và tổng quan repository GitHub hiện có. Chi tiết kết nối Cloudflare với Ollama sẽ được hoàn thiện sau khi xác minh cấu hình vận hành của máy chủ AI local.
