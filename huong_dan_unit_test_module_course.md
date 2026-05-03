# HƯỚNG DẪN HOÀN THÀNH UNIT TEST REPORT VÀ TEST SCRIPT - MODULE COURSES

## 0. Ghi chú phạm vi

Bạn phụ trách **Unit Test cho module Courses ở backend**. Trọng tâm nên là các lớp service có logic nghiệp vụ:

- `CourseService`
- `EnrollmentCourseService`
- `CourseReviewService`
- `LessonService`
- `CourseContentService`
- `CategoryService`
- `RequestUpdateService`

Không nên dàn trải sang toàn bộ frontend hoặc controller vì báo cáo của nhóm đã xác định unit test tập trung vào backend service/business logic.

Tài liệu Google Drive trong đề bài có yêu cầu đăng nhập nên không đọc được trực tiếp trong môi trường này. File hướng dẫn này được xây dựng dựa trên yêu cầu bạn cung cấp, cấu trúc test hiện có trong repo và format báo cáo nhóm đang dùng.

---

## 1. Chuẩn bị trước khi viết report

### Bước 1. Xác định file nguồn cần test

Mở các file service trong:

```text
backend/project/Modules/Courses/Services/Implementations/
```

Danh sách file chính:

```text
CategoryService.cs
CourseContentService.cs
CourseReviewService.cs
CourseService.cs
EnrollmentCourseService.cs
LessonService.cs
RequestUpdateService.cs
```

### Bước 2. Xem các test mẫu đã có

Repo hiện đã có test mẫu ở:

```text
backend/project.Tests/Modules/Payments/
backend/project.Tests/Modules/Posts/
```

Nên tham khảo cách đặt tên test, cách dùng:

- `xUnit` với `[Fact]`
- `FluentAssertions` với `.Should()`
- `Moq` để mock repository
- comment Test Case ID ngay phía trên mỗi test
- `try/finally` để rollback dữ liệu test khi có truy cập DB thật

### Bước 3. Tạo thư mục test cho module Courses

Khi bắt đầu viết test script, tạo thư mục:

```text
backend/project.Tests/Modules/Courses/
```

Tên file test đề xuất:

```text
CourseServiceTests.cs
EnrollmentCourseServiceTests.cs
CourseReviewServiceTests.cs
LessonServiceTests.cs
CourseContentServiceTests.cs
CategoryServiceTests.cs
RequestUpdateServiceTests.cs
```

---

## 2. Hoàn thành mục 1.1 - Tools and Libraries

Trong Excel, phần này có thể giữ theo nhóm đã viết. Nếu bạn cần ghi riêng cho module Courses thì dùng nội dung sau:

| STT | Tool/Library | Mục đích sử dụng |
|---|---|---|
| 1 | xUnit | Framework viết và chạy unit test cho backend .NET 8 |
| 2 | FluentAssertions | Viết assert dễ đọc, rõ expected/actual |
| 3 | Moq | Mock repository/dependency để cô lập service cần test |
| 4 | Microsoft.EntityFrameworkCore.InMemory | Dự phòng khi cần test logic có DB nhưng không muốn đụng DB thật |
| 5 | coverlet.collector | Thu thập code coverage khi chạy `dotnet test` |
| 6 | ReportGenerator | Chuyển file coverage Cobertura thành báo cáo HTML dễ chụp ảnh |

---

## 3. Hoàn thành mục 1.2 - Scope of Testing

### 3.1. Các file/class ĐƯỢC kiểm thử

Nên ghi trong Excel theo bảng:

| Nhóm | File/Class | Lý do kiểm thử |
|---|---|---|
| Course Service | `CourseService.cs` | Chứa logic tạo, cập nhật, tìm kiếm, phân trang, lấy khóa học theo teacher/student |
| Enrollment Service | `EnrollmentCourseService.cs` | Chứa logic đăng ký khóa học, cập nhật tiến độ, tính progress, yêu cầu hủy/refund |
| Review Service | `CourseReviewService.cs` | Chứa logic thêm/sửa review, tính rating trung bình, kiểm tra đã review |
| Lesson Service | `LessonService.cs` | Chứa logic tạo/cập nhật/sắp xếp lesson, lấy lesson detail |
| Course Content Service | `CourseContentService.cs` | Chứa logic tạo/cập nhật nội dung khóa học |
| Category Service | `CategoryService.cs` | Chứa logic tạo/lấy danh mục khóa học |
| Request Update Service | `RequestUpdateService.cs` | Chứa logic yêu cầu cập nhật khóa học nếu có business rule |

