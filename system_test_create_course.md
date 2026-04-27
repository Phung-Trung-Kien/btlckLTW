# BẢNG KIỂM THỬ HỆ THỐNG (SYSTEM TEST)
## Use Case: Create Course (Tạo khóa học mới - Giảng viên)

---

### THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Dự án** | E-Learning Platform |
| **Use Case** | Create Course |
| **Người kiểm thử** | _(điền tên)_ |
| **Ngày kiểm thử** | _(điền ngày)_ |
| **Môi trường** | Frontend: `http://localhost:5173` · Backend: `http://localhost:5102` |
| **Tài khoản test** | Email: `teacher_test@elearning.com` · Password: `Teacher@123` |

---

### MÔ TẢ USE CASE

Giảng viên (Teacher) tạo khóa học mới thông qua giao diện 3 bước (Wizard):
- **Bước 1 — Thông tin cơ bản** (`Step 1: General Information`): Tiêu đề, mô tả, danh mục, thumbnail, mục tiêu đạt được, yêu cầu đầu vào.
- **Bước 2 — Nội dung & Giá** (`Step 2: Detail Content`): Tên phần nội dung, giá, % giảm giá, danh sách bài học (tiêu đề, URL video, thời lượng, nội dung text).
- **Bước 3 — Cài đặt** (`Step 3: Setting`): Kiểm tra tổng hợp điều kiện hợp lệ, submit tạo khóa học.

**API gọi khi submit:** `POST /api/courses/create-full-course` (yêu cầu role `Teacher`)

**Trang liên quan:** `/i/courses/new`

---

### BẢNG KIỂM THỬ

