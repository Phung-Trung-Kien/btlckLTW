# Unit Test Cases - Module Courses (Backend Service)

## Thông tin chung

- **Phạm vi kiểm thử:** Toàn bộ các file service của module Courses ở backend .NET.
- **Test framework:** xUnit (v2.5.3).
- **Thư viện hỗ trợ:** Moq (v4.20.72), FluentAssertions (v8.9.0), Microsoft.EntityFrameworkCore.InMemory (v8.0.10).
- **Công cụ coverage:** coverlet.collector + ReportGenerator.
- **Vị trí test script:** `backend/project.Tests/Modules/Courses/`.
- **Lệnh chạy test module Courses:**

```powershell
dotnet test backend/project.Tests/project.Tests.csproj --filter "FullyQualifiedName~CategoryServiceTests|FullyQualifiedName~CourseServiceTests|FullyQualifiedName~CourseContentServiceTests|FullyQualifiedName~LessonServiceTests|FullyQualifiedName~CourseReviewServiceTests|FullyQualifiedName~EnrollmentCourseServiceTests|FullyQualifiedName~RequestUpdateServiceTests"
```

- **Lệnh tạo coverage report:**

```powershell
dotnet test backend/project.Tests/project.Tests.csproj --filter "..." --collect:"XPlat Code Coverage" --results-directory "backend/project.Tests/TestResults/CourseCoverage"
reportgenerator "-reports:<path>/coverage.cobertura.xml" "-targetdir:backend/project.Tests/CoverageReport/Courses" "-reporttypes:Html;TextSummary" "-classfilters:+CourseService;+EnrollmentCourseService;+CourseReviewService;+LessonService;+CourseContentService;+CategoryService;+RequestUpdateService"
```

---

## Danh sách file test script

| File | Lớp Service được kiểm thử | Số test cases |
|---|---|---:|
| `CategoryServiceTests.cs` | `CategoryService` | 4 |
| `CourseServiceTests.cs` | `CourseService` | 14 |
| `CourseContentServiceTests.cs` | `CourseContentService` | 5 |
| `LessonServiceTests.cs` | `LessonService` | 7 |
| `CourseReviewServiceTests.cs` | `CourseReviewService` | 6 |
| `EnrollmentCourseServiceTests.cs` | `EnrollmentCourseService` | 10 |
| `RequestUpdateServiceTests.cs` | `RequestUpdateService` | 4 |
| `CourseTestHelpers.cs` | *(Lớp tiện ích dùng chung)* | — |
| **Tổng** | | **50** |

---