### 3.2. Các file/class KHÔNG cần kiểm thử

| Nhóm | Không kiểm thử | Lý do |
|---|---|---|
| Controllers | `CourseController`, `EnrollmentController`, `LessonController`, `CourseReviewController`, `CategoryController` | Controller chủ yếu nhận request, gọi service, trả response. Nên kiểm thử bằng integration/system test hơn là unit test |
| Repositories | `CourseRepository`, `LessonRepository`, `EnrollmentCourseRepository`, ... | Chủ yếu gọi EF Core/DB. Unit test repository thường ít giá trị; nếu cần chỉ test bằng integration test |
| DTOs | Các file trong `DTOs/` | Chỉ chứa dữ liệu/getter/setter, không có logic nghiệp vụ |
| Models/Entities | `Course`, `Lesson`, `CourseReview`, `Enrollment_course`, ... | Entity chủ yếu mô tả schema, không có xử lý logic đáng kể |
| Migrations | `backend/project/Migrations` | Code tự sinh bởi EF Core, không viết unit test |
| Config/Startup | `Program.cs`, `appsettings*.json` | Thuộc cấu hình ứng dụng, nên kiểm tra bằng smoke test/integration test |

### 3.3. Ghi chú về Repository

Mặc dù scope nhóm ghi “không kiểm thử Repository”, trong test service vẫn có thể:

- Mock repository bằng `Moq` để kiểm thử logic service.
- Dùng DB thật hoặc InMemory DB cho một số test có yêu cầu **CheckDB/Rollback**.

Điểm quan trọng: mục tiêu test vẫn là **Service**, không phải test riêng Repository.

---

## 4. Quy ước Test Case ID cho module Courses

Đề xuất dùng prefix thống nhất:

| Service | Prefix Test Case ID |
|---|---|
| `CourseService` | `SERV_COS_XX` |
| `EnrollmentCourseService` | `SERV_ECS_XX` |
| `CourseReviewService` | `SERV_CRS_XX` |
| `LessonService` | `SERV_LSS_XX` |
| `CourseContentService` | `SERV_CCS_XX` |
| `CategoryService` | `SERV_CAS_XX` |
| `RequestUpdateService` | `SERV_RUS_XX` |

Ví dụ:

```text
SERV_COS_01
SERV_COS_02
SERV_ECS_01
SERV_CRS_01
```

Trong test script, mỗi test phải có comment chứa Test Case ID:

```csharp
// ------------------------------------------------------------------------------------------------
// [ID: SERV_COS_01]
// [Mục đích: Trả về danh sách khóa học khi repository có dữ liệu hợp lệ]
// ------------------------------------------------------------------------------------------------
```

---

## 5. Đề xuất danh sách unit test case cho Excel

Excel mục 1.3 nên có các cột:

```text
STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes (PASS/FAIL)
```

### 5.1. CourseService

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---|---|---|---|---|---|---|
| 1 | SERV_COS_01 | CourseService.cs | Lấy danh sách khóa học thành công | Repository trả về danh sách Course có Category/Teacher | Trả về danh sách `CourseInformationDTO` đúng dữ liệu | PASS |
| 2 | SERV_COS_02 | CourseService.cs | Lấy khóa học theo ID thành công | `courseId` tồn tại | Trả về DTO đúng `Id`, `Title`, `CategoryName`, `TeacherName` | PASS |
| 3 | SERV_COS_03 | CourseService.cs | Báo lỗi khi khóa học không tồn tại | `courseId` không tồn tại | Ném `KeyNotFoundException` hoặc exception tương ứng | PASS |
| 4 | SERV_COS_04 | CourseService.cs | Tìm kiếm khóa học có phân trang | keyword/category/page/pageSize hợp lệ | Trả về đúng list, currentPage, totalPages | PASS |
| 5 | SERV_COS_05 | CourseService.cs | Tạo full course thành công | DTO hợp lệ + teacher tồn tại | Course, CourseContent, Lessons được tạo đúng | PASS |
| 6 | SERV_COS_06 | CourseService.cs | Không tạo course nếu teacher không tồn tại | teacherId không tồn tại | Ném lỗi phù hợp, DB không phát sinh course mới | PASS |
| 7 | SERV_COS_07 | CourseService.cs | Cập nhật full course thành công | courseId tồn tại + DTO cập nhật | Course/CourseContent/Lessons được cập nhật đúng | PASS |
| 8 | SERV_COS_08 | CourseService.cs | Lấy khóa học theo teacher có thống kê | teacherId hợp lệ | Trả về page result và statistic đúng | PASS |

