# CHECKLIST CODE REVIEW — MODULE COURSE
### Dự án: E-Learning Platform | Ngôn ngữ: C# (.NET 8) | Mô hình: Repository/Service Pattern

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | Course (CourseController, CourseService, EnrollmentCourseService, CourseReviewService, LessonService, CategoryService, UpdateRequestController) |
| **Người review** | _(điền tên)_ |
| **Ngày review** | _(điền ngày)_ |
| **Commit / Branch** | _(điền commit hash hoặc branch)_ |

---

## HƯỚNG DẪN SỬ DỤNG

| Kết quả | Ý nghĩa |
|---|---|
| **PASS** | Không phát hiện lỗi |
| **FAIL** | Có ≥ 1 lỗi — ghi rõ file, dòng, mô tả lỗi vào cột Ghi chú |
| **NA** | Không áp dụng hoặc không thể trả lời — ghi lý do |

---

## BẢNG CHECKLIST

| STT | Nội dung câu hỏi | PASS/FAIL/NA | Số Error | Ghi chú |
|---|---|---|---|---|
| 1 | Các quy tắc lập trình quy định trong kế hoạch dự án đã được tuân thủ chưa? | FAIL | 4 | (1) `CourseService.AddFullCourseAsync()` (L388, L406): tham số tên `userId` nhưng dùng như `teacherId` — không nhất quán. (2) `EnrollmentCourseService` (L161): biến đặt tên `studentIdGuild` (sai, phải là `studentIdGuid`). (3) `CourseService` (L314): biến local `InstructorStatisticDTO` dùng PascalCase thay vì camelCase. (4) `EnrollmentController` (L33): `ennrollments` (thừa chữ 'n'). |
| 2 | Các tài liệu hướng dẫn trực tiếp trong mã nguồn (comment, XML comment) có đầy đủ và cập nhật không? | FAIL | 8 | Không có XML comment (`///`) cho bất kỳ public method nào trong `CourseService`, `EnrollmentCourseService`, `CourseReviewService`. Comment nội bộ `// Update Course Content and Lessons logic goes here` (CourseService L201) là comment placeholder còn sót lại, gây nhầm lẫn về trạng thái hoàn thiện của code. |
| 3 | Các quy ước đặt tên (namespace, class, method, biến) có tuân thủ kế hoạch quản lý cấu hình không? | FAIL | 2 | (1) `EnrollmentCourseService.GetRecentEnrollmentsOfTeacherAsync()` (L343): gọi `_studentRepository.IsStudentExistAsync(teacherId)` thay vì `_teacherRepository.IsTeacherExistsAsync()` — sai repository, sai tên method. (2) `UpdateRequestController` (L3): route đặt là `"api/requestUpdateds"` — sai ngữ pháp, nên là `"api/request-updates"`. |
| 4 | Mã nguồn đã được định dạng đúng cách và thống nhất trong toàn bộ module chưa? | FAIL | 2 | (1) `CourseService.GetFullCourseDataForEditAsync()` (L495): `.ToList().OrderBy(l => l.Order).ToList()` — gọi `ToList()` thừa trước `OrderBy()`, phải đổi thành `.OrderBy(l => l.Order).ToList()`. (2) `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` (L184-192): khối comment-out dài chiếm 9 dòng, làm giảm khả năng đọc code. |
| 5 | Các chương trình con dùng chung (method/service dùng lại) đã được viết để tránh lặp logic ở nhiều nơi chưa? | FAIL | 1 | Logic lấy `CourseInformationDTO` từ entity `Course` (mapping các field: Id, Title, Price, ThumbnailUrl, CategoryName, TeacherName...) được lặp lại y hệt trong `GetAllCoursesAsync()`, `SearchItemsAsync()`, `GetCoursesByTeacherIdAsync()`. Nên tách thành private method `ToCourseInformationDTO(Course c)`. |
| 6 | Có mã nguồn thừa, mã không sử dụng hoặc logic đã lỗi thời không? | FAIL | 3 | (1) `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` (L184-192): khối code ownership check bị comment-out — logic bảo mật bị vô hiệu hóa mà không có giải thích. (2) `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` (L163-165, L168-169): các biến `lessonGuild` và `examGuild` được khai báo nhưng không dùng đến. (3) `UpdateRequestController.cs` (L1-14): Controller chỉ có 14 dòng, hoàn toàn trống — không có endpoint nào được triển khai. |
| 7 | Có method, API endpoint hoặc service nào được khai báo nhưng không được gọi tới không? | FAIL | 1 | `UpdateRequestController` được đăng ký DI và route nhưng không có endpoint nào — controller rỗng hoàn toàn. Người dùng gọi `GET/POST api/requestUpdateds/*` sẽ nhận 404 mà không có thông báo rõ ràng. |
| 8 | Các đối tượng có khả năng null đã được kiểm tra và xử lý phù hợp chưa? | FAIL | 3 | (1) `CourseService.GetAllCoursesAsync()` (L57-59): `c.Category.Name` và `c.Teacher.User.FullName` không dùng `?.` — nếu Category hoặc Teacher null sẽ ném NullReferenceException, trong khi `SearchItemsAsync()` (L82-84) dùng `?.` đúng cách. (2) `CourseService.GetCourseByIdAsync()` (L120-122): tương tự, `course.Category.Name` và `course.Teacher.User.FullName` thiếu null-safe. (3) `CourseController` nhiều endpoint lấy `User.FindFirst(...)?.Value` nhưng truyền thẳng vào Service mà không kiểm tra null trước. |
| 9 | Việc truy cập object, collection hoặc kết quả truy vấn có tránh được lỗi null reference không? | PASS | 0 | |
| 10 | Tất cả các chỉ số truy cập collection/list có nằm trong phạm vi cho phép không? | PASS | 0 | |
| 11 | Các collection/list có được khởi tạo đầy đủ trước khi sử dụng không? | PASS | 0 | `lessonsToUpdate`, `lessonsToAdd`, `lessons` đều được khởi tạo trước vòng lặp. |
| 12 | Tất cả các điều kiện rẽ nhánh (if/switch) có chính xác và đúng nghiệp vụ không? | FAIL | 1 | `EnrollmentCourseService.GetEnrollmentInCourseAsync()` (L55-62): lỗi logic nghiêm trọng. Điều kiện `if (course.Teacher.User.Id != userId)` → throw "not teacher"; `else if (!admin)` → throw "not admin". Logic này sai: nếu là teacher thì vào if và throw — không ai được phép truy cập. Đúng phải là: nếu KHÔNG phải teacher VÀ KHÔNG phải admin thì mới throw. |
| 13 | Tất cả các vòng lặp có điều kiện kết thúc rõ ràng và an toàn không? | PASS | 0 | |
| 14 | Điều kiện để kết thúc vòng lặp có thực tế và tránh rủi ro lặp vô hạn không? | PASS | 0 | |
| 15 | Các phép chia, phép tính số học có được kiểm tra lỗi chia cho 0 hoặc dữ liệu không hợp lệ chưa? | PASS | 0 | `CalculateProgressAsync()` (L249, L253): đã kiểm tra `totalLessons == 0` và `totalExams == 0` trước khi chia. `SearchItemsAsync()` (L93): `Math.Ceiling(totalCount / pageSize)` — pageSize luôn > 0 do có default value. |
| 16 | Có câu lệnh nào nằm trong vòng lặp mà có thể đưa ra ngoài để tối ưu không? | PASS | 0 | Các vòng lặp foreach trong `UpdateFullCourseAsync()` và `AddFullCourseAsync()` chỉ build object, không gọi DB trong vòng lặp. |
| 17 | Có phần nào trong mã nguồn mà luồng thực thi không bao giờ chạm tới (dead code) không? | FAIL | 1 | `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` (L163-169): `var lessonGuild = GuidHelper.ParseOrThrow(...)` và `var examGuild = GuidHelper.ParseOrThrow(...)` — kết quả parse được gán vào biến local nhưng biến đó không được dùng ở bất kỳ đâu sau đó → dead code thực sự. |
| 18 | Các câu lệnh if có bị lồng nhau quá sâu, gây khó đọc và khó bảo trì không? | FAIL | 1 | `EnrollmentCourseService.RequestCancelEnrollmentAsync()` (L258-336): phương thức dài 78 dòng với nhiều if lồng nhau (kiểm tra course, enrollment, user, status, price, ngày, progress) — độ phức tạp cyclomatic cao, khó test và bảo trì. Nên tách thành các private method validate riêng. |
| 19 | Các tham số truyền giữa các tầng (Controller – Service – Repository) có khớp nhau không? | FAIL | 1 | `CourseService.AddFullCourseAsync()` (L386): nhận tham số `string userId` nhưng bên trong xử lý như teacherId (L388: `IsTeacherExistsAsync(userId)`, L406: `TeacherId = userId`). Controller gọi với `teacherId` — tên tham số ở Controller và Service không thống nhất. |
| 20 | Có biến, tham số hoặc dependency nào được khai báo nhưng không sử dụng không? | FAIL | 2 | (1) `EnrollmentCourseService` (L161): `var studentIdGuild = GuidHelper.ParseOrThrow(...)` — kết quả không dùng đến. (2) `EnrollmentCourseService.GetRecentEnrollmentsOfTeacherAsync()` (L339-367): inject và dùng `_studentRepository` để check teacherId tồn tại — sai repository, nên dùng `_teacherRepository`. |
| 21 | Các đối tượng và dữ liệu có được khởi tạo đúng cách trước khi sử dụng không? | PASS | 0 | |
| 22 | Các tài nguyên (DB context, stream, connection) có được giải phóng tại tất cả các điểm thoát không? | PASS | 0 | Transaction được quản lý đúng bằng `using var transaction` trong `UpdateFullCourseAsync()` và `AddFullCourseAsync()`. |
| 23 | Các truy vấn dữ liệu có được thiết kế hợp lý, tránh truy vấn dư thừa và tận dụng index khi cần không? | FAIL | 1 | `CourseService.GetCoursesByTeacherIdAsync()` (L317-321): tính thống kê `TotalPublishedCourses`, `TotalDraftCourses`... bằng `.Count()` và `.Sum()` trên danh sách `courses` — đây là danh sách của **trang hiện tại** (paged), không phải toàn bộ khóa học của giáo viên, dẫn đến thống kê sai khi có nhiều trang. |
| 24 | Trạng thái lỗi có được kiểm tra và xử lý sau mỗi thao tác truy cập dữ liệu không? | PASS | 0 | |
| 25 | Việc khóa hoặc transaction có được thực hiện trước khi cập nhật dữ liệu khi cần thiết không? | PASS | 0 | `AddFullCourseAsync()` và `UpdateFullCourseAsync()` dùng transaction bao toàn bộ thao tác ghi (Course + CourseContent + Lessons). |
| 26 | Trong các biểu thức số học, việc làm tròn và thứ tự ưu tiên toán tử đã được xem xét đầy đủ chưa? | FAIL | 2 | (1) `CourseReviewService.UpdateCourseReviewAsync()` (L100): `... + courseReviewUpdateDTO.Rating ?? review.Rating) / course.ReviewCount` — toán tử `??` có độ ưu tiên thấp hơn `+`, nên thực tế là `(... + courseReviewUpdateDTO.Rating) ?? review.Rating` — sai logic hoàn toàn, không tính đúng rating trung bình. (2) `EnrollmentCourseService.CalculateProgressAsync()` (L255): dùng `0.7f` và `0.3f` (float literal) kết hợp với biến `double` — nên dùng `0.7` và `0.3` (double) để tránh mất độ chính xác. |
| 27 | Các yêu cầu về thời gian phản hồi của module có được đáp ứng không? | PASS | 0 | |
| 28 | Có phương án nào tốt hơn để cải thiện hiệu năng và thời gian phản hồi không? | FAIL | 1 | `CourseService.GetCoursesByTeacherIdAsync()` (L317-321): dùng LINQ `.Count()` và `.Sum()` trên danh sách in-memory (chỉ là dữ liệu trang hiện tại). Nên tính thống kê bằng một câu truy vấn DB riêng (GROUP BY status) thay vì đếm client-side từ data đã page. |
| 29 | Các kiểm tra dữ liệu rỗng, dữ liệu không tồn tại và lỗi IO đã được thực hiện chưa? | PASS | 0 | |
| 30 | Các thông báo lỗi trả về có rõ ràng, đầy đủ và nhất quán không? | FAIL | 2 | (1) `CourseService.SearchItemsAsync()` (L98): `throw new Exception("Error when retriev course: ", ex)` — lỗi chính tả "retriev" (thiếu 'e'), và wrap exception mất context gốc (nên dùng `throw;` hoặc không wrap). (2) `CourseService.GetCoursesByTeacherIdAsync()` (L336) và `GetEnrolledCoursesByStudentIdAsync()` (L381): cùng pattern tương tự — message sai ngữ pháp và mất stack trace gốc. |
| 31 | Tất cả các điều kiện lỗi đã được bẫy và xử lý phù hợp chưa? | FAIL | 1 | `EnrollmentController.GetEnrollmentInCourseAsync()` (L26-34): chỉ catch `KeyNotFoundException` và `Exception` chung — không catch riêng `UnauthorizedAccessException`, khiến lỗi phân quyền trả về 500 thay vì 401/403. |
| 32 | Trong các biểu thức số học, thứ tự xử lý, dấu ngoặc và khả năng đọc hiểu có rõ ràng không? | FAIL | 1 | `CourseReviewService.AddCourseReviewAsync()` (L65): `course.AverageRating = ((course.AverageRating * (course.ReviewCount - 1)) + courseReviewCreateDTO.Rating) / course.ReviewCount` — công thức đúng về nghiệp vụ nhưng có thể gây lỗi nếu `ReviewCount` vừa được tăng lên trước đó trên cùng dòng L64. Thứ tự thực thi (ReviewCount++ rồi mới tính) cần được làm rõ bằng comment. |
| 33 | Trong các biểu thức quan hệ, các phép so sánh có đúng kiểu dữ liệu và đúng mục đích sử dụng không? | FAIL | 1 | `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` (L237): `enrollment.Status != "Cancelled"` — so sánh string với "Cancelled" (PascalCase) nhưng status được set là `"active"` (lowercase) và `"Completed"` — không nhất quán case. Cần dùng `StringComparison.OrdinalIgnoreCase` hoặc chuẩn hóa về một kiểu duy nhất. |
| 34 | Trong các biểu thức logic, điều kiện có rõ ràng, dễ đọc và tránh kết hợp quá phức tạp không? | PASS | 0 | |
| 35 | Trong các thao tác với dữ liệu, tài nguyên có được mở đúng thời điểm và đóng sau khi hoàn tất không? | PASS | 0 | |
| 36 | Trong việc khai báo biến và cấu hình, có tránh lạm dụng global/static và tránh hard code không? | FAIL | 4 | (1) `EnrollmentCourseService` (L5): `private const double passScore = 70` — magic number, nên đưa vào config/appsettings. (2) `EnrollmentCourseService` (L112): `CertificateUrl = "No Certificate"` — hardcode string. (3) `EnrollmentCourseService` (L333): `enrollment.Status = "Peding Refund"` — lỗi typo "Peding" (thiếu 'n') và hardcode string status. (4) `EnrollmentCourseService` (L302): `difference.TotalDays > 2` — magic number 2, nên là hằng số có tên rõ ràng. |
| 37 | Các API trong module đã được bảo vệ bằng cơ chế xác thực và phân quyền chưa? | FAIL | 1 | `CourseController.GetAllCourses()` (L22) không có `[Authorize]` — mọi user ẩn danh đều lấy được toàn bộ danh sách khóa học kể cả draft/rejected. Tùy nghiệp vụ nhưng cần xem xét. `IsEnrolledInCourseAsync` cho phép role Teacher nhưng trong service lại chỉ lấy `studentId` từ claim — Teacher sẽ nhận studentId = null và gọi xuống Repository với null. |
| 38 | Dữ liệu đầu vào từ API đã được validate và sanitize đầy đủ chưa? | FAIL | 2 | (1) `CourseReviewService.AddCourseReviewAsync()`: không validate `Rating` nằm trong khoảng hợp lệ (1–5). Nếu Rating = 0 hoặc âm, DB vẫn nhận và làm sai AverageRating. (2) `EnrollmentCourseService.RequestCancelEnrollmentAsync()`: không validate `dto.ReasonRequest` có null/empty không trước khi lưu vào DB. |
| 39 | Các thông tin nhạy cảm có được cô lập trong cấu hình an toàn thay vì hard code không? | PASS | 0 | |
| 40 | Các luồng xử lý lỗi có ghi log đầy đủ để phục vụ truy vết và debug không? | FAIL | 8 | Nghiêm trọng: Toàn bộ module Course (CourseController, EnrollmentController, CourseReviewController, LessonController) không inject `ILogger` và không có bất kỳ lệnh log nào trong các khối `catch`. Không thể truy vết lỗi production. |
| 41 | Các tác vụ nặng hoặc gọi dịch vụ bên ngoài có được thiết kế bất đồng bộ khi phù hợp không? | PASS | 0 | Tất cả method đều dùng `async/await` đúng cách. |
| 42 | Các thao tác API quan trọng (tạo, cập nhật, xóa) có xử lý trạng thái chờ và lỗi đầy đủ không? | FAIL | 1 | `EnrollmentCourseService.RequestCancelEnrollmentAsync()` (L331): `CreatedAt = DateTime.Now` — dùng local time thay vì `DateTime.UtcNow` như tất cả các nơi khác trong codebase. Gây sai lệch timezone khi so sánh với dữ liệu khác. |
| 43 | Module có cơ chế xử lý lỗi thống nhất và phản hồi thân thiện cho client không? | FAIL | 1 | Nhiều Service ném `throw new Exception(...)` thay vì dùng đúng loại exception có ý nghĩa: `CourseReviewService` (L37, L42, L47, L76, L111): dùng `Exception` thay vì `KeyNotFoundException` hay `InvalidOperationException` — Controller không thể bắt riêng để trả đúng HTTP status code. |
| 44 | Việc xóa dữ liệu có được thiết kế theo hướng bảo toàn dữ liệu (soft delete) khi cần không? | FAIL | 1 | Không có endpoint hay service method nào để xóa khóa học (DeleteCourse). Chưa rõ là chủ ý hay thiếu sót. `CourseReview` khi update tạo bản ghi mới và set `IsNewest = false` cho cũ — đây là pattern soft-delete tốt, nhưng không nhất quán với các entity khác. |
| 45 | Quyền truy cập và trạng thái hợp lệ của người dùng có được kiểm tra trước khi xử lý nghiệp vụ không? | FAIL | 1 | `EnrollmentCourseService.UpdateProgressEnrollmentAsync()` (L184-192): khối code kiểm tra ownership (`enrollment.Student?.User.Id != userId`) bị comment-out hoàn toàn — bất kỳ student đã đăng nhập nào cũng có thể cập nhật tiến độ của student khác nếu biết `courseId`. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 16 | 29 | 0 |

