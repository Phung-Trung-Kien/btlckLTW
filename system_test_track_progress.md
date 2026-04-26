# BẢNG KIỂM THỬ HỆ THỐNG (SYSTEM TEST)
## Use Case: Track Study Progress (Theo dõi tiến độ học tập của học viên)

---

### THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Dự án** | E-Learning Platform – Hỗ trợ học tập trực tuyến |
| **Use Case** | Track Study Progress |
| **Người kiểm thử** | _(điền tên)_ |
| **Ngày kiểm thử** | _(điền ngày)_ |
| **Phiên bản** | v1.0 |
| **Môi trường** | Backend: `http://localhost:5102` · Frontend: `http://localhost:5173` |

---

### MÔ TẢ USE CASE

Học viên (Student) sau khi đăng nhập có thể theo dõi toàn bộ tiến độ học tập của mình thông qua các màn hình:

- **Dashboard** (`/s/dashboard`): Thống kê tổng quan (số khóa đã ghi danh, đang học, hoàn thành, giờ học), danh sách khóa học kèm progress bar, hoạt động gần đây.
- **Enrollments** (`/s/enrollments`): Danh sách khóa học đã đăng ký, tiến độ từng khóa, tìm kiếm/lọc/sắp xếp.
- **Enrollment Detail** (`/s/enrollments/:courseId`): Chi tiết tiến độ một khóa cụ thể: KPI (%), số bài đã học, lượt thi, thời gian học; timeline các bài học; lịch sử lượt làm bài kiểm tra.
- **History Test** (`/s/historytest`): Lịch sử các bài thi đã làm, điểm số, thời gian, chủ đề; danh sách khóa học kèm tiến độ theo tab.

---

### BẢNG KIỂM THỬ