### 5.2. EnrollmentCourseService

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---|---|---|---|---|---|---|
| 9 | SERV_ECS_01 | EnrollmentCourseService.cs | Enroll khóa học miễn phí thành công | studentId, courseId hợp lệ, course free | Tạo enrollment status active | PASS |
| 10 | SERV_ECS_02 | EnrollmentCourseService.cs | Không enroll nếu student không tồn tại | studentId không tồn tại | Ném exception phù hợp | PASS |
| 11 | SERV_ECS_03 | EnrollmentCourseService.cs | Không enroll trùng khóa học | Enrollment đã tồn tại | Ném exception hoặc trả lỗi phù hợp | PASS |
| 12 | SERV_ECS_04 | EnrollmentCourseService.cs | Cập nhật tiến độ bài học thành công | lesson completed | Progress tăng đúng | PASS |
| 13 | SERV_ECS_05 | EnrollmentCourseService.cs | Tính progress khi không có lesson/exam | Course content rỗng | Không chia cho 0, progress = 0 hoặc giá trị hợp lệ | PASS |
| 14 | SERV_ECS_06 | EnrollmentCourseService.cs | Yêu cầu hủy enrollment hợp lệ | enrollment active, đủ điều kiện refund | Tạo RefundRequestCourse, status đổi phù hợp | PASS |
| 15 | SERV_ECS_07 | EnrollmentCourseService.cs | Không hủy nếu quá hạn hoặc sai trạng thái | enrollment completed/cancelled/quá hạn | Ném exception phù hợp | PASS |

### 5.3. CourseReviewService

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---|---|---|---|---|---|---|
| 16 | SERV_CRS_01 | CourseReviewService.cs | Thêm review thành công | student đã enroll, rating hợp lệ | Tạo review, tăng ReviewCount, cập nhật AverageRating | PASS |
| 17 | SERV_CRS_02 | CourseReviewService.cs | Không review nếu chưa enroll | student chưa enroll course | Ném exception phù hợp | PASS |
| 18 | SERV_CRS_03 | CourseReviewService.cs | Không review trùng nếu đã review newest | review mới nhất đã tồn tại | Ném exception hoặc chặn tạo mới | PASS |
| 19 | SERV_CRS_04 | CourseReviewService.cs | Cập nhật review thành công | reviewId hợp lệ, rating/comment mới | Review cũ không còn newest, tạo/cập nhật dữ liệu đúng | PASS |
| 20 | SERV_CRS_05 | CourseReviewService.cs | Kiểm tra has-reviewed | courseId + studentId | Trả true/false đúng | PASS |

### 5.4. LessonService

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---|---|---|---|---|---|---|
| 21 | SERV_LSS_01 | LessonService.cs | Lấy danh sách lesson theo course content | courseContentId hợp lệ | Trả list lesson đúng order | PASS |
| 22 | SERV_LSS_02 | LessonService.cs | Lấy lesson detail thành công | lessonId hợp lệ | Trả LessonInformationDTO đúng | PASS |
| 23 | SERV_LSS_03 | LessonService.cs | Tạo lesson thành công | DTO hợp lệ | Lesson được tạo đúng CourseContentId | PASS |
| 24 | SERV_LSS_04 | LessonService.cs | Cập nhật lesson thành công | lessonId + DTO hợp lệ | Lesson thay đổi đúng field | PASS |
| 25 | SERV_LSS_05 | LessonService.cs | Sắp xếp lesson thành công | Danh sách lesson order mới | Repository được gọi cập nhật order đúng | PASS |

