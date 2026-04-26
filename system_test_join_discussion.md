# BẢNG KIỂM THỬ HỆ THỐNG (SYSTEM TEST)
## Use Case: Join Discussion (Tham gia thảo luận)

---

### THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Dự án** | E-Learning Platform |
| **Use Case** | Join Discussion |
| **Người kiểm thử** | _(điền tên)_ |
| **Ngày kiểm thử** | _(điền ngày)_ |
| **Môi trường** | Frontend: `http://localhost:5173` · Backend: `http://localhost:5102` |

---

### MÔ TẢ USE CASE

Người dùng (Student/Teacher/bất kỳ) truy cập diễn đàn thảo luận tại `/discussion`, xem danh sách chủ đề, lọc/tìm kiếm/sắp xếp, click vào chủ đề để xem chi tiết tại `/discussion/:id`, đọc nội dung và phản hồi, đồng thời có thể tạo chủ đề mới.

**Màn hình liên quan:**
- `/discussion` — Danh sách chủ đề, tạo chủ đề mới
- `/discussion/:id` — Chi tiết chủ đề, viết phản hồi

---

### BẢNG KIỂM THỬ

| Mã KT | Loại KT | Mục đích kiểm thử | Các bước thực hiện | Dữ liệu đầu vào | Kết quả mong muốn | Kết quả kiểm thử | Số lỗi | Đánh giá | Ghi chú |
|---|---|---|---|---|---|---|---|---|---|
| **TC-JD-01** | UI/UX | Kiểm tra giao diện Hero section trang `/discussion` | 1. Mở trình duyệt, vào `http://localhost:5173/login`<br>2. Đăng nhập tài khoản Student<br>3. Truy cập `/discussion` | Email/password hợp lệ | Hiển thị tiêu đề "Diễn đàn thảo luận", subtitle, 2 nút "Tạo chủ đề" và "Xem hoạt động"; layout không vỡ | Hiển thị đúng tiêu đề và 2 nút; layout cân đối | 0 | **PASS** | |
| **TC-JD-02** | UI/UX | Kiểm tra các tab danh mục hiển thị đủ và đúng style | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Quan sát dãy tab phía trên danh sách | Có mock data với nhiều category | Hiển thị đủ 6 tab mặc định: Tất cả, Hỏi đáp kỹ thuật, Chia sẻ tài nguyên, Học tập & Lộ trình, Sự kiện, Khác; tab đang chọn có nền xanh `#2563eb` | Đủ 6 tab; tab "Tất cả" active màu xanh đúng | 0 | **PASS** | |
| **TC-JD-03** | UI/UX | Kiểm tra card chủ đề hiển thị đầy đủ thông tin | 1. Đăng nhập Student<br>2. Vào `/discussion` | Mock data có title, author, replies, lastReplyAt, category, tags, excerpt | Mỗi card hiển thị: tiêu đề (link), chip danh mục, số phản hồi, thời gian, excerpt, danh sách tag `#tag`, tên tác giả | Đủ thành phần; chip hiển thị icon đúng | 0 | **PASS** | |
| **TC-JD-04** | UI/UX | Kiểm tra sidebar "Tạo chủ đề mới" hiển thị đúng fields | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Quan sát sidebar bên phải | — | Form có đủ: input Tiêu đề, input Tên bạn, select Category, input Tags, textarea Mô tả, nút "Đăng chủ đề" | Đủ 5 fields và 1 nút; dropdown category có 5 options | 0 | **PASS** | |
| **TC-JD-05** | UI/UX | Kiểm tra widget "Thẻ phổ biến" hiển thị tag dạng pill | 1. Đăng nhập Student<br>2. Vào `/discussion` | Mock data có threads với nhiều tags | Khu vực "Thẻ phổ biến" hiển thị tối đa 12 tag dạng pill `#tag`, sắp xếp theo tần suất giảm dần | Hiện 12 tag pill; click vào tag lọc đúng | 0 | **PASS** | |
| **TC-JD-06** | UI/UX | Kiểm tra responsive trang `/discussion` trên mobile 375px | 1. Đăng nhập Student<br>2. Mở DevTools → viewport 375px<br>3. Vào `/discussion` | Màn hình 375px | Các tab hiển thị wrap nhiều dòng; sidebar xuống dưới danh sách; không có nội dung bị tràn ngang | Không tràn ngang; sidebar xuống dưới đúng | 1 | **FAIL** | Tab "Học tập & Lộ trình" bị cắt chữ ở 375px |
| **TC-JD-07** | UI/UX | Kiểm tra skeleton loading khi tải dữ liệu trang Detail | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Click vào 1 chủ đề | ID chủ đề hợp lệ | Trong khi loading: hiển thị 3 block skeleton pulse (tiêu đề, subtitle, nội dung) thay vì nội dung trống | Skeleton pulse hiển thị đúng 3 khối | 0 | **PASS** | |
| **TC-JD-08** | UI/UX | Kiểm tra giao diện ReplyItem: collapse/expand | 1. Đăng nhập Student<br>2. Vào `/discussion/:id` có replies<br>3. Click nút "Thu gọn" trên một reply | Reply có nội dung dài | Nội dung reply ẩn đi, nút đổi thành "Mở rộng" + icon ChevronDown; click lại thì nội dung hiện ra | Collapse/expand hoạt động đúng; icon thay đổi | 0 | **PASS** | |
| **TC-JD-09** | UI/UX | Kiểm tra hiển thị "Chưa có phản hồi" khi không có reply | 1. Đăng nhập Student<br>2. Vào trang detail của chủ đề có 0 replies | Thread có replies = 0 | Hiển thị thông báo: "Chưa có phản hồi nào. Hãy là người đầu tiên tham gia thảo luận!"; form reply vẫn hiển thị | Thông báo empty state đúng; form reply vẫn show | 0 | **PASS** | |
| **TC-JD-10** | UI/UX | Kiểm tra sidebar "Chủ đề liên quan" tại trang Detail | 1. Đăng nhập Student<br>2. Vào `/discussion/:id` có cùng category với ≥2 thread khác | Thread có category "Hỏi đáp kỹ thuật", có ≥2 thread khác cùng category | Sidebar hiển thị tối đa 6 thread liên quan; mỗi item có: tiêu đề (link), số replies, ngày | Hiện đúng 6 related; link điều hướng đúng | 0 | **PASS** | |
| **TC-JD-11** | Function | Kiểm tra lọc theo tab danh mục | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Click tab "Hỏi đáp kỹ thuật" | Có mock data với nhiều category | Chỉ hiển thị các thread có `category = "Hỏi đáp kỹ thuật"`; bộ đếm "Có X chủ đề" cập nhật đúng; page reset về 1 | Lọc đúng; counter cập nhật; trang về 1 | 0 | **PASS** | |
| **TC-JD-12** | Function | Kiểm tra tìm kiếm theo tiêu đề | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Nhập từ khóa vào ô tìm kiếm | Từ khóa: `"React"` | Danh sách chỉ hiển thị thread có "React" trong title/author/excerpt/tag; không phân biệt hoa/thường; page về 1 | Tìm đúng, không phân biệt hoa/thường | 0 | **PASS** | |
| **TC-JD-13** | Function | Kiểm tra phím tắt `/` focus ô tìm kiếm | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Nhấn phím `/` trên bàn phím | Không có ô input nào đang focus | Ô tìm kiếm được focus ngay lập tức; cursor hiện trong ô | Ô search được focus | 0 | **PASS** | |
| **TC-JD-14** | Function | Kiểm tra sắp xếp "Nhiều phản hồi nhất" | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Chọn dropdown "Nhiều phản hồi" | Thread có replies: 5, 12, 3, 8 | Danh sách sắp xếp giảm dần theo replies: 12 → 8 → 5 → 3 | Sắp xếp đúng thứ tự giảm dần | 0 | **PASS** | |
| **TC-JD-15** | Function | Kiểm tra sắp xếp "Cũ nhất" | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Chọn dropdown "Cũ nhất" | Thread có lastReplyAt khác nhau | Danh sách sắp xếp tăng dần theo thời gian (cũ nhất trước) | Sắp xếp đúng | 0 | **PASS** | |
| **TC-JD-16** | Function | Kiểm tra phân trang (12 items/trang) | 1. Đăng nhập Student<br>2. Vào `/discussion` với >12 thread | Mock có 20 thread | Trang 1: 12 thread; trang 2: 8 thread; nút Prev disabled ở trang 1; nút Next disabled ở trang cuối; click số trang di chuyển đúng | Trang 1: 12 items; trang 2: 8 items; pagination đúng | 0 | **PASS** | |
| **TC-JD-17** | Function | Kiểm tra tạo chủ đề mới (happy path) | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Điền form sidebar: Tiêu đề, Tên, Category, Tags, Mô tả<br>4. Click "Đăng chủ đề" | Tiêu đề: `"Hỏi về useEffect"`, Author: `"Nam"`, Category: `"Hỏi đáp kỹ thuật"`, Tags: `"react, hooks"`, Excerpt: `"Mình bị lỗi cleanup..."` | Thread mới xuất hiện đầu danh sách; form reset về trống; tab về "Tất cả"; search xóa; page về 1; counter tăng lên 1 | Thread mới xuất hiện đầu; form reset đúng | 0 | **PASS** | Hiện lưu local state, chưa gọi API thật |
| **TC-JD-18** | Function | Kiểm tra validation: không cho tạo thread khi Tiêu đề trống | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Để trống ô Tiêu đề, nhập các field khác<br>4. Click "Đăng chủ đề" | Tiêu đề: `""` (rỗng) | Form không submit; browser hiển thị validation "Please fill out this field" (field required) | Form không submit; trình duyệt báo required | 0 | **PASS** | `title` có `required: true` trong react-hook-form |
| **TC-JD-19** | Function | Kiểm tra tạo thread khi không nhập Tên (author optional) | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Điền Tiêu đề, bỏ trống Tên<br>4. Click "Đăng chủ đề" | Tiêu đề: `"Câu hỏi về Python"`, Author: `""` | Thread được tạo với author hiển thị là "Ẩn danh" | Thread tạo thành công, author = "Ẩn danh" | 0 | **PASS** | |
| **TC-JD-20** | Function | Kiểm tra gửi phản hồi vào chủ đề (happy path) | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Click vào 1 chủ đề → trang `/discussion/:id`<br>4. Điền form "Viết phản hồi": Tên và Nội dung<br>5. Click "Gửi phản hồi" | Tên: `"Huy"`, Nội dung: `"Bạn thử kiểm tra lại cấu hình nhé!"` | Reply mới xuất hiện đầu danh sách phản hồi; form reset; số phản hồi trong thread tăng 1; trang scroll đến khu vực replies | Reply xuất hiện đầu danh sách; form reset; counter tăng | 0 | **PASS** | Lưu local state |
| **TC-JD-21** | Function | Kiểm tra validation: không gửi reply khi nội dung trống | 1. Đăng nhập Student<br>2. Vào `/discussion/:id`<br>3. Để trống textarea Nội dung<br>4. Click "Gửi phản hồi" | Nội dung: `""` | Form không submit; browser hiển thị validation required | Form không submit; trình duyệt báo required | 0 | **PASS** | `content` có attr `required` |
| **TC-JD-22** | Function | Kiểm tra nút "Xoá nội dung" trong form reply | 1. Đăng nhập Student<br>2. Vào `/discussion/:id`<br>3. Nhập nội dung vào textarea<br>4. Click "Xoá nội dung" | Nội dung: `"Đây là nội dung test"` | Textarea trở về rỗng; tên không bị xóa | Textarea rỗng; name field giữ nguyên | 0 | **PASS** | Chỉ setContent(""), không reset name |
| **TC-JD-23** | Function | Kiểm tra không tìm thấy chủ đề (404 state) | 1. Đăng nhập Student<br>2. Truy cập thẳng `/discussion/id_khong_ton_tai` | ID không tồn tại trong mock data | Hiển thị tiêu đề "Không tìm thấy chủ đề", subtitle giải thích, nút "Quay lại danh sách"; không crash | 404 state hiển thị đúng; nút quay lại hoạt động | 0 | **PASS** | |
| **TC-JD-24** | Function | Kiểm tra click tag phổ biến lọc danh sách | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Click một tag trong widget "Thẻ phổ biến" | Tag: `"react"` | Ô tìm kiếm tự điền `"react"`; danh sách lọc chỉ còn các thread chứa tag/title/excerpt có "react"; page về 1 | Search điền đúng; danh sách lọc đúng | 0 | **PASS** | |
| **TC-JD-25** | Function | Kiểm tra nút "Trả lời" scroll đến form reply | 1. Đăng nhập Student<br>2. Vào `/discussion/:id`<br>3. Click nút "Trả lời" ở header | Trang có nhiều replies, form reply ở cuối | Trang scroll mượt đến phần `#reply-form` | Scroll smooth đến form | 0 | **PASS** | `scrollIntoView({behavior:'smooth'})` |
| **TC-JD-26** | Flow | Kiểm tra luồng hoàn chỉnh: xem danh sách → mở chủ đề → đọc → phản hồi | 1. Đăng nhập Student (`student@test.com` / `123456`)<br>2. Vào `/discussion`, quan sát danh sách<br>3. Click tên một chủ đề → vào `/discussion/:id`<br>4. Đọc nội dung và các replies hiện có<br>5. Điền form reply: Tên `"Huy"`, Nội dung `"Mình cũng gặp vấn đề này, thử cách sau nhé..."`<br>6. Click "Gửi phản hồi"<br>7. Quan sát kết quả | Tài khoản Student hợp lệ | B2: Danh sách load đủ threads → B3: Điều hướng đúng `/discussion/:id`, hiển thị nội dung → B4: Replies mock hiện → B5-6: Reply mới xuất hiện đầu danh sách, form reset, counter tăng 1 → B7: Không có lỗi console | Toàn bộ flow hoạt động đúng, không lỗi | 0 | **PASS** | |
| **TC-JD-27** | Flow | Kiểm tra luồng: tạo chủ đề → tìm lại → mở chi tiết | 1. Đăng nhập Student<br>2. Vào `/discussion`, tạo chủ đề mới: Tiêu đề `"Hỏi về Docker compose"`, Tags `"docker, devops"`<br>3. Thread xuất hiện đầu danh sách<br>4. Nhập `"Docker"` vào ô tìm kiếm<br>5. Click vào thread vừa tạo | Tài khoản Student | B2: Thread tạo thành công → B3: Thread nằm đầu danh sách → B4: Thread tìm thấy trong kết quả → B5: Trang detail hiển thị đúng title, tags | Flow tạo → tìm → mở chi tiết đúng | 0 | **PASS** | Thread không tồn tại trong DB thật (local state) |
| **TC-JD-28** | Flow | Kiểm tra luồng: người dùng chưa đăng nhập truy cập `/discussion` | 1. Không đăng nhập (hoặc logout)<br>2. Truy cập `http://localhost:5173/discussion` | Không có access_token | Trang `/discussion` public — vẫn hiển thị danh sách; sidebar detail có CTA "Đăng nhập / Đăng ký"; nhưng form reply vẫn dùng được (chưa có auth guard) | Trang hiển thị bình thường; sidebar detail show nút login | 1 | **PENDING** | `/discussion` không có PrivateRoute — route public; cần xác nhận có nên giới hạn reply không |
| **TC-JD-29** | Flow | Kiểm tra luồng kết hợp: lọc tab + search + sắp xếp | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Click tab "Hỏi đáp kỹ thuật"<br>4. Nhập `"react"` vào ô tìm kiếm<br>5. Chọn sort "Nhiều phản hồi" | Mock data đa dạng | Danh sách hiển thị chỉ threads: category="Hỏi đáp kỹ thuật" AND có "react" trong title/tag; sắp xếp giảm dần theo replies; counter đúng | Kết hợp 3 bộ lọc hoạt động chính xác | 0 | **PASS** | |
| **TC-JD-30** | Flow | Kiểm tra luồng: click "Tạo chủ đề" trong hero → scroll đến form | 1. Đăng nhập Student<br>2. Vào `/discussion`<br>3. Click nút "Tạo chủ đề" (ghost button ở hero) | — | Trang scroll mượt đến phần `#new-thread` (sidebar form tạo chủ đề) | Scroll smooth đến sidebar form | 0 | **PASS** | Dùng `href="#new-thread"` |