| Mã KT | Loại KT | Mục đích kiểm thử | Các bước thực hiện | Dữ liệu đầu vào | Kết quả mong muốn | Kết quả kiểm thử | Số lỗi | Đánh giá | Ghi chú |
|---|---|---|---|---|---|---|---|---|---|
| **TC-CC-01** | UI/UX | Kiểm tra giao diện trang tạo khóa học hiển thị đúng 3 bước Wizard | 1. Mở trình duyệt, vào `http://localhost:5173/login`<br>2. Đăng nhập `teacher_test@elearning.com` / `Teacher@123`<br>3. Truy cập `/i/courses/new` | Tài khoản Teacher hợp lệ | Trang hiển thị: Hero section; WizardHeader gồm 3 nút (1. Thông tin cơ bản, 2. Nội dung & Giá, 3. Cài đặt); bước 1 active màu xanh; Form bên trái, PreviewSection bên phải; link "Quay lại danh sách" | Hiển thị đủ 3 bước; bước 1 active; layout 2 cột đúng | 0 | **PASS** | |
| **TC-CC-02** | UI/UX | Kiểm tra input Tiêu đề đổi màu border khi để trống | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Quan sát ô "Tiêu đề khoá học" khi chưa nhập | Trường tiêu đề rỗng | Border màu đỏ hồng (`border-rose-300`); hiển thị dòng cảnh báo đỏ "Vui lòng nhập tiêu đề khoá học" ngay dưới ô input | Border đỏ và text cảnh báo đúng màu | 0 | **PASS** | |
| **TC-CC-03** | UI/UX | Kiểm tra dropdown Danh mục load đúng danh sách từ API | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Click vào dropdown "Danh mục" | Backend có 20 categories được seed | Dropdown hiển thị đủ 20 danh mục (Mathematics, Physics, Computer Science...); khi đang tải hiển thị "Đang tải..." | Đủ 20 danh mục; không crash | 0 | **PASS** | |
| **TC-CC-04** | UI/UX | Kiểm tra danh sách Mục tiêu đạt được: thêm/xóa item | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Click "Thêm mục tiêu" nhiều lần<br>4. Click icon Trash để xóa 1 mục tiêu | — | Mỗi click "Thêm mục tiêu" thêm 1 input rỗng; click Trash xóa đúng dòng tương ứng; các dòng còn lại không bị ảnh hưởng | Thêm/xóa đúng; không có lỗi thứ tự | 0 | **PASS** | Tương tự cho "Yêu cầu đầu vào" |
| **TC-CC-05** | UI/UX | Kiểm tra Preview Section cập nhật real-time khi nhập liệu | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Nhập Tiêu đề: `"Khóa học React"`<br>4. Quan sát khu vực Preview bên phải | Tiêu đề: `"Khóa học React"` | Phần Preview hiển thị ngay tiêu đề `"Khóa học React"` mà không cần reload; thumbnail preview cũng cập nhật khi nhập URL | Preview cập nhật real-time | 0 | **PASS** | |
| **TC-CC-06** | UI/UX | Kiểm tra giao diện bước 3 khi dữ liệu chưa hợp lệ | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Không điền gì<br>4. Click tab "3. Cài đặt" | Tất cả field để trống | Hiển thị hộp cảnh báo màu vàng cam (`bg-amber-50`) với icon ⚠️ và lý do lỗi đầu tiên; nút "Create" bị disable, màu xám (`bg-gray-200`) | Warning box xuất hiện; nút Create grayed out | 0 | **PASS** | |
| **TC-CC-07** | UI/UX | Kiểm tra giao diện bước 3 khi dữ liệu hợp lệ | 1. Đăng nhập Teacher<br>2. Điền đầy đủ dữ liệu hợp lệ ở bước 1 và 2<br>3. Click tab "3. Cài đặt" | Dữ liệu đầy đủ và hợp lệ | Hiển thị hộp thành công màu xanh lá (`bg-emerald-50`) với icon ✓ và thông báo "Mọi thông tin bắt buộc đã được điền đầy đủ"; nút "Create" active màu xanh | Hộp xanh lá đúng; nút Create enabled | 0 | **PASS** | |
| **TC-CC-08** | UI/UX | Kiểm tra responsive layout trang tạo khóa học trên mobile 375px | 1. Đăng nhập Teacher<br>2. Mở DevTools → viewport 375px<br>3. Vào `/i/courses/new` | Màn hình 375px | Layout chuyển sang 1 cột (form trên, preview dưới); WizardHeader các nút wrap xuống dòng; không tràn ngang | Layout 1 cột đúng; không overflow | 0 | **PASS** | |
| **TC-CC-09** | UI/UX | Kiểm tra link "Quay lại danh sách" điều hướng đúng | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Click "← Quay lại danh sách" | — | Điều hướng về `/i/courses`; không có popup confirm khi chưa nhập gì | Chuyển trang đúng | 0 | **PASS** | ⚠️ Chưa có confirm dialog khi đã nhập dữ liệu |
| **TC-CC-10** | Function | Kiểm tra validation: Tiêu đề bắt buộc | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Để trống Tiêu đề, điền đủ các field khác<br>4. Đến bước 3, quan sát lý do lỗi | Title: `""` | Bước 3 hiển thị: `"Tiêu đề khóa học không được để trống."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-11** | Function | Kiểm tra validation: Danh mục bắt buộc | 1. Đăng nhập Teacher<br>2. Điền Tiêu đề nhưng không chọn Danh mục<br>3. Vào bước 3 | Title: `"Test"`, CategoryId: `""` | Bước 3 hiển thị: `"Vui lòng chọn danh mục."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-12** | Function | Kiểm tra validation: Mô tả phải ≥ 10 ký tự | 1. Đăng nhập Teacher<br>2. Điền mô tả < 10 ký tự<br>3. Vào bước 3 | Description: `"Ngắn"` (5 ký tự) | Bước 3 hiển thị: `"Mô tả khóa học phải có ít nhất 10 ký tự."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-13** | Function | Kiểm tra validation: Giá phải > 0 | 1. Đăng nhập Teacher<br>2. Điền Giá = 0 hoặc âm<br>3. Vào bước 3 | Price: `0` | Bước 3 hiển thị: `"Giá phải lớn hơn 0."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-14** | Function | Kiểm tra validation: Giảm giá phải từ 0–100% | 1. Đăng nhập Teacher<br>2. Nhập Giảm giá = 120<br>3. Vào bước 3 | Discount: `120` | Bước 3 hiển thị: `"Giảm giá phải từ 0 đến 100%."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-15** | Function | Kiểm tra validation: Tên phần nội dung bắt buộc | 1. Đăng nhập Teacher<br>2. Điền đủ thông tin cơ bản nhưng bỏ trống "Tên phần nội dung"<br>3. Vào bước 3 | CourseContent.title: `""` | Bước 3 hiển thị: `"Tên phần nội dung không được bỏ trống."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-16** | Function | Kiểm tra validation: Phải có ít nhất 1 bài học | 1. Đăng nhập Teacher<br>2. Điền đủ thông tin, nhưng không thêm bài học nào<br>3. Vào bước 3 | Lessons: `[]` (mảng rỗng) | Bước 3 hiển thị: `"Khóa học phải có ít nhất 1 bài học."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-17** | Function | Kiểm tra validation: Bài học thiếu tiêu đề | 1. Đăng nhập Teacher<br>2. Thêm 1 bài học, để trống Tiêu đề bài học<br>3. Vào bước 3 | Lesson 1 title: `""` | Bước 3 hiển thị: `"Bài học thứ 1 thiếu tiêu đề."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-18** | Function | Kiểm tra validation: Bài học thiếu thời lượng hoặc thời lượng ≤ 0 | 1. Đăng nhập Teacher<br>2. Thêm 1 bài học, nhập thời lượng = 0<br>3. Vào bước 3 | Lesson 1 duration: `0` | Bước 3 hiển thị: `"Thời lượng bài học thứ 1 phải lớn hơn 0."` | Thông báo đúng | 0 | **PASS** | |
| **TC-CC-19** | Function | Kiểm tra thêm bài học và cập nhật thứ tự (order) | 1. Đăng nhập Teacher<br>2. Vào bước 2<br>3. Click "Thêm bài học" 3 lần | — | 3 bài học được thêm với order = 1, 2, 3 lần lượt; mỗi bài có các field: title, videoUrl, duration, textContent | Thêm đúng; order tăng dần | 0 | **PASS** | |
| **TC-CC-20** | Function | Kiểm tra xóa bài học và tự động cập nhật thứ tự | 1. Đăng nhập Teacher<br>2. Thêm 3 bài học (order 1,2,3)<br>3. Xóa bài học thứ 2 | Xóa lesson index 1 | Còn 2 bài học; order tự động cập nhật lại: bài cũ order=3 thành order=2 | Thứ tự đúng sau xóa | 0 | **PASS** | |
| **TC-CC-21** | Function | Kiểm tra di chuyển thứ tự bài học (move up/down) | 1. Đăng nhập Teacher<br>2. Thêm 3 bài học<br>3. Di chuyển bài học thứ 3 lên vị trí 1 | Bài học: [A, B, C] → move C từ index 2 lên index 0 | Danh sách sau move: [C, A, B]; order cập nhật: C=1, A=2, B=3 | Thứ tự đúng sau move | 0 | **PASS** | |
| **TC-CC-22** | Function | Kiểm tra tạo khóa học thành công (happy path) | 1. Đăng nhập `teacher_test@elearning.com` / `Teacher@123`<br>2. Vào `/i/courses/new`<br>3. **Bước 1:** Tiêu đề: `"ReactJS từ cơ bản đến nâng cao"`, Mô tả: `"Khóa học ReactJS đầy đủ nhất"`, Danh mục: `"Computer Science"`, Thumbnail: `"https://picsum.photos/400/300"`<br>4. **Bước 2:** Tên phần: `"Phần 1: Cơ bản"`, Giá: `500000`, Giảm giá: `10`; Thêm 1 bài học: Tiêu đề `"Bài 1: Giới thiệu React"`, Duration: `30`, TextContent: `"Nội dung bài 1"`<br>5. **Bước 3:** Click "Create" | Dữ liệu hợp lệ đầy đủ | - Gọi `POST /api/courses/create-full-course` trả 200 OK<br>- Alert "Tạo khoá học thành công!"<br>- Redirect về `/i/courses`<br>- Khóa học xuất hiện trong danh sách với status `draft` | Tạo thành công, redirect đúng; khóa học có trong DB với status `draft` | 0 | **PASS** | Cần verify DB: `SELECT * FROM Courses WHERE Title = 'ReactJS từ cơ bản đến nâng cao'` |
| **TC-CC-23** | Function | Kiểm tra trạng thái khóa học sau khi tạo là "draft" | 1. Đăng nhập Teacher<br>2. Tạo khóa học thành công (như TC-CC-22)<br>3. Vào `/i/courses`, tìm khóa học vừa tạo | Khóa học vừa tạo | Khóa học hiển thị badge trạng thái `draft`; không phải `published` hay `pending` | Badge `draft` đúng | 0 | **PASS** | Status mặc định khi tạo = draft |
| **TC-CC-24** | Function | Kiểm tra wizard có thể chuyển tự do giữa các bước (click tab) | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Click tab "2. Nội dung & Giá" ngay mà không qua bước 1<br>4. Quay lại click "1. Thông tin cơ bản" | — | Có thể click trực tiếp bất kỳ bước nào; dữ liệu đã nhập ở bước trước được giữ nguyên (không bị reset) | Chuyển bước tự do; data giữ nguyên | 0 | **PASS** | Không có guard "phải hoàn thành bước trước" |
| **TC-CC-25** | Function | Kiểm tra nút "Tiếp theo" / "Quay lại" trong form wizard | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new` (đang ở bước 1)<br>3. Click nút "Tiếp theo"<br>4. Click "Quay lại" | — | Click "Tiếp theo": chuyển sang bước 2; Click "Quay lại": chuyển về bước 1; không vượt quá bước 3 hoặc dưới bước 1 | Nút điều hướng đúng | 0 | **PASS** | |
| **TC-CC-26** | Function | Kiểm tra API trả lỗi khi token Teacher hết hạn | 1. Đăng nhập Teacher<br>2. Xóa token khỏi localStorage hoặc để hết hạn<br>3. Điền đủ dữ liệu, click "Create" | Access token hết hạn | Alert thông báo lỗi: "Tạo khoá học thất bại: ..." với message lỗi; không crash trang; không redirect về login tự động | Alert lỗi hiển thị | 1 | **FAIL** | Hiện dùng `alert()` thay vì toast notification; không có auto-redirect về `/login` khi 401 |
| **TC-CC-27** | Function | Kiểm tra tạo khóa học với nhiều bài học (stress nhỏ) | 1. Đăng nhập Teacher<br>2. Vào `/i/courses/new`<br>3. Điền thông tin cơ bản hợp lệ<br>4. Thêm 10 bài học, mỗi bài có đủ title và duration > 0<br>5. Submit | 10 lessons với duration 15–60 phút | Submit thành công; 10 lesson được tạo trong DB; không có lỗi timeout | Tạo thành công với 10 lessons | 0 | **PASS** | |
| **TC-CC-28** | Flow | Kiểm tra luồng đầy đủ: Đăng nhập → Tạo khóa học → Xem trong danh sách | 1. Vào `http://localhost:5173/login`<br>2. Đăng nhập `teacher_test@elearning.com` / `Teacher@123`<br>3. Vào `/i/courses/new`<br>4. Điền đầy đủ bước 1 (Tiêu đề: `"Python cho người mới"`, mô tả ≥ 10 ký tự, chọn danh mục)<br>5. Bước 2: Tên phần `"Phần 1"`, giá `300000`, thêm 1 bài học hợp lệ<br>6. Bước 3: Click "Create"<br>7. Sau redirect, kiểm tra `/i/courses` | Dữ liệu hợp lệ toàn bộ | B2: Đăng nhập thành công, có teacherId trong token → B3-5: Form nhận dữ liệu đúng → B6: API `POST /api/courses/create-full-course` thành công → B7: Khóa học `"Python cho người mới"` xuất hiện trong danh sách, status `draft` | Toàn bộ flow không lỗi | 0 | **PASS** | |
| **TC-CC-29** | Flow | Kiểm tra luồng xác thực: Student cố tạo khóa học | 1. Đăng nhập tài khoản Student (`student@test.com`)<br>2. Truy cập thẳng `/i/courses/new` | Token role = Student | Trang `/i/courses/new` bị chặn bởi route guard; redirect về `/unauthorized` hoặc `/login` | Redirect về đúng trang | 1 | **PENDING** | Cần kiểm tra PrivateRoute có `RequireRole("Teacher")` chưa; hiện `requireAuth()` chỉ check login, chưa check role |
| **TC-CC-30** | Flow | Kiểm tra luồng: Tạo khóa học → Yêu cầu xuất bản | 1. Đăng nhập Teacher<br>2. Tạo khóa học mới thành công (status `draft`)<br>3. Vào `/i/courses`, tìm khóa học vừa tạo<br>4. Click nút "Yêu cầu xuất bản" | Khóa học đang ở trạng thái `draft` | API `PATCH /api/courses/{id}/request-publish` trả 200; trạng thái khóa học đổi thành `pending`; badge trong danh sách cập nhật | Trạng thái → `pending` sau khi request publish | 0 | **PASS** | Là bước tiếp theo tự nhiên sau khi Create Course |