### 5.5. CourseContentService

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---|---|---|---|---|---|---|
| 26 | SERV_CCS_01 | CourseContentService.cs | Tạo course content thành công | courseId hợp lệ, DTO hợp lệ | CourseContent được tạo đúng | PASS |
| 27 | SERV_CCS_02 | CourseContentService.cs | Không tạo content nếu course không tồn tại | courseId sai | Ném exception phù hợp | PASS |
| 28 | SERV_CCS_03 | CourseContentService.cs | Lấy overview course content | courseId hợp lệ | Trả DTO overview đúng | PASS |
| 29 | SERV_CCS_04 | CourseContentService.cs | Cập nhật course content thành công | courseId hợp lệ + DTO cập nhật | Content thay đổi đúng | PASS |

### 5.6. CategoryService

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---|---|---|---|---|---|---|
| 30 | SERV_CAS_01 | CategoryService.cs | Tạo category thành công | `CategoryCreateDTO` hợp lệ | Category được tạo đúng tên | PASS |
| 31 | SERV_CAS_02 | CategoryService.cs | Lấy danh sách category thành công | Repository có dữ liệu | Trả list category đúng | PASS |

### 5.7. RequestUpdateService

| STT | Test Case ID | Tên File | Test Objective | Input | Expected Output | Notes |
|---|---|---|---|---|---|---|
| 32 | SERV_RUS_01 | RequestUpdateService.cs | Tạo request update course thành công | teacher/course hợp lệ | Request được tạo đúng trạng thái | PASS |
| 33 | SERV_RUS_02 | RequestUpdateService.cs | Không tạo request nếu course không tồn tại | courseId sai | Ném exception phù hợp | PASS |

---

## 6. Cách viết test script

### 6.1. Nguyên tắc chung

Mỗi test nên theo cấu trúc:

```text
Arrange -> Act -> Assert
```

Yêu cầu bắt buộc:

- Tên test mô tả rõ hành vi.
- Có comment chứa Test Case ID.
- Dùng biến tên rõ nghĩa.
- Với test không cần DB: ưu tiên mock repository bằng `Moq`.
- Với test có thay đổi DB: phải có CheckDB và Rollback.
- Không phụ thuộc dữ liệu thật có sẵn nếu không cần thiết.
- Dữ liệu test nên có prefix riêng như `ut_course_`, `ut_student_`, `ut_teacher_`.

### 6.2. Template test dùng Mock

```csharp
using FluentAssertions;
using Moq;
using project.Models;
using Xunit;

namespace project.Tests.Modules.Courses
{
    public class CourseServiceTests
    {
        private readonly Mock<ICourseRepository> _mockCourseRepository = new();
        private readonly Mock<ICategoryRepository> _mockCategoryRepository = new();
        private readonly Mock<ITeacherRepository> _mockTeacherRepository = new();

        // ------------------------------------------------------------------------------------------------
        // [ID: SERV_COS_01]
        // [Mục đích: Đảm bảo GetCourseByIdAsync trả về DTO đúng khi course tồn tại]
        // ------------------------------------------------------------------------------------------------
        [Fact]
        public async Task GetCourseByIdAsync_ShouldReturnCourseInformation_WhenCourseExists()
        {
            // Arrange
            var courseId = "course-1";
            var expectedCourse = new Course
            {
                Id = courseId,
                Title = "Khóa học C#",
                Category = new Category { Id = "cat-1", Name = "Lập trình" },
                Teacher = new Teacher
                {
                    TeacherId = "teacher-1",
                    User = new User { FullName = "Nguyễn Văn A" }
                }
            };

            _mockCourseRepository
                .Setup(repo => repo.GetCourseByIdAsync(courseId))
                .ReturnsAsync(expectedCourse);

            var service = CreateCourseService();

            // Act
            var result = await service.GetCourseByIdAsync(courseId);

            // Assert
            result.Should().NotBeNull();
            result.Id.Should().Be(courseId);
            result.Title.Should().Be("Khóa học C#");
            result.CategoryName.Should().Be("Lập trình");
            result.TeacherName.Should().Be("Nguyễn Văn A");
        }

        private CourseService CreateCourseService()
        {
            // TODO: truyền đầy đủ mock dependency theo constructor thực tế của CourseService.
            throw new NotImplementedException();
        }
    }
}
```