| Mã KT | Loại KT | Mục đích kiểm thử | Các bước thực hiện | Dữ liệu đầu vào | Kết quả mong muốn | Kết quả kiểm thử | Số lỗi | Đánh giá | Ghi chú |
|---|---|---|---|---|---|---|---|---|---|
| **TC-SP-01** | Function | Kiểm tra trang Dashboard hiển thị đúng số liệu thống kê tổng quan | 1. Đăng nhập tài khoản Student<br>2. Truy cập `/s/dashboard` | Tài khoản Student đã đăng ký ≥1 khóa học, đã hoàn thành ≥1 bài học | Các thẻ thống kê hiển thị đúng: "Đã ghi danh", "Đang học", "Hoàn thành", "Giờ học" với số liệu thực tế từ DB | | | | Dashboard hiện dùng mock data; cần kiểm tra khi API trả dữ liệu thật |
| **TC-SP-02** | UI/UX | Kiểm tra giao diện progress bar trên thẻ khóa học tại Dashboard | 1. Đăng nhập Student<br>2. Vào `/s/dashboard`<br>3. Quan sát phần "Khóa học của tôi" | Student có ≥1 khóa học với tiến độ khác nhau (0%, 50%, 100%) | Mỗi thẻ khóa học hiển thị progress bar đúng màu xanh (`#2563eb`), % hiển thị khớp với DB; thanh bar không bị tràn/vỡ layout | | | | |
| **TC-SP-03** | Function | Kiểm tra nút "Tiếp tục học" trên thẻ khóa học chuyển hướng đúng | 1. Vào `/s/dashboard`<br>2. Click "Tiếp tục học" trên một thẻ khóa học | Khóa học có courseContentId hợp lệ | Hệ thống điều hướng đến `/s/:courseContentId/lesson/:lessonId` (bài học gần nhất chưa hoàn thành) | | | | |
| **TC-SP-04** | Function | Kiểm tra danh sách khóa học đã đăng ký tải đúng tại trang Enrollments | 1. Đăng nhập Student<br>2. Truy cập `/s/enrollments` | Student đã ghi danh 3 khóa học | Danh sách hiển thị đủ 3 khóa, mỗi khóa có: tên, thumbnail, tiến độ %; không có khóa của student khác; loading spinner hiển thị trong khi tải | | | | |
| **TC-SP-05** | Function | Kiểm tra phân trang trên trang Enrollments | 1. Vào `/s/enrollments`<br>2. Student có >10 khóa<br>3. Click sang trang 2 | Student có 15 khóa đã ghi danh | Trang 1 hiển thị 10 khóa, trang 2 hiển thị 5 khóa; nút Prev/Next hoạt động; số trang hiển thị đúng | | | | |
| **TC-SP-06** | Function | Kiểm tra tìm kiếm khóa học theo tên tại trang Enrollments | 1. Vào `/s/enrollments`<br>2. Nhập từ khóa vào ô tìm kiếm<br>3. Click "Tìm" | Từ khóa: `"React"` (Student đã đăng ký khóa có "React" trong tên) | Danh sách chỉ hiển thị các khóa khớp từ khóa; không phân biệt hoa/thường; nếu không tìm thấy hiển thị thông báo "không có kết quả" | | | | |
| **TC-SP-07** | Function | Kiểm tra lọc khóa học theo trạng thái (status) tại Enrollments | 1. Vào `/s/enrollments`<br>2. Chọn filter "Đang học" / "Hoàn thành" / "Tạm dừng" | Student có khóa ở mỗi trạng thái | Danh sách chỉ hiển thị khóa đúng trạng thái được chọn; kết hợp được với ô tìm kiếm | | | | |
| **TC-SP-08** | Function | Kiểm tra sắp xếp danh sách khóa học theo tiêu chí | 1. Vào `/s/enrollments`<br>2. Chọn sort "Mới nhất" / "Tiến độ cao nhất" | Student có ≥3 khóa với tiến độ khác nhau | Danh sách sắp xếp đúng thứ tự theo tiêu chí; khi đổi sort, danh sách cập nhật ngay | | | | |
| **TC-SP-09** | UI/UX | Kiểm tra trạng thái loading khi tải danh sách Enrollments | 1. Vào `/s/enrollments`<br>2. Quan sát khi dữ liệu đang tải | Kết nối mạng bình thường | Loading spinner / skeleton hiển thị trong khi chờ API; sau khi có data, spinner biến mất và danh sách hiện ra | | | | Code có `await new Promise(resolve => setTimeout(resolve, 2000))` giả lập delay |
| **TC-SP-10** | Function | Kiểm tra hiển thị lỗi khi API Enrollments thất bại | 1. Ngắt kết nối backend hoặc mock lỗi 500<br>2. Vào `/s/enrollments` | API trả lỗi 500 hoặc timeout | Hiển thị thông báo lỗi: "Không tải được danh sách khóa học. Vui lòng thử lại sau."; không crash trang | | | | |
| **TC-SP-11** | Function | Kiểm tra KPI tiến độ (%) trong trang Enrollment Detail | 1. Đăng nhập Student<br>2. Vào `/s/enrollments/:courseId`<br>3. Quan sát 4 thẻ KPI | Student đã học 3/10 bài của khóa | Thẻ "Tiến độ" hiển thị 30%; thẻ "Bài đã học" hiển thị "3/10"; thẻ "Lượt làm bài" hiển thị số lần thi; thẻ "Thời gian học" hiển thị giờ:phút | | | | |
| **TC-SP-12** | UI/UX | Kiểm tra progress bar tổng và vòng tròn radial tại Enrollment Detail | 1. Vào `/s/enrollments/:courseId` | Khóa học có tiến độ 65% | Progress bar ngang hiển thị 65% màu xanh; vòng tròn conic-gradient xoay đúng 65%; số % hiển thị bên trong vòng tròn | | | | |
| **TC-SP-13** | Function | Kiểm tra timeline bài học (Nhật ký theo bài học) tại Enrollment Detail | 1. Vào `/s/enrollments/:courseId`<br>2. Quan sát phần "Nhật ký theo bài học" | Khóa có 5 bài: 3 đã hoàn thành, 2 chưa | - Bài đã hoàn thành: icon ✓ màu xanh lá + timestamp "Hoàn thành DD/MM/YYYY"<br>- Bài chưa hoàn thành: icon ○ màu xám + "Chưa hoàn thành"<br>- Đường nối timeline hiển thị đúng thứ tự | | | | |
| **TC-SP-14** | Function | Kiểm tra hiển thị lịch sử lượt làm bài kiểm tra tại Enrollment Detail | 1. Vào `/s/enrollments/:courseId` có bài thi đã nộp | Student đã làm 2 lần bài kiểm tra | Bảng "Lượt làm bài kiểm tra" hiển thị: tên đề, ngày giờ, điểm %; điểm ≥70% màu xanh lá, <70% màu vàng; có nút "Chi tiết" dẫn đến `/s/results/:attemptId` | | | | |
| **TC-SP-15** | Function | Kiểm tra xuất CSV lịch sử lượt làm bài tại Enrollment Detail | 1. Vào `/s/enrollments/:courseId`<br>2. Click nút "Export CSV" | Có ≥1 lượt làm bài | File CSV tải về máy với tên `enrollment_{courseId}_me_attempts.csv`; nội dung gồm header: attemptId, examTitle, date, duration, scorePct, status và dữ liệu đúng | | | | |
| **TC-SP-16** | Function | Kiểm tra fallback khi không tìm thấy dữ liệu Enrollment Detail | 1. Truy cập `/s/enrollments/courseId_khong_ton_tai` | courseId không tồn tại trong DB và không có trong localStorage | Hiển thị thông báo lỗi: "Không tải được chi tiết ghi danh..." + link "Về 'Khoá học của tôi'"; không crash; không hiển thị dữ liệu rỗng | | | | |
| **TC-SP-17** | UI/UX | Kiểm tra badge trạng thái enrollment (active/completed/paused/refunded) | 1. Vào Enrollment Detail của từng trạng thái | 4 khóa học với 4 trạng thái khác nhau | - active: badge xanh lá (`bg-emerald-100`)<br>- completed: badge tím (`bg-indigo-100`)<br>- paused: badge vàng (`bg-amber-100`)<br>- refunded: badge đỏ (`bg-rose-100`) | | | | |
| **TC-SP-18** | Function | Kiểm tra trang History Test hiển thị tab "Kết quả luyện tập" | 1. Đăng nhập Student<br>2. Vào `/s/historytest`<br>3. Click tab "Kết quả luyện tập" | Student đã làm ≥1 bài thi | Hiển thị: tổng số bài đã làm, chủ đề gần đây, ngày làm gần nhất; danh sách các bài thi với tên, ngày, kết quả (X/Y câu + %), thời gian làm; nút "Xem chi tiết" hoạt động | | | | |
| **TC-SP-19** | Function | Kiểm tra trang History Test hiển thị tab "Khóa học" | 1. Vào `/s/historytest`<br>2. Click tab "Khóa học" | Student đã ghi danh ≥1 khóa | Danh sách khóa học với: tên, số bài đã/tổng, progress bar đúng %; nút "Tiếp tục học" và "Xem chi tiết" điều hướng đúng; nếu chưa có khóa hiển thị empty state | | | | |
| **TC-SP-20** | UI/UX | Kiểm tra giao diện empty state khi Student chưa đăng ký khóa nào | 1. Đăng nhập tài khoản Student mới (chưa có enrollment)<br>2. Vào `/s/enrollments` | Tài khoản Student mới, 0 khóa học | Trang hiển thị empty state: thông báo "Bạn chưa đăng ký khóa học nào" + nút "Khám phá khóa học" dẫn đến `/courses`; không hiển thị danh sách rỗng trơ | | | | |
| **TC-SP-21** | Flow | Kiểm tra luồng hoàn chỉnh: học bài → tiến độ cập nhật trên Dashboard | 1. Vào `/s/dashboard`, ghi nhận % tiến độ của khóa A<br>2. Vào học và hoàn thành 1 bài học của khóa A<br>3. Quay lại `/s/dashboard` | Khóa A có 10 bài, đang ở 20% (2/10 bài hoàn thành) | Sau khi hoàn thành bài thứ 3, Dashboard hiển thị 30%; LessonProgress được ghi vào DB; tiến độ đồng bộ giữa tất cả các màn hình | | | | Cần verify API `LessonProgress` được gọi khi hoàn thành bài |
| **TC-SP-22** | Flow | Kiểm tra luồng: nộp bài thi → kết quả xuất hiện trong Enrollment Detail | 1. Làm và nộp một bài thi trong khóa học<br>2. Vào Enrollment Detail của khóa đó<br>3. Xem bảng "Lượt làm bài kiểm tra" | Bài thi mới được nộp (ExamAttemp + SubmissionExam) | Lượt thi mới xuất hiện trong bảng với điểm số đúng; số "Lượt làm bài" trong KPI tăng lên 1; nút "Chi tiết" dẫn đến trang kết quả đúng | | | | |
| **TC-SP-23** | Flow | Kiểm tra luồng xác thực: truy cập `/s/dashboard` khi chưa đăng nhập | 1. Không đăng nhập (hoặc đã logout)<br>2. Truy cập trực tiếp `/s/dashboard` | Không có access_token trong localStorage | Hệ thống redirect đến trang `/login`; sau khi đăng nhập thành công, redirect về `/s/dashboard` | | | | Kiểm tra PrivateRoute + RequireRole("Student") |
| **TC-SP-24** | Flow | Kiểm tra luồng xác thực: tài khoản Teacher truy cập `/s/dashboard` | 1. Đăng nhập tài khoản Teacher<br>2. Truy cập `/s/dashboard` | Token hợp lệ nhưng role = "Teacher" | Hệ thống redirect đến `/unauthorized` hoặc `/forbidden`; không hiển thị nội dung Dashboard của Student | | | | |
| **TC-SP-25** | UI/UX | Kiểm tra responsive layout của Dashboard trên màn hình mobile (375px) | 1. Mở DevTools → đặt viewport 375px<br>2. Vào `/s/dashboard` | Màn hình 375px (iPhone SE) | - 4 thẻ thống kê xuống 2 cột (grid-cols-2)<br>- Danh sách khóa học 1 cột<br>- Sidebar phải chuyển xuống dưới<br>- Không có nội dung bị tràn ngang | | | | |
| **TC-SP-26** | UI/UX | Kiểm tra responsive của Enrollment Detail trên tablet (768px) | 1. Viewport 768px<br>2. Vào `/s/enrollments/:courseId` | Màn hình 768px (iPad) | KPI grid chuyển sang 2 hoặc 4 cột phù hợp; aside sticky nằm đúng; không có overflow ngang | | | | |
| **TC-SP-27** | Function | Kiểm tra "Tiếp tục học" tại Enrollment Detail điều hướng đúng | 1. Vào `/s/enrollments/:courseId`<br>2. Click "Tiếp tục học" | courseId hợp lệ, có ít nhất 1 bài học chưa hoàn thành | Điều hướng đến `/s/learning/:courseId` hoặc `/s/:courseContentId/lesson/:lessonId` mở bài học gần nhất chưa hoàn thành | | | | |
| **TC-SP-28** | Function | Kiểm tra link "Trang khoá học" tại Enrollment Detail | 1. Vào `/s/enrollments/:courseId`<br>2. Click "Trang khoá học" hoặc "Trang public" | courseId hợp lệ | Điều hướng đến `/courses/:courseId`; trang public của khóa hiển thị đúng | | | | |
| **TC-SP-29** | Function | Kiểm tra phần "Hoạt động gần đây" tại Dashboard | 1. Vào `/s/dashboard`<br>2. Xem phần ActivityFeed | Student đã hoàn thành bài học, đăng ký khóa mới | Hiển thị các hoạt động theo thứ tự thời gian mới nhất trước; mỗi mục có icon ✓, mô tả hoạt động, thời gian tương đối ("2 giờ trước", "Hôm qua") | | | | Dashboard hiện dùng mock data |
| **TC-SP-30** | UI/UX | Kiểm tra chức năng scroll ngang "Tiếp tục học" tại Dashboard | 1. Vào `/s/dashboard`<br>2. Click nút "‹" và "›" tại phần tiếp tục học | Có ≥4 khóa trong lịch sử học gần đây | Danh sách cuộn mượt 280px mỗi lần click; nút hoạt động đúng hướng; không có lỗi console | | | | |