## Bảng Test Case

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Ghi chú |
|---:|---|---|---|---|---|---|
| 1 | SERV_CAS_01 | CategoryService.cs | Tạo danh mục khóa học khi dữ liệu đầu vào hợp lệ. | `CategoryCreateDTO { Name = "Lập trình", Description = "..." }` | Repository được gọi với category có `Name` và `Description` đúng; `Id` tự sinh không rỗng. | PASS |
| 2 | SERV_CAS_02 | CategoryService.cs | Lấy danh mục theo ID không tồn tại phải báo lỗi. | `categoryId = "missing-category"` không có trong repository. | Ném `KeyNotFoundException` với message chứa ID; không trả về dữ liệu sai. | PASS |
| 3 | SERV_CAS_03 | CategoryService.cs | Lấy toàn bộ danh mục từ repository. | Repository trả về 3 category. | Kết quả có đúng 3 phần tử; tên và ID khớp dữ liệu mock. | PASS |
| 4 | SERV_CAS_04 | CategoryService.cs | Lấy danh mục theo ID tồn tại trả về đúng đối tượng. | `categoryId = "cat-exist"` có trong repository. | Trả về `Category` với đúng `Id` và `Name`. | PASS |
| 5 | SERV_COS_01 | CourseService.cs | Tạo khóa học nháp khi teacher tồn tại. | `teacherId` hợp lệ (GUID), `CourseCreateDTO { Title, Price, ... }`. | Course được lưu với `Status = "draft"`, `TeacherId`, `Title`, `Price` đúng yêu cầu. | PASS |
| 6 | SERV_COS_02 | CourseService.cs | Chặn tạo khóa học khi teacher không tồn tại. | `teacherId` không tồn tại trong repository. | Ném `KeyNotFoundException("Teacher not found")`; repository `AddCourseAsync` không được gọi. | PASS |
| 7 | SERV_COS_03 | CourseService.cs | Cập nhật khóa học nháp khi đúng giáo viên sở hữu. | `teacherId` khớp với `TeacherId` của course `draft`; `CourseUpdateDTO` có dữ liệu mới. | Course được cập nhật `Title`, `CategoryId`, `Price`, `DiscountPrice`, `ThumbnailUrl`. | PASS |
| 8 | SERV_COS_04 | CourseService.cs | Chặn cập nhật course khi giáo viên không sở hữu. | `teacherId` khác `TeacherId` của course. | Ném `UnauthorizedAccessException("You are not the teacher of this course")`; không lưu thay đổi. | PASS |
| 9 | SERV_COS_05 | CourseService.cs | Chặn cập nhật course khi không ở trạng thái nháp. | Course có `Status = "published"`, cùng `teacherId`. | Ném `InvalidOperationException("Only draft courses can be updated")`; không gọi `UpdateCourseAsync`. | PASS |
| 10 | SERV_COS_06 | CourseService.cs | Tìm kiếm khóa học có phân trang. | `keyword = "ASP"`, `categoryId`, `page = 1`, `pageSize = 10`. | Trả về `TotalCount = 1`, `TotalPages = 1`, danh sách có course tên "ASP.NET Core". | PASS |
| 11 | SERV_COS_07 | CourseService.cs | Gửi yêu cầu duyệt course: trạng thái draft → pending khi đúng teacher. | `teacherId` khớp `TeacherId`; course đang `draft`. | Course được cập nhật `Status = "pending"`; repository `UpdateCourseAsync` được gọi 1 lần. | PASS |
| 12 | SERV_COS_08 | CourseService.cs | Chặn gửi yêu cầu duyệt khi teacher không sở hữu course. | `teacherId` khác `TeacherId` của course. | Ném `UnauthorizedAccessException`; `UpdateCourseAsync` không được gọi. | PASS |
| 13 | SERV_COS_09 | CourseService.cs | Lấy danh sách khóa học của teacher có thống kê. | `teacherId` tồn tại; repository trả về 2 course (1 published, 1 draft). | `TotalCount = 2`; `TotalPublishedCourses = 1`; `TotalDraftCourses = 1`; `TotalEnrollments = 1`. | PASS |
| 14 | SERV_COS_10 | CourseService.cs | **CheckDB/Rollback:** Tạo đầy đủ course + content + lessons, sau test xóa trắng dữ liệu. | `FullCourseCreateDTO` gồm course, content và 2 lesson; InMemory DB có sẵn teacher, user, category. | DB có 1 course `draft`, 1 content, 2 lesson đúng thứ tự; cuối test DB trở về rỗng (rollback thành công). | PASS |
| 15 | SERV_COS_11 | CourseService.cs | Lấy toàn bộ danh sách khóa học. | Repository trả về 2 course. | Kết quả có 2 phần tử; `TeacherName` của phần tử cuối khớp dữ liệu mock. | PASS |
| 16 | SERV_COS_12 | CourseService.cs | Lấy chi tiết khóa học theo ID khi tồn tại. | `courseId` hợp lệ; repository trả về course đầy đủ dữ liệu. | DTO trả về đúng `Id`, `Title`, `TeacherName`, `Price`. | PASS |
| 17 | SERV_COS_13 | CourseService.cs | Lấy chi tiết khóa học theo ID không tồn tại phải báo lỗi. | `courseId = "non-existent"`; repository trả về `null`. | Ném `KeyNotFoundException("Course not found")`. | PASS |
| 18 | SERV_COS_14 | CourseService.cs | Lấy danh sách khóa học đã enroll của student theo phân trang. | `studentId` tồn tại; repository trả về 1 enrollment có course. | `TotalCount = 1`; tên course và `Progress = 50.0` khớp dữ liệu mock. | PASS |
| 19 | SERV_CCS_01 | CourseContentService.cs | Thêm nội dung khóa học khi course đang nháp và đúng teacher. | `teacherId` khớp, course `draft`, `CourseContentCreateDTO` hợp lệ, chưa có content. | Content được lưu với đúng `CourseId`, `Title`, `Introduce`. | PASS |
| 20 | SERV_CCS_02 | CourseContentService.cs | Chặn thêm content khi course không ở trạng thái nháp. | Course có `Status = "published"`. | Ném `InvalidOperationException("Cannot add course content unless the course is in draft status")`; không tạo content. | PASS |
| 21 | SERV_CCS_03 | CourseContentService.cs | Chặn thêm content khi course đã có content trước đó. | Course `draft`; repository báo content đã tồn tại. | Ném `Exception("Course content already exists for this course")`; không gọi `AddCourseContentAsync`. | PASS |
| 22 | SERV_CCS_04 | CourseContentService.cs | Cập nhật nội dung khóa học khi đúng teacher và course nháp. | `teacherId` khớp; course `draft`; `CourseContentUpdateDTO` có dữ liệu mới. | Content được cập nhật `Title` và `Introduce` đúng giá trị mới. | PASS |
| 23 | SERV_CCS_05 | CourseContentService.cs | Lấy thông tin course content theo courseId. | `courseId` tồn tại; repository trả về content. | DTO trả về đúng `Id`, `Title`, `Introduce`. | PASS |
| 24 | SERV_LSS_01 | LessonService.cs | Thêm bài học khi course content thuộc course nháp của đúng teacher. | `teacherId`, `courseContentId` hợp lệ; `LessonCreateDTO { Title, Order, Duration, VideoUrl }`. | Lesson được lưu với đúng `CourseContentId`, `Title`, `Order`. | PASS |
| 25 | SERV_LSS_02 | LessonService.cs | Chặn thêm lesson khi course không ở trạng thái nháp. | Course của content có `Status = "published"`. | Ném `Exception("Can only update lessons for courses in Draft status")`; không tạo lesson. | PASS |
| 26 | SERV_LSS_03 | LessonService.cs | Lấy danh sách lesson theo course content. | `courseContentId` tồn tại; repository trả về 2 lesson. | Kết quả có 2 phần tử; `Title` và `Order` khớp dữ liệu mock. | PASS |
| 27 | SERV_LSS_04 | LessonService.cs | Chặn cập nhật thứ tự lesson khi danh sách ID gửi lên không khớp DB. | Repository có `lesson-1`; request gửi `another-lesson`. | Ném `Exception("Lesson IDs in the request do not match the existing lessons")`. | PASS |
| 28 | SERV_LSS_05 | LessonService.cs | Cập nhật nội dung bài học khi course nháp và đúng teacher. | `teacherId` khớp; course `draft`; `LessonUpdateDTO { Title, VideoUrl, Duration, TextContent }`. | Lesson được cập nhật đúng 4 trường. | PASS |
| 29 | SERV_LSS_06 | LessonService.cs | Cập nhật thứ tự lesson thành công khi danh sách ID khớp. | Repository có `lesson-A`, `lesson-B`; request đảo thứ tự (B→1, A→2). | `lesson-B.Order = 1`; `lesson-A.Order = 2` sau khi cập nhật. | PASS |
| 30 | SERV_LSS_07 | LessonService.cs | Lấy chi tiết bài học khi student đã enroll khóa học. | `studentId` đã enroll `courseId`; `lessonId` tồn tại trong content. | DTO trả về đúng `Title`, `VideoUrl`, `Duration`, `Order`. | PASS |
| 31 | SERV_CRS_01 | CourseReviewService.cs | Sinh viên đã enroll có thể thêm đánh giá khóa học. | `courseId`, `studentId` hợp lệ, student chưa review; `CourseReviewCreateDTO { Rating = 5 }`. | Review mới với `IsNewest = true`; `ReviewCount = 2`; `AverageRating = 4.5`. | PASS |
| 32 | SERV_CRS_02 | CourseReviewService.cs | Chặn đánh giá khi sinh viên chưa enroll khóa học. | `studentId` chưa enroll vào `courseId`. | Ném `Exception("Student with id ... is not enrolled in course with id ...")`; không tạo review. | PASS |
| 33 | SERV_CRS_03 | CourseReviewService.cs | Chặn đánh giá khi sinh viên đã đánh giá khóa học này trước đó. | Student đã có review cho course; `CheckReviewedCourseAsync` trả về `true`. | Ném `Exception("Student with id ... has already reviewed course with id ...")`; không tạo review. | PASS |
| 34 | SERV_CRS_04 | CourseReviewService.cs | Cập nhật review tạo bản mới nhất và đánh dấu bản cũ không còn newest. | `reviewId` tồn tại; `CourseReviewUpdateDTO { Rating = 5, Comment = "Updated" }`. | Review cũ `IsNewest = false`; review mới có `ParentId = reviewId`, `Rating = 5`, `IsNewest = true`. | PASS |
| 35 | SERV_CRS_05 | CourseReviewService.cs | Lấy danh sách đánh giá theo khóa học. | `courseId` tồn tại; repository trả về 2 review với thông tin student. | Kết quả có 2 phần tử; `Rating` và `StudentName` khớp dữ liệu mock. | PASS |
| 36 | SERV_CRS_06 | CourseReviewService.cs | Lấy danh sách đánh giá theo student. | `studentId` tồn tại; repository trả về 2 review từ 2 course khác nhau. | Kết quả có 2 phần tử; `Rating` và `Comment` khớp dữ liệu mock. | PASS |
| 37 | SERV_ECS_01 | EnrollmentCourseService.cs | Tạo enrollment active khi course và student đều tồn tại và chưa enroll. | `courseId` tồn tại, `studentId` tồn tại, chưa có enrollment trước đó. | Enrollment được lưu với `Status = "active"`, `Progress = 0.00`. | PASS |
| 38 | SERV_ECS_02 | EnrollmentCourseService.cs | Chặn enroll trùng khóa học. | Student đã enroll vào course (`IsEnrollmentExistAsync = true`). | Ném `Exception("Student has already enrolled in this course")`; không tạo enrollment mới. | PASS |
| 39 | SERV_ECS_03 | EnrollmentCourseService.cs | Chặn enroll khi student không tồn tại. | `studentId` không có trong repository. | Ném `KeyNotFoundException("Student with id: ... not found")`; không tạo enrollment. | PASS |
| 40 | SERV_ECS_04 | EnrollmentCourseService.cs | Cập nhật tiến độ khi hoàn thành lesson mới. | Enrollment active; lesson tồn tại; lesson chưa được đánh dấu hoàn thành; 1 lesson / 0 exam. | Tạo `LessonProgress`; `Progress ≈ 70%`; trạng thái enrollment vẫn `active`. | PASS |
| 41 | SERV_ECS_05 | EnrollmentCourseService.cs | Không cập nhật tiến độ nếu lesson đã hoàn thành trước đó. | `LessonProgressRepository.ExistsAsync = true`. | `AddNewLessonProgressAsync` không được gọi; `UpdateProgressEnrollmentAsync` không được gọi. | PASS |
| 42 | SERV_ECS_06 | EnrollmentCourseService.cs | Yêu cầu hủy enrollment đủ điều kiện tạo refund request. | User là student sở hữu enrollment; course có `Price = 1000`; `Progress = 20`; enroll < 2 ngày. | Enrollment đổi sang `"Peding Refund"`; `RefundRequestCourse` được tạo với `RefundAmount = 800`, `Status = "pending"`. | PASS |
| 43 | SERV_ECS_07 | EnrollmentCourseService.cs | Chặn hoàn tiền khi khóa học miễn phí. | Course có `Price = 0`. | Ném `Exception("Free courses are not eligible for refunds.")`; không tạo refund request. | PASS |
| 44 | SERV_ECS_08 | EnrollmentCourseService.cs | Chặn hoàn tiền khi đã quá 2 ngày kể từ khi enroll. | `EnrolledAt = DateTime.UtcNow.AddDays(-3)` (đã quá 2 ngày). | Ném `Exception("Refund requests are only accepted within 2 days of enrollment.")`; không tạo refund request. | PASS |
| 45 | SERV_ECS_09 | EnrollmentCourseService.cs | Chặn hoàn tiền khi tiến độ học ≥ 50%. | `Progress = 60` (vượt ngưỡng 50%). | Ném `Exception("Refund not available for courses with progress ≥ 50%.")`; không tạo refund request. | PASS |
| 46 | SERV_ECS_10 | EnrollmentCourseService.cs | Lấy chi tiết enrollment khi student là chủ sở hữu. | `userId` khớp với `Student.User.Id`; `enrollmentId` thuộc đúng `courseId`. | DTO trả về đúng `StudentName`, `Progress = 30`, `Status = "active"`. | PASS |
| 47 | SERV_RUS_01 | RequestUpdateService.cs | Tạo request update khi target là course thuộc đúng teacher. | `userId` (GUID hợp lệ), `TargetType = "course"`, `TargetId` là course thuộc teacher, teacher tồn tại. | `UpdateRequestCourse` được lưu với đúng `TargetType`, `TargetId`, `RequestById`, `UpdatedDataJSON`. | PASS |
| 48 | SERV_RUS_02 | RequestUpdateService.cs | Chặn request update khi target type không hợp lệ. | `TargetType = "invalid"`, `TargetId` là GUID hợp lệ. | Ném `ArgumentException("Invalid TargetType. Only 'course', 'coursecontent', 'lesson' is allowed.")`; không tạo request. | PASS |
| 49 | SERV_RUS_03 | RequestUpdateService.cs | Chặn request update khi TargetId không phải GUID hợp lệ. | `TargetId = "not-a-valid-guid"` (chuỗi tùy ý). | Ném `ArgumentException("Invalid TargetId or RequestById. It must be a valid GUID.")`; không tạo request. | PASS |
| 50 | SERV_RUS_04 | RequestUpdateService.cs | Tạo request update khi target là coursecontent thuộc đúng teacher. | `userId` (GUID hợp lệ), `TargetType = "coursecontent"`, `TargetId` là content của course thuộc teacher. | `UpdateRequestCourse` được lưu với `TargetType = "coursecontent"` và `RequestById` đúng. | PASS |
| 51 | SERV_RUS_05 | RequestUpdateService.cs | Tạo request update khi target là lesson thuộc đúng teacher. | `TargetType = "lesson"`, `TargetId` là lesson trong course thuộc teacher. | `UpdateRequestCourse` được lưu với `TargetType = "lesson"` và đúng `TargetId`. | PASS |
| 52 | SERV_RUS_06 | RequestUpdateService.cs | Chặn request update khi teacher không tồn tại trong hệ thống. | `userId` hợp lệ (GUID) nhưng `IsTeacherExistsAsync = false`. | Ném `ArgumentException("Teacher with the given RequestById does not exist.")`; không gọi create. | PASS |
| 53 | SERV_ECS_11 | EnrollmentCourseService.cs | IsEnrolledInCourseAsync trả về true khi student đã enroll. | `studentId`, `courseId` hợp lệ; repository trả về `true`. | Kết quả `true`; repository được gọi đúng một lần. | PASS |
| 54 | SERV_ECS_12 | EnrollmentCourseService.cs | Chặn cập nhật tiến độ khi cả LessonId và ExamId đều được cung cấp. | `LessonId` và `ExamId` đều khác null. | Ném `ArgumentException("LessonId or ExamId must have value, not both")`. | PASS |
| 55 | SERV_ECS_13 | EnrollmentCourseService.cs | Chặn cập nhật tiến độ khi enrollment không ở trạng thái active. | Enrollment `Status = "Completed"`. | Ném `Exception("Only active enrollment can be updated!")`. | PASS |
| 56 | SERV_ECS_14 | EnrollmentCourseService.cs | Chặn cập nhật tiến độ qua LessonId khi lesson không tồn tại. | `LessonExistsAsync = false`. | Ném `KeyNotFoundException("Lesson with id: ... not found")`. | PASS |
| 57 | SERV_ECS_15 | EnrollmentCourseService.cs | Cập nhật tiến độ qua ExamId và chuyển trạng thái Completed khi đạt ≥ 95%. | Exam tồn tại và mở; 1/1 lesson hoàn thành; 1/1 exam đậu → progress = 100%. | `Progress ≥ 95`; `Status = "Completed"`; `UpdateProgressEnrollmentAsync` được gọi. | PASS |
| 58 | SERV_ECS_16 | EnrollmentCourseService.cs | Chặn cập nhật tiến độ qua ExamId khi exam không tồn tại hoặc chưa mở. | `GetExamStatusAsync = (false, false)`. | Ném `KeyNotFoundException("Exam with id: ... not found")`. | PASS |
| 59 | SERV_ECS_17 | EnrollmentCourseService.cs | Chặn hủy enrollment khi user không sở hữu enrollment. | `userId` khác `Student.User.Id`; user tồn tại. | Ném `UnauthorizedAccessException("You are not authorized to update this enrollment")`. | PASS |
| 60 | SERV_ECS_18 | EnrollmentCourseService.cs | Chặn hủy enrollment khi enrollment.CourseId không khớp courseId. | `enrollment.CourseId != courseId`. | Ném `KeyNotFoundException("Course Id is not match with enrollment.")`. | PASS |
| 61 | SERV_ECS_19 | EnrollmentCourseService.cs | Chặn hủy enrollment khi enrollment không ở trạng thái active. | `enrollment.Status = "Completed"`. | Ném `Exception("Can't cancel enrollment without active status")`. | PASS |
| 62 | SERV_ECS_20 | EnrollmentCourseService.cs | Hoàn tiền 100% khi student hủy enrollment và progress = 0. | `Progress = 0`; `Price = 500000`; điều kiện hợp lệ. | `RefundAmount = 500000`; `RefundRequestCourse` được tạo. | PASS |
| 63 | SERV_ECS_21 | EnrollmentCourseService.cs | Lấy danh sách enrollment gần nhất của teacher. | `teacherId` tồn tại; repository trả về 1 enrollment. | Kết quả có 1 phần tử; `StudentName = "Học viên X"`; `CourseName = "Khóa học Y"`. | PASS |
| 64 | SERV_LSS_08 | LessonService.cs | Chặn thêm lesson khi giáo viên không sở hữu khóa học. | Course `draft` nhưng `Teacher.User.Id != userId`. | Ném `UnauthorizedAccessException("You are not the teacher of this course")`; không gọi `AddLessonAsync`. | PASS |
| 65 | SERV_LSS_09 | LessonService.cs | Chặn cập nhật lesson khi giáo viên không sở hữu khóa học. | Course `draft` nhưng `Teacher.User.Id != userId`. | Ném `UnauthorizedAccessException("You are not the teacher of this course")`; không gọi `UpdateLessonAsync`. | PASS |
| 66 | SERV_LSS_10 | LessonService.cs | Chặn cập nhật thứ tự lesson khi giáo viên không sở hữu. | Course `draft` nhưng `Teacher.User.Id != userId`. | Ném `UnauthorizedAccessException("You are not the teacher of this course")`. | PASS |
| 67 | SERV_LSS_11 | LessonService.cs | Chặn lấy danh sách lesson khi courseContent không tồn tại. | `CourseContentExistsByContentIdAsync = false`. | Ném `Exception("Course content with id: ... not found")`. | PASS |
| 68 | SERV_LSS_12 | LessonService.cs | Chặn lấy chi tiết lesson khi student chưa enroll khóa học. | `IsEnrollmentExistAsync = false`. | Ném `Exception("You are not enrolled in this course")`. | PASS |
| 69 | SERV_CCS_06 | CourseContentService.cs | Chặn lấy thông tin content khi khóa học không tồn tại. | `CourseExistsAsync = false`. | Ném `KeyNotFoundException("Course not found")`. | PASS |
| 70 | SERV_CCS_07 | CourseContentService.cs | Lấy overview content khi đúng teacher sở hữu khóa học. | `teacherId` khớp; repository trả về content có 2 lesson. | DTO trả về đúng `Id`; `Lessons.Count = 2`; `Lessons[0].Title = "Bài 1"`. | PASS |
| 71 | SERV_CCS_08 | CourseContentService.cs | Chặn lấy overview content khi giáo viên không sở hữu. | `course.TeacherId != teacherId`. | Ném `UnauthorizedAccessException("You are not the teacher of this course")`. | PASS |
| 72 | SERV_COS_15 | CourseService.cs | Lấy toàn bộ dữ liệu khóa học để chỉnh sửa khi đúng teacher. | `teacherId` tồn tại; course có content với 2 lesson. | DTO trả về đúng `Id`, `Title`, `CourseContent.Title`, `CourseContent.Lessons.Count = 2`. | PASS |
| 73 | SERV_COS_16 | CourseService.cs | Chặn lấy dữ liệu chỉnh sửa khi teacher không tồn tại. | `IsTeacherExistsAsync = false`. | Ném `KeyNotFoundException("Teacher not found")`. | PASS |
| 74 | SERV_COS_17 | CourseService.cs | **CheckDB/Rollback:** Cập nhật đầy đủ course + content + lessons mới; sau test rollback dữ liệu. | InMemory DB có course `draft`, content, 1 lesson; `FullCourseUpdateDTO` cập nhật và thêm 1 lesson mới. | Course `Title = "Mới"`, `Price = 200`; content `Title = "Content mới"`; sau test DB trở về rỗng. | PASS |
| 75 | SERV_COS_18 | CourseService.cs | SearchItemsAsync báo lỗi khi repository ném exception. | Repository `SearchItemsAsync` ném `Exception("DB connection lost")`. | Ném `Exception("Error when retriev course: *")`. | PASS |
| 76 | SERV_COS_19 | CourseService.cs | GetCoursesByTeacherIdAsync báo lỗi khi teacher không tồn tại. | `IsTeacherExistsAsync = false`. | Ném `Exception("Error when retriev course: *")`. | PASS |
| 77 | SERV_COS_20 | CourseService.cs | GetEnrolledCoursesByStudentIdAsync báo lỗi khi student không tồn tại. | `IsStudentExistAsync = false`. | Ném `Exception("Error when retriev enrolled courses: *")`. | PASS |

