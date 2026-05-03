# Unit Test Cases - Module Courses (Backend Service)

## Thông tin chung

- **Phạm vi kiểm thử:** các file service của module Courses ở backend.
- **Test framework:** xUnit.
- **Thư viện hỗ trợ:** Moq, FluentAssertions, Entity Framework Core InMemory.
- **Vị trí test script:** `backend/project.Tests/Modules/Courses/`.
- **Lệnh chạy test module Courses:**

```powershell
dotnet test backend/project.Tests/project.Tests.csproj --no-restore --filter "FullyQualifiedName~CategoryServiceTests|FullyQualifiedName~CourseServiceTests|FullyQualifiedName~CourseContentServiceTests|FullyQualifiedName~LessonServiceTests|FullyQualifiedName~CourseReviewServiceTests|FullyQualifiedName~EnrollmentCourseServiceTests|FullyQualifiedName~RequestUpdateServiceTests"
```

## Bảng test case

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---:|---|---|---|---|---|---|
| 1 | SERV_CAS_01 | CategoryService.cs | Kiểm tra tạo danh mục khóa học khi dữ liệu đầu vào hợp lệ. | `CategoryCreateDTO { Name = "Lập trình" }` | Gọi repository tạo category đúng tên, không phát sinh exception. | PASS |
| 2 | SERV_CAS_02 | CategoryService.cs | Kiểm tra lấy danh mục theo ID không tồn tại. | `categoryId` không có trong repository. | Ném `KeyNotFoundException`, không trả về dữ liệu sai. | PASS |
| 3 | SERV_COS_01 | CourseService.cs | Kiểm tra tạo khóa học nháp khi teacher tồn tại. | `teacherId` hợp lệ, `CourseCreateDTO` hợp lệ. | Tạo course với `TeacherId`, `Title`, `Status = "draft"` đúng yêu cầu. | PASS |
| 4 | SERV_COS_02 | CourseService.cs | Kiểm tra chặn tạo khóa học khi teacher không tồn tại. | `teacherId` không tồn tại, `CourseCreateDTO` hợp lệ. | Ném `KeyNotFoundException`, không gọi hàm tạo course. | PASS |
| 5 | SERV_COS_03 | CourseService.cs | Kiểm tra cập nhật thông tin khóa học nháp thuộc đúng teacher. | `teacherId` sở hữu course, course đang `draft`, `CourseUpdateDTO` có dữ liệu mới. | Course được cập nhật title, description, category, price, discount, thumbnail. | PASS |
| 6 | SERV_COS_04 | CourseService.cs | Kiểm tra chặn cập nhật course không thuộc teacher hiện tại. | `teacherId` khác `TeacherId` của course. | Ném `UnauthorizedAccessException`, không lưu cập nhật. | PASS |
| 7 | SERV_COS_05 | CourseService.cs | Kiểm tra tìm kiếm khóa học có phân trang. | `keyword = "ASP"`, `categoryId`, `page = 1`, `pageSize = 10`. | Trả về `TotalCount = 1`, `TotalPages = 1`, danh sách có course phù hợp. | PASS |
| 8 | SERV_COS_06 | CourseService.cs | CheckDB: kiểm tra tạo đầy đủ course, course content và lessons; sau test rollback dữ liệu. | `FullCourseCreateDTO` gồm course, content và 2 lesson; DB InMemory có sẵn teacher, user, category. | DB có 1 course `draft`, 1 content, 2 lesson đúng thứ tự; cuối test xóa dữ liệu và DB trở về rỗng. | PASS |
| 9 | SERV_CCS_01 | CourseContentService.cs | Kiểm tra thêm nội dung khóa học khi course đang nháp và thuộc teacher. | `teacherId` hợp lệ, `courseId` thuộc teacher, `CourseContentCreateDTO` hợp lệ. | Tạo course content đúng `CourseId`, `Title`, `Description`, `Introduce`. | PASS |
| 10 | SERV_CCS_02 | CourseContentService.cs | Kiểm tra chặn thêm content khi course không ở trạng thái nháp. | Course có `Status = "published"`. | Ném `InvalidOperationException`, không tạo content. | PASS |
| 11 | SERV_CCS_03 | CourseContentService.cs | Kiểm tra cập nhật nội dung khóa học hợp lệ. | `teacherId` sở hữu course content, `CourseContentUpdateDTO` có dữ liệu mới. | Content được cập nhật title, description và introduce. | PASS |
| 12 | SERV_LSS_01 | LessonService.cs | Kiểm tra thêm bài học khi course content thuộc course nháp của teacher. | `teacherId` hợp lệ, `courseContentId` hợp lệ, `LessonCreateDTO` hợp lệ. | Tạo lesson đúng title, order, duration, video URL và content ID. | PASS |
| 13 | SERV_LSS_02 | LessonService.cs | Kiểm tra chặn thêm lesson khi course không ở trạng thái nháp. | Course của content có `Status = "published"`. | Ném exception, không tạo lesson. | PASS |
| 14 | SERV_LSS_03 | LessonService.cs | Kiểm tra lấy danh sách lesson theo course content. | `courseContentId` tồn tại, repository trả về 2 lesson. | Trả về danh sách lesson card đúng số lượng và đúng thứ tự. | PASS |
| 15 | SERV_LSS_04 | LessonService.cs | Kiểm tra chặn cập nhật thứ tự lesson khi danh sách ID gửi lên không khớp dữ liệu hiện có. | Repository có lesson A, request gửi lesson B. | Ném exception, không cập nhật thứ tự lesson. | PASS |
| 16 | SERV_CRS_01 | CourseReviewService.cs | Kiểm tra sinh viên đã enroll có thể thêm đánh giá khóa học. | `courseId`, `studentId` hợp lệ, `CourseReviewCreateDTO { Rating = 5, Review = "..." }`. | Tạo review mới, cập nhật `AverageRating` và `ReviewCount` của course. | PASS |
| 17 | SERV_CRS_02 | CourseReviewService.cs | Kiểm tra chặn đánh giá khi sinh viên chưa enroll khóa học. | `studentId` chưa enroll vào `courseId`. | Ném exception, không tạo review. | PASS |
| 18 | SERV_CRS_03 | CourseReviewService.cs | Kiểm tra cập nhật review tạo bản mới nhất và đánh dấu bản cũ không còn mới nhất. | `reviewId` tồn tại, `CourseReviewUpdateDTO` có rating/review mới. | Review cũ có `IsNewest = false`, review mới được thêm với nội dung cập nhật. | PASS |
| 19 | SERV_ECS_01 | EnrollmentCourseService.cs | Kiểm tra tạo enrollment active khi course và student hợp lệ. | `courseId` tồn tại, `studentId` tồn tại, chưa enroll trước đó. | Tạo enrollment với `Status = "active"`, `Progress = 0`. | PASS |
| 20 | SERV_ECS_02 | EnrollmentCourseService.cs | Kiểm tra chặn enroll trùng khóa học. | Student đã enroll vào course. | Ném exception, không tạo enrollment mới. | PASS |
| 21 | SERV_ECS_03 | EnrollmentCourseService.cs | Kiểm tra cập nhật tiến độ khi hoàn thành lesson mới. | Enrollment active, lesson tồn tại, lesson chưa được đánh dấu hoàn thành. | Tạo `LessonProgress`, cập nhật `Progress` xấp xỉ 70%, trạng thái vẫn active. | PASS |
| 22 | SERV_ECS_04 | EnrollmentCourseService.cs | Kiểm tra không cập nhật lại khi lesson đã hoàn thành trước đó. | Lesson đã có `LessonProgress` cho student. | Không thêm progress mới, không gọi cập nhật enrollment. | PASS |
| 23 | SERV_ECS_05 | EnrollmentCourseService.cs | Kiểm tra yêu cầu hủy enrollment đủ điều kiện. | User là student của enrollment, enrollment active, progress dưới ngưỡng hoàn thành. | Tạo `RequestRefundCourse`, đổi enrollment sang trạng thái chờ hoàn tiền. | PASS |
| 24 | SERV_RUS_01 | RequestUpdateService.cs | Kiểm tra tạo request update khi target course thuộc teacher. | `userId`/teacher hợp lệ, `TargetType = "course"`, `TargetId` là course thuộc teacher. | Tạo request update đúng user, target type, target ID, lý do và trạng thái chờ xử lý. | PASS |
| 25 | SERV_RUS_02 | RequestUpdateService.cs | Kiểm tra chặn request update khi target type không hợp lệ. | `TargetType = "invalid-type"`. | Ném `ArgumentException`, không tạo request update. | PASS |

## Kết quả chạy hiện tại

- **Tổng số test module Courses:** 25.
- **Passed:** 25.
- **Failed:** 0.
- **Skipped:** 0.

