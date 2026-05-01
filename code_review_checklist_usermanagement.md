# CHECKLIST CODE REVIEW - MODULE USERMANAGEMENT
### Dự án: E-Learning Platform | Ngôn ngữ: C# (.NET 8) | Phạm vi: Authentication, Authorization, User/Role, Admin/Teacher

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | UserManagement BE (`AuthController`, `AuthService`, `AdminController`, `AdminService`, `TeacherController`, `TeacherService`, các repository và DTO liên quan tới User/Student/Teacher/Admin) |
| **Người review** | _(điền tên)_ |
| **Ngày review** | _(điền ngày)_ |
| **Commit / Branch** | _(điền commit hash hoặc branch)_ |

---

## HƯỚNG DẪN SỬ DỤNG

| Kết quả | Ý nghĩa |
|---|---|
| **PASS** | Không phát hiện lỗi |
| **FAIL** | Có >= 1 lỗi - ghi rõ file, dòng, mô tả lỗi vào cột Ghi chú |
| **NA** | Không áp dụng hoặc không thể trả lời - ghi rõ lý do |

---

## BẢNG CHECKLIST

| STT | Nội dung câu hỏi | PASS/FAIL/NA | Số Error | Ghi chú |
|---|---|---|---|---|
| 1 | Các quy tắc lập trình quy định trong kế hoạch dự án đã được tuân thủ chưa? | FAIL | 4 | (1) Nhiều file trong `UserManagement` không khai báo namespace, ví dụ `AdminController.cs` L5, `TeacherController.cs` L4, `AdminService.cs` L5, `TeacherService.cs` L2. (2) `Teacher.cs` L16 đặt property `instruction` bằng camelCase trong model C#, không nhất quán với PascalCase. (3) `testDto.cs` tồn tại class tên `testDto`, sai convention. (4) `RegisterDTO.cs` L5 bị lệch indent so với các property còn lại. |
| 2 | Các tài liệu hướng dẫn trực tiếp trong mã nguồn (comment, XML comment) có đầy đủ, đúng và cập nhật không? | FAIL | 3 | (1) Các public API quan trọng trong `AuthController`, `AuthService`, `AdminService` không có XML comment. (2) `AuthService.cs` L149 có comment nói rõ "không xoá role Student" nhưng không giải thích rủi ro người dùng có đồng thời 2 role. (3) `AdminController.cs` L115-L128 còn khối endpoint refund bị comment-out dài, không rõ còn dùng hay đã bỏ. |
| 3 | Các quy ước đặt tên namespace, class, method, biến và property có nhất quán không? | FAIL | 4 | (1) `TeacherController.cs` L25 đọc claim `"teacherId"` trong khi `AuthService.GenerateJwtToken()` L336 sinh claim `"TeacherId"` - sai hoa/thường. (2) `AdminService.AdminRejectCourseAsync()` L158 đặt tham số `RejectReason` bằng PascalCase thay vì camelCase. (3) `User.cs` L31 đặt `DeleteAt`, nên là `DeletedAt` nếu biểu diễn thời điểm xoá. (4) Các DTO có namespace không nhất quán: `RegisterDto`, `LoginDto`, `LoginAdminDTO` không khai báo namespace trong khi `RefreshTokenDto`, `AuthResponseDto`, `AdminRegisterDTO` có namespace. |
| 4 | Mã nguồn đã được định dạng đúng cách và thống nhất trong toàn bộ module chưa? | FAIL | 2 | (1) `RegisterDTO.cs` L5 thụt lề sai. (2) `TeacherRegisterDto.cs` L7 thụt lề lệch, comment inline không thống nhất với style còn lại. |
| 5 | Các chương trình con dùng chung đã được viết để tránh lặp logic ở nhiều nơi chưa? | FAIL | 1 | `AuthService.GenerateAdminJwtToken()` L223-L252 và `GenerateJwtToken()` L324-L357 lặp phần lớn logic tạo JWT, lấy roles, gán role claims và ký token. Nên tách helper chung để giảm lỗi lệch claim/expiry. |
| 6 | Có mã nguồn thừa, mã không sử dụng hoặc logic đã lỗi thời không? | FAIL | 3 | (1) `AuthController.Test()` L35-L40 là endpoint test chỉ cho Teacher, không phục vụ nghiệp vụ. (2) `AdminController.cs` L115-L128 còn endpoint refund bị comment-out. (3) `testDto.cs` là DTO test còn sót trong module production. |
| 7 | Có method, API endpoint, DTO hoặc model nào được khai báo nhưng không được expose/gọi tới không? | FAIL | 2 | (1) `IAdminService.GetPendingRefundRequestsAsync()` L14 và `AdminService.GetPendingRefundRequestsAsync()` L184-L203 tồn tại nhưng endpoint trong `AdminController` đã bị comment-out. (2) `AdminActionLog.cs` tồn tại trong Models nhưng không thấy DbSet/Repository/Service sử dụng trong module. |
| 8 | Các đối tượng có khả năng null đã được kiểm tra và xử lý phù hợp chưa? | FAIL | 3 | (1) `AuthController.RefreshToken()` L89 dùng `dto.RefreshToken` nhưng không kiểm tra `dto` hoặc token null/empty. (2) `AuthController.RefreshAdminToken()` L104 tương tự. (3) `AdminService.GetFullCourseByIdAsync()` L119-L120 truy cập `course.Content.Id` và `course.Content.Lessons` trực tiếp, có thể gây `NullReferenceException` nếu course chưa có content. |
| 9 | Việc truy cập navigation property và kết quả truy vấn có tránh được lỗi null reference không? | FAIL | 2 | (1) `AdminService.GetPendingRefundRequestsAsync()` L191-L195 truy cập `rrc.Student.User`, `rrc.Admin.AdminId`, `rrc.Enrollment.Course` trực tiếp. (2) `AdminRepository.GetAdminNameAsync()` L119 trả `admin.User.FullName` trực tiếp, phụ thuộc navigation User luôn có dữ liệu. |
| 10 | Tất cả chỉ số truy cập collection/list có nằm trong phạm vi cho phép không? | PASS | 0 | |
| 11 | Các collection/list trong entity User/Student/Teacher/Admin có được khởi tạo trước khi sử dụng không? | PASS | 0 | |
| 12 | Tất cả điều kiện rẽ nhánh có chính xác và đúng nghiệp vụ UserManagement không? | FAIL | 3 | (1) `TeacherController.GetOverview()` L25 dùng sai tên claim nên teacher hợp lệ vẫn có thể bị trả 401. (2) `AuthService.LoginAsync()` L102 bắt buộc mọi user login thường phải có Student record, làm admin-only user không thể login bằng endpoint login chung. (3) `AdminService.AdminRejectCourseAsync()` L166 thông báo "approve this course" trong nhánh reject, sai ngữ cảnh. |
| 13 | Tất cả vòng lặp có điều kiện kết thúc rõ ràng và an toàn không? | PASS | 0 | |
| 14 | Điều kiện để kết thúc vòng lặp có thực tế và tránh rủi ro lặp vô hạn không? | PASS | 0 | |
| 15 | Các phép tính số học, phân trang và giới hạn review có kiểm tra chia cho 0 hoặc đầu vào không hợp lệ chưa? | FAIL | 2 | (1) `AdminService.GetCoursesByAdminAsync()` L72 tính `totalCount / pageSize` nhưng không validate `pageSize > 0`. (2) `AdminRepository.GetCoursesByAdminAsync()` L35 dùng `Skip((page - 1) * pageSize)` nhưng không validate `page >= 1`, có thể sinh Skip âm. |
| 16 | Có câu lệnh nào nằm trong vòng lặp mà có thể đưa ra ngoài để tối ưu không? | PASS | 0 | |
| 17 | Có phần nào trong mã nguồn mà luồng thực thi không bao giờ chạm tới hoặc kết quả không được dùng không? | FAIL | 2 | (1) `AdminService.AdminReviewCourseAsync()` L208 gán `adminName` nhưng không sử dụng. (2) `AdminService.AdminReviewLessonAsync()` L235 gán `adminName` nhưng không sử dụng. |
| 18 | Các câu lệnh if có bị lồng nhau quá sâu, gây khó đọc và khó bảo trì không? | PASS | 0 | |
| 19 | Các tham số truyền giữa Controller - Service - Repository có khớp nhau không? | FAIL | 2 | (1) `TeacherController` lấy claim `"teacherId"` L25 nhưng `AuthService` sinh `"TeacherId"` L336. (2) `IAdminService.AdminRejectCourseAsync()` L11 đặt `[FromBody]` trong interface service, annotation MVC không nên nằm ở tầng service. |
| 20 | Có biến, tham số hoặc dependency nào được khai báo nhưng không sử dụng không? | FAIL | 3 | (1) `TeacherController.cs` L9-L17 inject `_courseService` nhưng không dùng. (2) `AdminService.cs` L11 và L24-L30 inject `_unitOfWork` nhưng không dùng. (3) `UserService.IsUserExistAsync()` L12 parse `userIdGuid` nhưng không dùng kết quả. |
| 21 | Các đối tượng và dữ liệu token có được khởi tạo/trả về đúng cách trước khi client sử dụng không? | FAIL | 1 | `AuthService.RegisterAsync()` L68-L71 tạo và lưu refresh token, nhưng response L79-L86 không gán `RefreshToken`. Nếu frontend kỳ vọng auto-login sau register thì sẽ thiếu refresh token. |
| 22 | Các tài nguyên DB context, transaction, stream, connection có được giải phóng tại mọi điểm thoát không? | PASS | 0 | Module dùng DI-managed `DBContext`, không thấy stream/connection thủ công cần dispose. |
| 23 | Các truy vấn dữ liệu có hợp lý, tránh truy vấn dư thừa và cho kết quả ổn định không? | FAIL | 2 | (1) `AdminRepository.GetCoursesByAdminAsync()` L35 phân trang không có `OrderBy`, kết quả giữa các trang có thể không ổn định. (2) `AdminRepository.GetCoursesByAdminAsync()` L20-L22 Include `AdminReviewCourse.Admin.User` cho list có thể nặng, trong khi có thể project DTO trực tiếp bằng `Select`. |
| 24 | Trạng thái lỗi có được kiểm tra sau mỗi thao tác ghi dữ liệu quan trọng không? | FAIL | 2 | (1) `AuthService.RegisterAsync()` L54 không kiểm tra kết quả `AddToRoleAsync`. (2) `AuthService.RegisterTeacherAsync()` L150-L155 và `RegisterAdminAsync()` L180 không kiểm tra kết quả add role/update user, có thể user được tạo nhưng role/token state sai. |
| 25 | Các thao tác ghi liên quan đến User + Role + Student/Teacher/Admin có transaction đảm bảo atomic không? | FAIL | 3 | (1) `RegisterAsync()` L49-L71 tạo User, add role, tạo Student, update refresh token qua nhiều thao tác nhưng không có transaction. (2) `RegisterAdminAsync()` L176-L189 tương tự với User + role Admin + Admin record. (3) `AdminApproveCourseAsync()` L154-L155 cập nhật admin review và course status bằng 2 repository call riêng, không có transaction. |
| 26 | Trong các biểu thức số học, thứ tự ưu tiên toán tử và dấu ngoặc có rõ ràng không? | PASS | 0 | |
| 27 | Các yêu cầu về thời gian phản hồi của module có được đáp ứng không? | PASS | 0 | Các endpoint chính chỉ gọi DB nội bộ, chưa thấy tác vụ ngoài hệ thống chậm bất thường. |
| 28 | Có phương án nào tốt hơn để cải thiện hiệu năng và thời gian phản hồi không? | FAIL | 1 | `AuthService.GenerateJwtToken()` L327 và `GenerateAdminJwtToken()` L226 gọi `_userManager.GetRolesAsync(user).Result` trong code async, có thể block thread. Nên chuyển helper tạo token thành async và `await GetRolesAsync`. |
| 29 | Dữ liệu đầu vào từ API đã được validate đầy đủ theo nghiệp vụ UserManagement chưa? | FAIL | 5 | (1) `RefreshTokenDto.cs` L7 không có `[Required]`. (2) `TeacherRegisterDto.cs` L7-L8 không có độ dài tối đa/sanitize cho `EmployeeCode`, `Instruction`. (3) `AdminRegisterDTO.cs` L12 không có `[MinLength]` cho password, không nhất quán với `RegisterDto`. (4) `RegisterDto.DateOfBirth` L17 không chặn ngày sinh trong tương lai. (5) Query `page`, `pageSize`, `status` trong `AdminController.GetCourseByStatusAsync()` L18-L20 không validate miền giá trị. |
| 30 | Các thông báo lỗi trả về có rõ ràng, đúng HTTP status và nhất quán ngôn ngữ không? | FAIL | 3 | (1) `AuthService` nhiều nơi throw `Exception` tiếng Anh và tiếng Việt lẫn lộn, ví dụ L95, L130, L165, L199. (2) `AuthController.Register()` L21-L25 và `Login()` L28-L32 không catch riêng Identity/validation exception nên có thể trả 500 thay vì 400/401. (3) `AdminService.AdminRejectCourseAsync()` L166-L177 dùng message approve trong luồng reject. |
| 31 | Tất cả điều kiện lỗi đã được bẫy và xử lý phù hợp chưa? | FAIL | 4 | (1) `AuthController.Register()` không catch exception từ service. (2) `AuthController.Login()` không catch exception từ service. (3) `AdminController.AdminApproveCourseAsync()` L76-L83 không catch `UnauthorizedAccessException`, bị trả 500 thay vì 403. (4) `AdminController.AdminRejectCourseAsync()` L104-L111 tương tự. |
| 32 | Các API trong module đã được bảo vệ bằng xác thực và phân quyền đúng mức chưa? | FAIL | 4 | (1) `AuthController.RegisterAdmin()` L113-L129 không có `[Authorize(Roles = "Admin")]` hoặc cơ chế bootstrap riêng - bất kỳ ai cũng có thể tạo admin. (2) `AuthController.GetClaims()` L42-L53 trả toàn bộ claims, có thể lộ cấu trúc token nội bộ. (3) `AuthController.RegisterTeacher()` L57-L59 chỉ cần user login, không có bước admin duyệt trước khi cấp role Teacher. (4) `AuthController.Test()` L35-L40 là endpoint test production có authorize Teacher nhưng không phục vụ nghiệp vụ. |
| 33 | JWT claims có nhất quán với frontend/backend và tránh dư thừa/nhầm lẫn không? | FAIL | 3 | (1) Claim TeacherId sinh hoa chữ cái đầu L336 nhưng controller đọc `teacherId` L25. (2) `AuthService` thêm cả `ClaimTypes.Role` và custom `"role"` L339-L342, L235-L238, có thể dư thừa và gây nhầm khi decode. (3) `RefreshTokenAsync()` L303 sinh claim `StudentId` bằng chuỗi rỗng nếu không có student, có thể tạo token hợp lệ nhưng thiếu identity nghiệp vụ. |
| 34 | Refresh token có được thiết kế an toàn: hash, hết hạn, rotate, revoke và tách role/session khi cần không? | FAIL | 3 | (1) Refresh token chỉ lưu một `RefreshTokenHash` trên `User` L33-L34, admin-login và student-login ghi đè nhau. (2) Không có endpoint logout/revoke refresh token. (3) Không có cơ chế phát hiện reuse token cũ sau rotation, nếu token cũ bị đánh cắp thì khó truy vết. |
| 35 | Chính sách mật khẩu, lockout và bảo vệ brute-force đã được cấu hình rõ ràng chưa? | FAIL | 2 | (1) `Program.cs` chỉ gọi `AddIdentity<User, IdentityRole>()` mà không cấu hình `PasswordOptions`, `LockoutOptions`, `SignInOptions`. (2) `LoginAsync()` L97 và `AdminLoginAsync()` L201 dùng `CheckPasswordAsync` trực tiếp, không tăng AccessFailedCount/lockout khi sai mật khẩu. |
| 36 | Cấu hình JWT và bí mật hệ thống có được validate an toàn thay vì phụ thuộc giá trị null/hard code không? | FAIL | 2 | (1) `AuthService` L240 và L345 đọc `_configuration["Jwt:Key"]` và truyền thẳng vào `Encoding.UTF8.GetBytes`, nếu thiếu config sẽ lỗi runtime. (2) Không kiểm tra độ dài/toàn vẹn key JWT trước khi ký token. |
| 37 | Việc gán role Student/Teacher/Admin có đúng quy trình nghiệp vụ của nền tảng học trực tuyến không? | FAIL | 2 | (1) `RegisterTeacherAsync()` L146-L151 tạo Teacher record và cấp role Teacher ngay khi user tự đăng ký, không có trạng thái chờ duyệt. (2) `RegisterAdminAsync()` L113-L129 mở công khai nên role Admin có thể bị tạo trái phép. |
| 38 | Luồng admin review/approve course có kiểm tra trạng thái hợp lệ và tránh tranh chấp không? | FAIL | 4 | (1) `AdminApproveCourseAsync()` L134 không kiểm tra `adminId` null/empty trước khi so sánh. (2) L154 đặt AdminReviewCourse status `Reviewed` trong khi Course status `Published`, hai trạng thái dễ gây nhầm. (3) `AdminRejectCourseAsync()` L180 cũng đặt review status `Reviewed` khi reject, chỉ phân biệt bằng Reason. (4) `AdminReviewCourseAsync()` L210-L229 check tồn tại rồi insert không transaction/lock, có race condition giữa hai admin. |
| 39 | Quyền truy cập và trạng thái hợp lệ của user có được kiểm tra trước khi xử lý nghiệp vụ không? | FAIL | 3 | (1) `AuthService.LoginAsync()` không kiểm tra `User.Status`, `DeleteAt`, lockout hay email confirmed. (2) `AdminLoginAsync()` tương tự. (3) `RegisterTeacherAsync()` không kiểm tra user đã bị khoá/xoá trước khi cấp role Teacher. |
| 40 | Các luồng xử lý lỗi có ghi log đầy đủ để phục vụ truy vết và debug không? | FAIL | 6 | Toàn bộ `AuthController`, `AdminController`, `TeacherController`, `AuthService`, `AdminService`, `TeacherService` không inject `ILogger` và không log trong catch. Các lỗi đăng nhập, tạo admin, cấp role, approve/reject course đều khó truy vết trên production. |
| 41 | Các tác vụ bất đồng bộ có được thiết kế đúng cách, không block thread không? | FAIL | 1 | `AuthService.GenerateAdminJwtToken()` L226 và `GenerateJwtToken()` L327 dùng `.Result` trên Task trong ứng dụng ASP.NET Core async. |
| 42 | Module có response format thống nhất với các module khác không? | FAIL | 2 | (1) `AdminController` trả `APIResponse`, trong khi `AuthController` trả DTO trực tiếp hoặc anonymous `{ message }`. (2) Lỗi auth có khi trả `Unauthorized(new { message })`, có khi `BadRequest` string, có khi exception không catch. |
| 43 | Việc xoá/khoá tài khoản và trạng thái người dùng có được thiết kế bảo toàn dữ liệu không? | FAIL | 2 | (1) `User.cs` có `Status` L27 và `DeleteAt` L31 nhưng login/register teacher/admin không dùng để chặn tài khoản bị khoá/xoá. (2) Không có service/endpoint quản lý vô hiệu hoá tài khoản, revoke token khi user bị khoá. |
| 44 | Ràng buộc toàn vẹn dữ liệu cho UserManagement có được thiết kế đầy đủ không? | FAIL | 3 | (1) `Teacher.EmployeeCode` L15 không có unique constraint, có thể trùng mã nhân viên. (2) `User.RefreshTokenHash` L33 không có index, refresh token lookup L291-L294 sẽ quét bảng Users khi dữ liệu lớn. (3) `AdminReviewCourse` tranh chấp dựa vào check app-level L210-L214 và catch `DbUpdateException` L90-L95, cần xác nhận có unique constraint rõ ràng trong DB. |
| 45 | Module UserManagement có unit test/automation test bao phủ các luồng quan trọng không? | FAIL | 1 | Trong `backend/project.Tests/Modules` hiện có test cho Payments và Posts, chưa thấy test riêng cho `AuthService`, `AdminService`, `TeacherService`. Các luồng nhạy cảm như register admin, refresh token, login sai mật khẩu, claim TeacherId, approve/reject course chưa được bao phủ. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 9 | 36 | 0 |

| Tổng số Error phát hiện | 95 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: **TỪ CHỐI** (Reject - Cần sửa các lỗi nghiêm trọng trước khi review lại)

**Ưu điểm:**
- Đã sử dụng ASP.NET Identity, JWT, role-based authorization và hash refresh token thay vì lưu refresh token plaintext.
- Cấu trúc Controller - Service - Repository rõ ràng; các luồng Admin review course/lesson có ý tưởng phân tách trách nhiệm.

**Vấn đề/rủi ro nổi bật:**
- Endpoint `register-admin` đang mở công khai, có rủi ro leo thang đặc quyền nghiêm trọng.
- Claim `TeacherId` bị sai hoa/thường giữa token và `TeacherController`, khiến API teacher overview có thể không hoạt động.
- Refresh token chỉ lưu một bản ghi trên `User`, không có revoke/logout/reuse detection và bị ghi đè giữa admin/student session.
- Các thao tác tạo user + gán role + tạo Student/Teacher/Admin không transaction; có thể tạo dữ liệu nửa chừng.
- Thiếu logging, thiếu test cho Auth/Admin/Teacher, và xử lý exception chưa đúng HTTP status.