---

### TỔNG HỢP KẾT QUẢ

| Tổng số test case | Passed | Failed | Blocked | Chưa thực hiện |
|---|---|---|---|---|
| 30 | | | | |

---

### GHI CHÚ CHUNG

1. **Dữ liệu test:** Sử dụng tài khoản Student được seed sẵn bởi `DBSeeder.cs`. Kiểm tra file `Data/DBSeeder.cs` để lấy thông tin tài khoản mẫu.
2. **Mock data:** `Dashboard.jsx` hiện dùng mock data cứng (không gọi API thật). Các TC-SP-01, TC-SP-29, TC-SP-30 cần lưu ý điều này — đánh dấu kết quả dựa trên UI mock.
3. **API cần thiết:**
   - `GET /api/enrollments/student` — danh sách khóa học đã ghi danh
   - `GET /api/courses/{courseId}/enrollments` — chi tiết enrollment
   - `GET /api/lesson-progress/student` — tiến độ từng bài học
   - `GET /api/exam-attempts/student` — lịch sử bài thi
4. **Tiêu chí đánh giá:**
   - ✅ **Pass**: Kết quả thực tế khớp hoàn toàn kết quả mong muốn
   - ⚠️ **Partial**: Đúng chức năng nhưng có lỗi UI nhỏ
   - ❌ **Fail**: Kết quả thực tế sai hoặc hệ thống lỗi/crash
   - 🚫 **Blocked**: Không thể thực hiện (do bug khác chặn)
