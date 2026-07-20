---
title: "Kết quả end-to-end"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.7.2. </b> "
---

#### Ứng dụng đã triển khai

Trang giới thiệu SmartStudy có thể được truy cập qua production URL của AWS Amplify và cung cấp điểm bắt đầu cho toàn bộ workflow học tập.

![Trang giới thiệu production của SmartStudy](/images/5.7-Testing-Results/5.7-Testing-Results/welcome-page.png)

*Hình 5.33: Ứng dụng SmartStudy sẵn sàng cho quá trình trình diễn production.*

#### Luồng quiz

Sau khi tài liệu sẵn sàng, SmartStudy có thể tạo bài quiz trắc nghiệm có giới hạn thời gian. Người học chọn đáp án, theo dõi tiến độ và nộp lượt làm bài từ giao diện web.

![Giao diện quiz SmartStudy](/images/5.7-Testing-Results/5.7-Testing-Results/quiz-page.png)

*Hình 5.34: Quiz được tạo và hiển thị cho người học.*

#### Xem lại kết quả

Giao diện kết quả hiển thị lựa chọn đã nộp, đáp án dự kiến, trạng thái đúng/sai và phần giải thích cho từng câu. Chức năng này hoàn tất workflow từ tài liệu nguồn tới đánh giá học tập.

![Kết quả quiz SmartStudy](/images/5.7-Testing-Results/5.7-Testing-Results/quiz-result.png)

*Hình 5.35: Giao diện kết quả và giải thích theo từng câu.*

Demo cuối xác minh các khả năng phía người dùng:

* Đăng ký và đăng nhập.
* Upload, theo dõi trạng thái xử lý, tìm kiếm và quản lý tài liệu.
* Tóm tắt toàn bộ tài liệu.
* Phòng học AI và trợ lý học tập.
* Tạo quiz, nộp bài, chấm điểm và xem lại kết quả.

#### Trạng thái vận hành và kế hoạch kết thúc

Dự án hoàn thành phát triển và kiểm thử cuối ngày 19/07/2026. Môi trường production SmartStudy, bao gồm AWS Amplify và các tài nguyên backend hỗ trợ, tiếp tục được duy trì đến hết ngày 30/07/2026 để phục vụ đánh giá báo cáo và trình diễn sản phẩm.

Vì vậy, cleanup tài nguyên không nằm trong phạm vi workshop tại thời điểm lập tài liệu. Sau giai đoạn đánh giá, nhóm có thể vô hiệu hóa Amplify deployment và xóa các CloudFormation stack không còn cần thiết để tránh phát sinh chi phí. Workshop không tuyên bố các tài nguyên này đã được xóa.
