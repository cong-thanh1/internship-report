---
title: "Quản lý và tóm tắt tài liệu"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### Dashboard tài liệu

Sau khi xác thực, dashboard SmartStudy hiển thị thư viện tài liệu và trạng thái xử lý của người dùng. Giao diện cung cấp số liệu tài liệu, tìm kiếm, thao tác upload và điều hướng tới phòng học, tính năng luyện tập.

![Dashboard tài liệu SmartStudy](/images/5-Workshop/5.5-Document-Processing/document-dashboard.png)

*Hình 5.25: Dashboard SmartStudy với các tài liệu đã sẵn sàng trong thư viện cá nhân.*

#### API quản lý tài liệu

Các thao tác tài liệu được bảo vệ bằng Bearer access token:

* `GET /api/v1/documents` liệt kê tài liệu và hỗ trợ tham số search, status, page, limit.
* `GET /api/v1/documents/{documentId}` trả metadata, số trang và các chương được phát hiện.
* `DELETE /api/v1/documents/{documentId}` xóa object đã lưu và soft-delete bản ghi tài liệu.

![Đặc tả API quản lý tài liệu](/images/5-Workshop/5.5-Document-Processing/document-management-api.png)

*Hình 5.26: Các thao tác API đã xác thực để quản lý tài liệu.*

#### Luồng tóm tắt

SmartStudy hỗ trợ tóm tắt toàn bộ tài liệu và từng chương. Yêu cầu summary có thể chỉ định `scope: "full"` hoặc `scope: "chapter"`; yêu cầu theo chương cung cấp thêm chapter reference từ metadata tài liệu. Kết quả được cache để yêu cầu sau có thể tái sử dụng summary hiện có, trừ khi người dùng yêu cầu `forceRefresh`.

![Đặc tả API tóm tắt tài liệu](/images/5-Workshop/5.5-Document-Processing/summary-api.png)

*Hình 5.27: Summary endpoint, scope và cơ chế cache.*

Giao diện web trình bày summary dưới dạng các điểm chính cùng phần giải thích ngắn, giúp người học xem lại tài liệu trước khi chuyển sang phòng học AI hoặc bài luyện tập.

![Kết quả tóm tắt tài liệu SmartStudy](/images/5-Workshop/5.5-Document-Processing/summary-result.png)

*Hình 5.28: Tóm tắt toàn bộ tài liệu được hiển thị trong SmartStudy.*

Dashboard, API quản lý và kết quả summary cùng xác minh vòng đời tài liệu từ trạng thái sẵn sàng tới quá trình học tập.