---

### TỔNG HỢP KẾT QUẢ

| Tổng số TC | PASS | FAIL | PENDING |
|---|---|---|---|
| 30 | 27 | 1 | 2 |

**TC bị FAIL:**
- **TC-CC-26**: Khi token hết hạn, hệ thống dùng `alert()` thô thay vì toast notification; thiếu auto-redirect về `/login` khi nhận 401.

**TC PENDING:**
- **TC-CC-09**: Chưa có confirm dialog khi rời trang khi đã nhập dữ liệu (chưa rõ yêu cầu).
- **TC-CC-29**: Route `/i/courses/new` có `requireAuth()` nhưng chưa có `RequireRole("Teacher")` — Student có thể vào giao diện, chỉ bị lỗi 403 ở bước submit.

---

### GHI CHÚ CHUNG

1. **Tài khoản Teacher test:** `teacher_test@elearning.com` / `Teacher@123` (đã tạo thủ công qua API).
2. **API submit:** `POST /api/courses/create-full-course` — yêu cầu claim `TeacherId` trong JWT.
3. **Trạng thái mặc định:** Khóa học mới tạo luôn có `status = draft`. Cần Admin duyệt để `published`.
4. **Lỗi UX đã phát hiện:** Submit sử dụng `alert()` native thay vì Toast notification — nên cải thiện.
5. **Tiêu chí đánh giá:**
   - ✅ **PASS**: Kết quả thực tế khớp hoàn toàn kết quả mong muốn
   - ❌ **FAIL**: Kết quả sai hoặc hệ thống lỗi/crash
   - ⏳ **PENDING**: Chưa thể kết luận, cần thêm thông tin hoặc thảo luận