Lưu ý: phần `CreateCourseService()` phải sửa theo constructor thực tế của `CourseService`.

### 6.3. Template test có CheckDB và Rollback

Dùng khi service thật sự ghi DB, ví dụ:

- Tạo full course
- Cập nhật full course
- Enroll course
- Add review
- Request cancel/refund

```csharp
// ------------------------------------------------------------------------------------------------
// [ID: SERV_COS_05]
// [Mục đích: Tạo full course thành công và xác minh DB có Course/CourseContent/Lesson]
// ------------------------------------------------------------------------------------------------
[Fact]
public async Task AddFullCourseAsync_ShouldCreateCourseContentAndLessons_WhenInputIsValid()
{
    // Arrange
    using var dbContext = GetDatabaseContext();

    var testPrefix = "ut_course_" + Guid.NewGuid().ToString("N")[..8];
    var teacherUserId = testPrefix + "_teacher_user";
    var teacherId = testPrefix + "_teacher";
    var categoryId = testPrefix + "_category";

    try
    {
        // Seed dữ liệu nền cần thiết
        dbContext.Users.Add(new User
        {
            Id = teacherUserId,
            UserName = teacherUserId,
            Email = $"{teacherUserId}@test.com",
            FullName = "Unit Test Teacher"
        });

        dbContext.Teachers.Add(new Teacher
        {
            TeacherId = teacherId,
            UserId = teacherUserId
        });

        dbContext.Categories.Add(new Category
        {
            Id = categoryId,
            Name = "Unit Test Category"
        });

        await dbContext.SaveChangesAsync();

        var service = CreateRealCourseService(dbContext);

        var request = new FullCourseCreateDTO
        {
            // TODO: điền field theo DTO thực tế.
        };

        // Act
        var result = await service.AddFullCourseAsync(request, teacherId);

        // Assert - CheckDB
        result.Should().NotBeNull();

        var savedCourse = await dbContext.Courses
            .Include(c => c.Content)
            .ThenInclude(content => content.Lessons)
            .FirstOrDefaultAsync(c => c.Id == result.Id);

        savedCourse.Should().NotBeNull();
        savedCourse!.TeacherId.Should().Be(teacherId);
        savedCourse.CategoryId.Should().Be(categoryId);
        savedCourse.Content.Should().NotBeNull();
        savedCourse.Content.Lessons.Should().NotBeEmpty();
    }
    finally
    {
        // Rollback - xóa dữ liệu test theo prefix
        var courses = dbContext.Courses
            .Where(c => c.TeacherId == teacherId || c.CategoryId == categoryId)
            .ToList();

        var courseIds = courses.Select(c => c.Id).ToList();
        var contents = dbContext.CourseContents
            .Where(cc => courseIds.Contains(cc.CourseId))
            .ToList();
        var contentIds = contents.Select(cc => cc.Id).ToList();
        var lessons = dbContext.Lessons
            .Where(l => contentIds.Contains(l.CourseContentId))
            .ToList();

        dbContext.Lessons.RemoveRange(lessons);
        dbContext.CourseContents.RemoveRange(contents);
        dbContext.Courses.RemoveRange(courses);

        dbContext.Categories.RemoveRange(dbContext.Categories.Where(c => c.Id == categoryId));
        dbContext.Teachers.RemoveRange(dbContext.Teachers.Where(t => t.TeacherId == teacherId));
        dbContext.Users.RemoveRange(dbContext.Users.Where(u => u.Id == teacherUserId));

        await dbContext.SaveChangesAsync();
    }
}
```

### 6.4. Gợi ý dùng InMemory DB

Nếu muốn rollback đơn giản hơn và vẫn đạt tinh thần CheckDB, có thể dùng InMemory DB:

```csharp
private DBContext CreateInMemoryDbContext()
{
    var options = new DbContextOptionsBuilder<DBContext>()
        .UseInMemoryDatabase("CourseTests_" + Guid.NewGuid())
        .Options;

    return new DBContext(options);
}
```

Ưu điểm:

- Mỗi test có DB riêng.
- Không cần xóa dữ liệu thủ công.
- Dễ đạt rollback vì DB bị dispose sau test.

