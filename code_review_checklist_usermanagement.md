# CHECKLIST CODE REVIEW - MODULE USERMANAGEMENT
### Dự án: E-Learning Platform | Ngôn ngữ: C# (.NET 8) | Phạm vi: Authentication, Authorization, User/Role, Admin/Teacher

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | UserManagement BE (`AuthController`, `AuthService`, `AdminController`, `AdminService`, `TeacherController`, `TeacherService`, repository và DTO liên quan tới User/Student/Teacher/Admin) |
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
| 1 | Các quy tắc lập trình quy định trong kế hoạch dự án đã được tuân thủ chưa? | PASS | 0 | |
| 2 | Các tài liệu hướng dẫn trực tiếp trong mã nguồn (comment, XML comment) có đủ để hiểu luồng xử lý chính không? | PASS | 0 | |
| 3 | Các quy ước đặt tên namespace, class, method, biến và property có nhất quán với cách module đang dùng không? | FAIL | 2 | (1) `TeacherController.cs` L25 đọc claim `"teacherId"` trong khi `AuthService.GenerateJwtToken()` L336 sinh claim `"TeacherId"` - sai hoa/thường, có thể làm API teacher overview trả 401. (2) `Teacher.cs` L16 đặt property `instruction` bằng camelCase, không nhất quán với PascalCase trong C#. |
| 4 | Mã nguồn đã được định dạng dễ đọc và thống nhất ở mức chấp nhận được chưa? | PASS | 0 | |
| 5 | Các đoạn logic dùng chung đã được tổ chức đủ tốt, không gây rủi ro lớn do lặp code chưa? | PASS | 0 | |
| 6 | Có mã nguồn thừa hoặc endpoint test nào gây ảnh hưởng trực tiếp tới chức năng chính không? | PASS | 0 | |
| 7 | Có method/API/DTO khai báo nhưng không dùng gây lỗi runtime hoặc lỗi build không? | PASS | 0 | |
| 8 | Các đối tượng có khả năng null đã được kiểm tra và xử lý phù hợp chưa? | FAIL | 2 | (1) `AuthController.RefreshToken()` L89 và `RefreshAdminToken()` L104 dùng `dto.RefreshToken` nhưng không kiểm tra `dto` hoặc token null/empty. (2) `AdminService.GetFullCourseByIdAsync()` L119-L120 truy cập `course.Content.Id` và `course.Content.Lessons` trực tiếp, có thể gây `NullReferenceException` nếu course chưa có content. |
| 9 | Việc truy cập navigation property và kết quả truy vấn nhìn chung có an toàn không? | PASS | 0 | |
| 10 | Tất cả chỉ số truy cập collection/list có nằm trong phạm vi cho phép không? | PASS | 0 | |
| 11 | Các collection/list trong entity User/Student/Teacher/Admin có được khởi tạo trước khi sử dụng không? | PASS | 0 | |
| 12 | Các điều kiện rẽ nhánh có chính xác và đúng nghiệp vụ UserManagement không? | FAIL | 2 | (1) `TeacherController.GetOverview()` L25 dùng sai tên claim nên teacher hợp lệ có thể bị từ chối. (2) `AuthService.LoginAsync()` L102 bắt buộc login thường phải có Student record, khiến admin-only user không thể login qua endpoint login chung. |
| 13 | Tất cả vòng lặp có điều kiện kết thúc rõ ràng và an toàn không? | PASS | 0 | |
| 14 | Điều kiện để kết thúc vòng lặp có thực tế và tránh rủi ro lặp vô hạn không? | PASS | 0 | |
| 15 | Các phép tính số học, phân trang và giới hạn review có kiểm tra đầu vào không hợp lệ chưa? | FAIL | 2 | (1) `AdminService.GetCoursesByAdminAsync()` L72 tính `totalCount / pageSize` nhưng không validate `pageSize > 0`. (2) `AdminRepository.GetCoursesByAdminAsync()` L35 dùng `Skip((page - 1) * pageSize)` nhưng không validate `page >= 1`, có thể sinh `Skip` âm. |
| 16 | Có câu lệnh nào nằm trong vòng lặp mà cần đưa ra ngoài để tối ưu rõ rệt không? | PASS | 0 | |
| 17 | Có phần nào trong mã nguồn là dead code gây ảnh hưởng tới hành vi hệ thống không? | PASS | 0 | |
| 18 | Các câu lệnh if có bị lồng nhau quá sâu, gây khó đọc và khó bảo trì không? | PASS | 0 | |
| 19 | Các tham số truyền giữa Controller - Service - Repository có khớp nhau không? | FAIL | 1 | `TeacherController` lấy claim `"teacherId"` L25 nhưng `AuthService` sinh `"TeacherId"` L336. Đây là lỗi liên tầng trực tiếp giữa Auth và Teacher. |
| 20 | Có biến, tham số hoặc dependency không sử dụng gây ảnh hưởng chức năng không? | PASS | 0 | |
| 21 | Dữ liệu token có được trả về đầy đủ sau khi đăng ký/đăng nhập không? | FAIL | 1 | `AuthService.RegisterAsync()` L68-L71 tạo và lưu refresh token, nhưng response L79-L86 không gán `RefreshToken`. Nếu frontend auto-login sau register thì thiếu refresh token để refresh session. |
| 22 | Các tài nguyên DB context, transaction, stream, connection có được giải phóng đúng cách không? | PASS | 0 | |
| 23 | Các truy vấn dữ liệu có hợp lý ở mức chấp nhận được không? | PASS | 0 | |
| 24 | Kết quả các thao tác ghi dữ liệu quan trọng có được kiểm tra đầy đủ không? | PASS | 0 | |
| 25 | Các thao tác ghi liên quan đến User + Role + Student/Teacher/Admin có transaction đảm bảo atomic không? | FAIL | 2 | (1) `RegisterAsync()` L49-L71 tạo User, add role, tạo Student, update refresh token qua nhiều thao tác nhưng không có transaction. (2) `RegisterAdminAsync()` L176-L189 tương tự với User + role Admin + Admin record. |
| 26 | Trong các biểu thức số học, thứ tự ưu tiên toán tử và dấu ngoặc có rõ ràng không? | PASS | 0 | |
| 27 | Các yêu cầu về thời gian phản hồi của module có được đáp ứng không? | PASS | 0 | |
| 28 | Có vấn đề hiệu năng rõ ràng cần sửa ngay không? | PASS | 0 | |
| 29 | Dữ liệu đầu vào từ API đã được validate đầy đủ theo nghiệp vụ UserManagement chưa? | FAIL | 3 | (1) `RefreshTokenDto.cs` L7 không có `[Required]`. (2) `TeacherRegisterDto.cs` L7-L8 không có giới hạn độ dài cho `EmployeeCode`, `Instruction`. (3) Query `page`, `pageSize`, `status` trong `AdminController.GetCourseByStatusAsync()` L18-L20 chưa validate miền giá trị. |
| 30 | Các thông báo lỗi trả về có đủ rõ ràng cho client không? | PASS | 0 | |
| 31 | Các điều kiện lỗi chính đã được bẫy và trả HTTP status phù hợp chưa? | FAIL | 3 | (1) `AuthController.Register()` không catch exception từ service. (2) `AuthController.Login()` không catch exception từ service. (3) `AdminController.AdminApproveCourseAsync()` và `AdminRejectCourseAsync()` không catch `UnauthorizedAccessException`, có thể trả 500 thay vì 403. |
| 32 | Các API trong module đã được bảo vệ bằng xác thực và phân quyền đúng mức chưa? | FAIL | 3 | (1) `AuthController.RegisterAdmin()` L113-L129 không có `[Authorize(Roles = "Admin")]` hoặc cơ chế bootstrap riêng - bất kỳ ai cũng có thể tạo admin. (2) `AuthController.RegisterTeacher()` L57-L59 chỉ cần user login, không có bước admin duyệt trước khi cấp role Teacher. (3) `AuthController.GetClaims()` L42-L53 trả toàn bộ claims, không nên dùng trong production nếu không có nhu cầu rõ ràng. |
| 33 | JWT claims có nhất quán với frontend/backend và tránh nhầm lẫn không? | FAIL | 2 | (1) Claim `"TeacherId"` sinh ở `AuthService` L336 nhưng controller đọc `"teacherId"` L25. (2) `RefreshTokenAsync()` L303 có thể sinh claim `StudentId` bằng chuỗi rỗng nếu user không có Student record. |
| 34 | Refresh token có được thiết kế an toàn: hash, hết hạn, rotate và revoke khi cần không? | FAIL | 2 | (1) Refresh token chỉ lưu một `RefreshTokenHash` trên `User` L33-L34, admin-login và student-login có thể ghi đè nhau. (2) Không có endpoint logout/revoke refresh token. |
| 35 | Chính sách mật khẩu, lockout và bảo vệ brute-force đã được cấu hình rõ ràng chưa? | FAIL | 2 | (1) `Program.cs` chỉ gọi `AddIdentity<User, IdentityRole>()` mà không cấu hình rõ `PasswordOptions`, `LockoutOptions`, `SignInOptions`. (2) `LoginAsync()` L97 và `AdminLoginAsync()` L201 dùng `CheckPasswordAsync` trực tiếp, không tăng AccessFailedCount/lockout khi sai mật khẩu. |
| 36 | Cấu hình JWT và bí mật hệ thống có được validate an toàn không? | FAIL | 1 | `AuthService` L240 và L345 đọc `_configuration["Jwt:Key"]` và truyền thẳng vào `Encoding.UTF8.GetBytes`; nếu thiếu config sẽ lỗi runtime và không có thông báo cấu hình rõ ràng. |
| 37 | Việc gán role Student/Teacher/Admin có đúng quy trình nghiệp vụ không? | FAIL | 2 | (1) `RegisterTeacherAsync()` L146-L151 tạo Teacher record và cấp role Teacher ngay khi user tự đăng ký, không có trạng thái chờ duyệt. (2) `RegisterAdminAsync()` L113-L129 mở công khai nên role Admin có thể bị tạo trái phép. |
| 38 | Luồng admin review/approve course có kiểm tra trạng thái hợp lệ và tránh tranh chấp không? | FAIL | 3 | (1) `AdminApproveCourseAsync()` L154 đặt AdminReviewCourse status `Reviewed` trong khi Course status `Published`, dễ gây nhầm trạng thái. (2) `AdminRejectCourseAsync()` L180 cũng đặt review status `Reviewed` khi reject, chỉ phân biệt bằng Reason. (3) `AdminReviewCourseAsync()` L210-L229 check tồn tại rồi insert, có race condition nếu hai admin nhận review cùng lúc. |
| 39 | Quyền truy cập và trạng thái hợp lệ của user có được kiểm tra trước khi xử lý nghiệp vụ không? | FAIL | 2 | (1) `AuthService.LoginAsync()` không kiểm tra `User.Status`, `DeleteAt`, lockout hay email confirmed. (2) `AdminLoginAsync()` tương tự. |
| 40 | Các luồng xử lý lỗi có ghi log đủ để truy vết lỗi quan trọng không? | FAIL | 2 | `AuthController`/`AuthService` và `AdminController`/`AdminService` không inject `ILogger`, nên lỗi đăng nhập, tạo admin, cấp role, approve/reject course khó truy vết trên production. |
| 41 | Các tác vụ bất đồng bộ có được thiết kế đúng cách, không block thread không? | FAIL | 1 | `AuthService.GenerateAdminJwtToken()` L226 và `GenerateJwtToken()` L327 dùng `.Result` trên Task trong ứng dụng ASP.NET Core async. |
| 42 | Module có response format đủ nhất quán để frontend xử lý không? | PASS | 0 | |
| 43 | Việc xoá/khoá tài khoản và trạng thái người dùng có được xử lý khi xác thực không? | FAIL | 2 | (1) `User.cs` có `Status` L27 và `DeleteAt` L31 nhưng login/register teacher/admin không dùng để chặn tài khoản bị khoá/xoá. (2) Không có cơ chế revoke token khi user bị khoá. |
| 44 | Ràng buộc toàn vẹn dữ liệu cho UserManagement có đáp ứng mức cơ bản không? | PASS | 0 | |
| 45 | Module UserManagement có unit test/automation test bao phủ các luồng quan trọng không? | FAIL | 1 | Trong `backend/project.Tests/Modules` chưa thấy test riêng cho `AuthService`, `AdminService`, `TeacherService`. Các luồng nhạy cảm như register admin, refresh token, login sai mật khẩu, claim TeacherId, approve/reject course chưa được bao phủ. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 24 | 21 | 0 |

| Tổng số Error phát hiện | 41 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: **PHÊ DUYỆT MỘT PHẦN**

Module có cấu trúc phân tầng rõ, đã dùng ASP.NET Identity, JWT, role-based authorization và refresh token được hash. Tuy nhiên cần sửa các lỗi chính trước khi phê duyệt toàn bộ: endpoint tạo admin đang mở công khai, claim `TeacherId` không nhất quán, refresh token chưa có revoke/logout, và các thao tác tạo user + role chưa có transaction.