| Tổng số Error phát hiện | 58 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: ❌ **TỪ CHỐI** (Reject — Required Changes Before Re-review)

---

### Đánh giá chi tiết

**Ưu điểm:**
- Kiến trúc phân tầng Controller → Service → Repository được tuân thủ, DI qua interface nhất quán.
- Sử dụng transaction đúng cách (`using var transaction`) trong các thao tác ghi phức hợp (AddFullCourse, UpdateFullCourse).
- Logic kiểm tra điều kiện hoàn trả (refund) đầy đủ nghiệp vụ: kiểm tra giá, tiến độ, số ngày đăng ký.
- Pattern cập nhật review theo kiểu "tạo bản ghi mới + IsNewest" là thiết kế tốt, bảo toàn lịch sử.

**Vấn đề nghiêm trọng:**
1. **[Logic Bug]** `GetEnrollmentInCourseAsync()` (L55-62): Điều kiện phân quyền bị đảo ngược — teacher bị chặn, không ai truy cập được endpoint.
2. **[Security]** `UpdateProgressEnrollmentAsync()`: ownership check bị comment-out — mọi student có thể sửa tiến độ của người khác.
3. **[Data Bug]** Thống kê trong `GetCoursesByTeacherIdAsync()` tính từ dữ liệu paged → số liệu sai khi có nhiều trang.
4. **[Data Bug]** `CourseReviewService.UpdateCourseReviewAsync()` (L100): lỗi precedence toán tử `??` làm công thức tính rating sai hoàn toàn.

**Rủi ro kỹ thuật:**
5. Typo `"Peding Refund"` ghi vào DB — trạng thái enrollment bị sai, không match với bất kỳ logic kiểm tra nào khác.
6. Toàn bộ module không có logging — không thể debug hay audit hành vi trong production.
7. Nhiều chỗ dùng `throw new Exception(...)` thay vì typed exception — Controller không thể map đúng HTTP status code.