Nhược điểm:

- Không kiểm tra được đầy đủ hành vi SQL Server thật như constraint/index/transaction.

Khuyến nghị:

- Test logic service bình thường: dùng Mock hoặc InMemory.
- Test yêu cầu chứng minh CheckDB/Rollback cho báo cáo: chọn một vài case quan trọng dùng DB thật hoặc InMemory có assert DB rõ ràng.

---

## 7. Cách chạy unit test

Chạy toàn bộ test backend:

```powershell
cd backend
dotnet test project.Tests/project.Tests.csproj
```

Chạy riêng test module Courses nếu đã tạo thư mục/test class:

```powershell
cd backend
dotnet test project.Tests/project.Tests.csproj --filter "FullyQualifiedName~Courses"
```

Chạy có log chi tiết:

```powershell
cd backend
dotnet test project.Tests/project.Tests.csproj --logger "console;verbosity=detailed"
```

---

## 8. Cách tạo Execution Report cho Excel

### 8.1. Nội dung cần có

Trong sheet Execution Report, ghi:

| Mục | Nội dung |
|---|---|
| Lệnh chạy test | `dotnet test project.Tests/project.Tests.csproj --filter "FullyQualifiedName~Courses"` |
| Ngày chạy | _(điền ngày)_ |
| Người chạy | _(điền tên)_ |
| Tổng số test | Ví dụ: 33 |
| Passed | Ví dụ: 33 |
| Failed | Ví dụ: 0 |
| Skipped | Ví dụ: 0 |
| Kết luận | Passed |
| Screenshot | Dán ảnh màn hình terminal sau khi chạy test |

### 8.2. Cách chụp bằng chứng

Sau khi chạy lệnh, terminal sẽ có dạng:

```text
Passed!  - Failed: 0, Passed: 33, Skipped: 0, Total: 33
```

Chụp màn hình phần này và dán vào Excel.

---

## 9. Cách tạo Code Coverage Report

### 9.1. Chạy test có coverage

```powershell
cd backend
dotnet test project.Tests/project.Tests.csproj --collect:"XPlat Code Coverage"
```

Sau khi chạy, file coverage thường nằm ở:

```text
backend/project.Tests/TestResults/<guid>/coverage.cobertura.xml
```

### 9.2. Tạo HTML report bằng ReportGenerator

Nếu đã cài `reportgenerator`, chạy:

```powershell
cd backend/project.Tests
reportgenerator -reports:"TestResults/**/coverage.cobertura.xml" -targetdir:"CoverageReport" -reporttypes:Html
```

Nếu máy chưa nhận lệnh `reportgenerator`, có thể dùng:

```powershell
dotnet tool install --global dotnet-reportgenerator-globaltool
```

Sau đó mở:

```text
backend/project.Tests/CoverageReport/index.html
```

### 9.3. Nội dung ghi vào sheet Coverage Report

| Mục | Nội dung |
|---|---|
| Tool đo coverage | coverlet.collector + ReportGenerator |
| Lệnh chạy coverage | `dotnet test ... --collect:"XPlat Code Coverage"` |
| Lệnh tạo HTML | `reportgenerator ...` |
| Coverage yêu cầu | 100% |
| Coverage thực tế | _(điền số từ report)_ |
| Screenshot | Dán ảnh dashboard HTML coverage |
| Ghi chú | Nếu chưa đạt 100%, liệt kê file/method chưa được cover và test case bổ sung |

### 9.4. Cách xử lý nếu chưa đạt 100%

1. Mở HTML coverage.
2. Chọn file service Course bị thiếu coverage.
3. Xem dòng màu đỏ/vàng.
4. Bổ sung test case cho nhánh còn thiếu:
   - input null/empty
   - entity không tồn tại
   - user không có quyền
   - trạng thái không hợp lệ
   - happy path
   - exception path
5. Chạy lại coverage.

---

## 10. Project Link trong báo cáo

Trong Excel mục 1.4 ghi URL GitHub trỏ tới thư mục test script.

Ví dụ format:

```text
https://github.com/<org-or-user>/<repo>/tree/<branch>/backend/project.Tests/Modules/Courses
```

