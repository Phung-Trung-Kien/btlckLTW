# CHECKLIST CODE REVIEW - MODULE COURSE
### Dự án: E-Learning Platform | Ngôn ngữ: C# (.NET 8) | Mô hình: Repository/Service Pattern

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | Course (`CourseController`, `CourseService`, `EnrollmentCourseService`, `CourseReviewService`, `LessonService`, `CategoryService`, `UpdateRequestController`) |
| **Người review** | _(điền tên)_ |
| **Ngày review** | _(điền ngày)_ |
| **Commit / Branch** | _(điền commit hash hoặc branch)_ |

---

## HƯỚNG DẪN SỬ DỤNG

| Kết quả | Ý nghĩa |
|---|---|
| **PASS** | Không phát hiện lỗi đáng kể |
| **FAIL** | Có >= 1 lỗi ảnh hưởng tới chức năng, bảo mật, dữ liệu hoặc khả năng bảo trì - ghi rõ file, dòng, mô tả lỗi vào cột Ghi chú |
| **NA** | Không áp dụng hoặc không thể trả lời - ghi rõ lý do |

---

## BẢNG CHECKLIST

| STT | Nội dung câu hỏi | PASS/FAIL/NA | Số Error | Ghi chú |
|---|---|---|---|---|
| 1 | Các quy tắc lập trình quy định trong kế hoạch dự án đã được tuân thủ ở mức chấp nhận được chưa? | PASS | 0 | |
| 2 | Comment/tài liệu trong mã nguồn có đủ để hiểu các luồng nghiệp vụ chính không? | PASS | 0 | |
| 3 | Các quy ước đặt tên class, method, biến và route có nhất quán với phần lớn module không? | PASS | 0 | |
| 4 | Mã nguồn đã được định dạng dễ đọc và thống nhất ở mức chấp nhận được chưa? | PASS | 0 | |
| 5 | Các đoạn mapping/dùng chung có bị lặp ở mức gây rủi ro lớn không? | PASS | 0 | |
| 6 | Có mã nguồn thừa hoặc controller rỗng gây ảnh hưởng trực tiếp tới chức năng chính không? | PASS | 0 | |
| 7 | Có method/API endpoint khai báo nhưng chưa triển khai gây lỗi nghiệp vụ rõ ràng không? | PASS | 0 | |
| 8 | Các đối tượng có khả năng null đã được kiểm tra và xử lý phù hợp chưa? | FAIL | 2 | (1) `CourseService.GetAllCoursesAsync()` và `GetCourseByIdAsync()` truy cập `Category.Name`, `Teacher.User.FullName` trực tiếp ở một số chỗ, có thể gây `NullReferenceException` nếu dữ liệu liên kết thiếu. (2) Một số endpoint trong `CourseController` lấy claim user bằng `User.FindFirst(...)?.Value` nhưng truyền xuống service khi chưa kiểm tra null đầy đủ. |
| 9 | Việc truy cập object, collection hoặc kết quả truy vấn nhìn chung có an toàn không? | PASS | 0 | |
| 10 | Tất cả chỉ số truy cập collection/list có nằm trong phạm vi cho phép không? | PASS | 0 | |
| 11 | Các collection/list có được khởi tạo đầy đủ trước khi sử dụng không? | PASS | 0 | |
| 12 | Tất cả điều kiện rẽ nhánh có chính xác và đúng nghiệp vụ không? | FAIL | 1 | `EnrollmentCourseService.GetEnrollmentInCourseAsync()` có logic phân quyền teacher/admin chưa đúng: điều kiện hiện tại có thể từ chối cả người dùng hợp lệ. Cần kiểm tra lại theo hướng: chỉ chặn khi user không phải teacher của course và cũng không phải admin. |
| 13 | Tất cả vòng lặp có điều kiện kết thúc rõ ràng và an toàn không? | PASS | 0 | |
| 14 | Điều kiện kết thúc vòng lặp có thực tế và tránh rủi ro lặp vô hạn không? | PASS | 0 | |
| 15 | Các phép chia, phép tính tiến độ có kiểm tra chia cho 0 hoặc dữ liệu không hợp lệ chưa? | PASS | 0 | |
| 16 | Có câu lệnh nào nằm trong vòng lặp cần đưa ra ngoài để tối ưu rõ rệt không? | PASS | 0 | |
| 17 | Có dead code gây ảnh hưởng trực tiếp tới hành vi hệ thống không? | PASS | 0 | |
| 18 | Các câu lệnh if có bị lồng nhau quá sâu, gây khó bảo trì ở luồng quan trọng không? | FAIL | 1 | `EnrollmentCourseService.RequestCancelEnrollmentAsync()` gom nhiều bước validate refund/cancel trong một method dài. Nên tách các bước kiểm tra điều kiện hủy, thời hạn, tiến độ, giá tiền để dễ test hơn. |
| 19 | Các tham số truyền giữa Controller - Service - Repository có khớp nhau không? | PASS | 0 | |
| 20 | Có biến, tham số hoặc dependency không sử dụng gây ảnh hưởng chức năng không? | PASS | 0 | |
| 21 | Các đối tượng và dữ liệu có được khởi tạo đúng cách trước khi sử dụng không? | PASS | 0 | |
| 22 | Các tài nguyên như DB context, transaction, stream, connection có được giải phóng đúng cách không? | PASS | 0 | |
| 23 | Các truy vấn dữ liệu có cho kết quả đúng và ổn định không? | FAIL | 1 | `CourseService.GetCoursesByTeacherIdAsync()` tính thống kê instructor từ danh sách course đã phân trang, khiến số liệu tổng quan có thể sai khi giáo viên có nhiều trang dữ liệu. |
| 24 | Trạng thái lỗi có được kiểm tra sau mỗi thao tác truy cập dữ liệu chính không? | PASS | 0 | |
| 25 | Transaction có được dùng khi cập nhật nhiều entity liên quan không? | PASS | 0 | |
| 26 | Trong các biểu thức số học, việc làm tròn và thứ tự ưu tiên toán tử đã được xem xét chưa? | FAIL | 1 | `CourseReviewService.UpdateCourseReviewAsync()` có biểu thức tính rating dùng `??` cùng phép cộng/chia, dễ sai do thứ tự ưu tiên toán tử. Cần tách biến trung gian để đảm bảo tính đúng average rating. |
| 27 | Các yêu cầu về thời gian phản hồi của module có được đáp ứng không? | PASS | 0 | |
| 28 | Có vấn đề hiệu năng lớn cần sửa ngay không? | PASS | 0 | |
| 29 | Các kiểm tra dữ liệu rỗng, dữ liệu không tồn tại và lỗi IO đã được thực hiện chưa? | PASS | 0 | |
| 30 | Các thông báo lỗi trả về có rõ ràng, đầy đủ và nhất quán không? | FAIL | 2 | (1) Một số service dùng `throw new Exception(...)` chung nên controller khó map đúng HTTP status. (2) Một vài message lỗi còn sai chính tả/không nhất quán ngôn ngữ, gây khó debug cho client. |
| 31 | Các điều kiện lỗi quan trọng đã được bẫy và xử lý phù hợp chưa? | FAIL | 1 | `EnrollmentController.GetEnrollmentInCourseAsync()` chưa catch riêng `UnauthorizedAccessException`, có thể trả 500 thay vì 401/403 trong lỗi phân quyền. |
| 32 | Trong các biểu thức số học, thứ tự xử lý và khả năng đọc hiểu có rõ ràng không? | PASS | 0 | |
| 33 | Trong các biểu thức quan hệ, so sánh có đúng kiểu dữ liệu và đúng mục đích sử dụng không? | FAIL | 1 | `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` so sánh status dạng string với nhiều kiểu chữ hoa/thường khác nhau (`active`, `Completed`, `Cancelled`). Nên chuẩn hóa enum/constant hoặc dùng so sánh không phân biệt hoa thường. |
| 34 | Trong các biểu thức logic, điều kiện có rõ ràng và tránh kết hợp quá phức tạp không? | PASS | 0 | |
| 35 | Trong các thao tác dữ liệu, tài nguyên có được mở/đóng đúng thời điểm không? | PASS | 0 | |
| 36 | Trong khai báo biến và cấu hình, có tránh hard code ở các giá trị nghiệp vụ quan trọng không? | FAIL | 3 | (1) `passScore = 70` là magic number. (2) `CertificateUrl = "No Certificate"` là hardcode string. (3) Status `"Peding Refund"` vừa hardcode vừa sai chính tả, có thể ghi dữ liệu trạng thái sai vào DB. |
| 37 | Các API trong module đã được bảo vệ bằng xác thực và phân quyền phù hợp chưa? | FAIL | 2 | (1) Một số endpoint public cần xác định rõ chỉ trả dữ liệu course đã publish, tránh lộ draft/rejected. (2) `UpdateProgressEnrollmentAsync()` từng có khối ownership check bị comment-out, cần đảm bảo student không thể cập nhật tiến độ của người khác. |
| 38 | Dữ liệu đầu vào từ API đã được validate đầy đủ theo nghiệp vụ Course chưa? | FAIL | 2 | (1) `CourseReviewService.AddCourseReviewAsync()` chưa thấy validate `Rating` trong khoảng hợp lệ, ví dụ 1-5. (2) `RequestCancelEnrollmentAsync()` cần validate `ReasonRequest` rỗng/null trước khi lưu. |
| 39 | Thông tin nhạy cảm có được cô lập trong cấu hình an toàn thay vì hard code không? | PASS | 0 | |
| 40 | Các luồng xử lý lỗi có ghi log đủ để truy vết lỗi quan trọng không? | FAIL | 1 | Các controller/service chính của Course chưa dùng `ILogger` trong catch, gây khó truy vết lỗi production. |
| 41 | Các tác vụ nặng hoặc gọi DB có được thiết kế bất đồng bộ khi phù hợp không? | PASS | 0 | |
| 42 | Các thao tác API quan trọng có xử lý trạng thái thời gian nhất quán không? | FAIL | 1 | `RequestCancelEnrollmentAsync()` có chỗ dùng `DateTime.Now` thay vì `DateTime.UtcNow`, có thể lệch timezone so với dữ liệu khác. |
| 43 | Module có cơ chế xử lý lỗi thống nhất và phản hồi thân thiện cho client không? | FAIL | 1 | Module vẫn trộn nhiều kiểu exception/response, ví dụ `Exception` chung ở service và catch khác nhau ở controller. Nên thống nhất typed exception hoặc middleware xử lý lỗi. |
| 44 | Việc xóa/cập nhật trạng thái dữ liệu có bảo toàn lịch sử khi cần không? | PASS | 0 | |
| 45 | Quyền truy cập và ownership của người dùng có được kiểm tra trước khi xử lý nghiệp vụ không? | FAIL | 1 | Khối kiểm tra ownership trong `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` đang bị comment-out, tạo rủi ro student cập nhật tiến độ enrollment không thuộc về mình nếu biết dữ liệu liên quan. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 29 | 16 | 0 |

| Tổng số Error phát hiện | 24 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: **PHÊ DUYỆT MỘT PHẦN**

Module Course có cấu trúc phân tầng rõ, transaction được dùng ở các luồng tạo/cập nhật lớn và các nghiệp vụ chính tương đối đầy đủ. Cần sửa trước các vấn đề quan trọng: logic phân quyền enrollment, ownership khi cập nhật tiến độ, công thức rating, thống kê instructor theo dữ liệu phân trang và chuẩn hóa status/validation.