---

## Kết quả thực thi

| Chỉ số | Giá trị |
|---|---|
| **Tổng số test** | **77** |
| **Passed** | **77** |
| **Failed** | 0 |
| **Skipped** | 0 |
| **Thời gian chạy** | ~1 giây |

```text
Passed!  - Failed: 0, Passed: 77, Skipped: 0, Total: 77, Duration: 1 s
```

---

## Code Coverage Report (Module Courses)

| Class | Line Coverage | Method Coverage |
|---|---|---|
| `CategoryService` | **100%** | **100%** |
| `CourseService` | **95.4%** | **100%** |
| `LessonService` | **96.3%** | **100%** |
| `RequestUpdateService` | **93.7%** | **100%** |
| `CourseReviewService` | **89.1%** | **100%** |
| `CourseContentService` | **85.1%** | **100%** |
| `EnrollmentCourseService` | **83.2%** | **95.4%** |
| **Tổng (7 classes)** | **91% line coverage** | **95.4% method coverage** |

> **Lưu ý:** Phần ~9% chưa cover là các branch điều kiện null-coalescing trong LINQ projection (ví dụ: `en.Student?.User?.FullName ?? "Unknown"`), các nhánh EF Core internal state-tracking, và private helper `CalculateProgressAsync` — đây là các nhánh phòng thủ/failsafe không thể kích hoạt qua unit test với mock.

---

## Ghi chú kỹ thuật

### Chiến lược Mock vs InMemory DB

| Loại test | Kỹ thuật | Áp dụng cho |
|---|---|---|
| Mock repositories (Moq) | `Mock<IXxxRepository>` + `.Setup(...).ReturnsAsync(...)` | Tất cả test case logic thuần (không thay đổi DB) |
| InMemory DB (EF Core) | `CourseTestHelpers.CreateInMemoryDbContext(...)` + real repositories | Test case `SERV_COS_10` và `SERV_COS_17` (CheckDB/Rollback) |

### CheckDB & Rollback (SERV_COS_10, SERV_COS_17)

- **CheckDB:** Sau khi gọi service, truy vấn trực tiếp `dbContext` để xác minh dữ liệu đã được lưu đúng.
- **Rollback:** Khối `finally` xóa toàn bộ entity đã tạo và `SaveChangesAsync()`, đảm bảo DB trở về rỗng sau test.

### Cấu trúc test theo pattern AAA

Tất cả test tuân thủ **Arrange → Act → Assert** với comment rõ ràng và `// [ID: SERV_XXX_NN]` ở đầu mỗi test method.