Nếu nộp theo commit cụ thể, dùng link dạng:

```text
https://github.com/<org-or-user>/<repo>/commit/<commit-hash>
```

Nên ghi rõ:

```text
Unit test scripts for Module Courses:
backend/project.Tests/Modules/Courses/
```

---

## 11. Checklist công việc cần làm theo thứ tự

### Giai đoạn 1 - Chuẩn bị Excel

- [ ] Tạo sheet `Tools and Libraries`.
- [ ] Tạo sheet `Scope of Testing`.
- [ ] Tạo sheet `CourseService Test Cases`.
- [ ] Tạo sheet `EnrollmentCourseService Test Cases`.
- [ ] Tạo sheet `CourseReviewService Test Cases`.
- [ ] Tạo sheet `LessonService Test Cases`.
- [ ] Tạo sheet `CourseContentService Test Cases`.
- [ ] Tạo sheet `CategoryService Test Cases`.
- [ ] Tạo sheet `Execution Report`.
- [ ] Tạo sheet `Coverage Report`.

### Giai đoạn 2 - Viết test cases trong Excel

- [ ] Copy các test case đề xuất ở mục 5 vào Excel.
- [ ] Điều chỉnh Expected Output theo code service thực tế.
- [ ] Đánh dấu Notes ban đầu là `Not Run`.
- [ ] Sau khi chạy test, đổi Notes thành `PASS` hoặc `FAIL`.

### Giai đoạn 3 - Viết test script

- [ ] Tạo folder `backend/project.Tests/Modules/Courses`.
- [ ] Viết `CourseServiceTests.cs`.
- [ ] Viết `EnrollmentCourseServiceTests.cs`.
- [ ] Viết `CourseReviewServiceTests.cs`.
- [ ] Viết `LessonServiceTests.cs`.
- [ ] Viết `CourseContentServiceTests.cs`.
- [ ] Viết `CategoryServiceTests.cs`.
- [ ] Mỗi test có comment `[ID: ...]`.
- [ ] Các test có thay đổi DB phải có CheckDB và Rollback.

### Giai đoạn 4 - Chạy test

- [ ] Chạy `dotnet test`.
- [ ] Sửa lỗi compile nếu có.
- [ ] Sửa test fail nếu expected chưa đúng.
- [ ] Chụp màn hình kết quả pass/fail.
- [ ] Dán vào Execution Report.

### Giai đoạn 5 - Chạy coverage

- [ ] Chạy `dotnet test --collect:"XPlat Code Coverage"`.
- [ ] Chạy `reportgenerator`.
- [ ] Mở `CoverageReport/index.html`.
- [ ] Chụp màn hình tổng quan coverage.
- [ ] Nếu chưa đạt 100%, bổ sung test cho dòng/nhánh chưa cover.
- [ ] Cập nhật Coverage Report trong Excel.

### Giai đoạn 6 - Hoàn thiện nộp bài

- [ ] Push test script lên GitHub.
- [ ] Dán GitHub URL vào mục Project Link.
- [ ] Kiểm tra Excel có đủ 1.1 đến 1.6.
- [ ] Kiểm tra tất cả Test Case ID trong Excel có test script tương ứng.
- [ ] Kiểm tra test script có comment rõ Test Case ID.
- [ ] Kiểm tra ảnh Execution và Coverage rõ số liệu.

---

## 12. Lưu ý để đạt 100% coverage cho module Courses

Để đạt 100%, mỗi service nên có đủ nhóm test:

1. **Happy path**: dữ liệu hợp lệ, xử lý thành công.
2. **Not found path**: course/lesson/category/student/teacher không tồn tại.
3. **Unauthorized/Forbidden path**: user không có quyền.
4. **Invalid state path**: course chưa publish, enrollment đã canceled/completed, review trùng.
5. **Invalid input path**: DTO null, list rỗng, rating ngoài khoảng, reason rỗng.
6. **Calculation path**: progress, average rating, total pages.
7. **DB write path**: tạo/cập nhật/xóa mềm nếu có.
8. **Rollback path**: dữ liệu DB trở lại như trước test.

Không nên cố test getter/setter của DTO/entity để tăng số lượng test. Coverage phải tập trung vào service code có logic.