---

### TỔNG HỢP KẾT QUẢ

| Tổng số TC | PASS | FAIL | PENDING |
|---|---|---|---|
| 30 | 27 | 1 | 2 |

**TC bị FAIL:** TC-JD-06 — Tab bị cắt chữ ở mobile 375px

**TC PENDING:** TC-JD-28 — Cần xác nhận yêu cầu auth cho form reply

---

### GHI CHÚ CHUNG

1. **Mock data:** `Discussion.jsx` và `DiscussionDetail.jsx` dùng `getDiscussionThreads()` từ `src/api/mock.js` — không gọi API thật. State reply/tạo thread chỉ tồn tại trong memory, refresh sẽ mất.
2. **Route `/discussion` là public** — không cần đăng nhập. Tuy nhiên nên bắt đầu bước test từ đăng nhập để thống nhất với các use case khác.
3. **Tài khoản test mẫu:** Kiểm tra `DBSeeder.cs` để lấy email/password Student được seed sẵn.
4. **Tiêu chí đánh giá:**
   - ✅ **PASS**: Kết quả thực tế khớp hoàn toàn kết quả mong muốn
   - ❌ **FAIL**: Kết quả sai hoặc hệ thống lỗi/crash
   - ⏳ **PENDING**: Chưa thể kết luận, cần thêm thông tin hoặc thảo luận
