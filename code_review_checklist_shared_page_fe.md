# CHECKLIST CODE REVIEW - FE SHARED PAGE
### Dự án: E-Learning Platform | Công nghệ: React 19 + Vite + TailwindCSS | Phạm vi: Shared/Public Pages

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | FE Shared Page (`frontend/src/pages/shared`: Home, Courses, CourseDetail, Exam, ExamDetail, Blog, Forum, Login, Register, About, BecomeInstructor, Rankings, PublicProfile, Payment...) |
| **Người review** | _(điền tên)_ |
| **Ngày review** | _(điền ngày)_ |
| **Commit / Branch** | _(điền commit hash hoặc branch)_ |

---

## HƯỚNG DẪN SỬ DỤNG

| Kết quả | Ý nghĩa |
|---|---|
| **PASS** | Không phát hiện lỗi đáng kể |
| **FAIL** | Có >= 1 lỗi ảnh hưởng tới chức năng, dữ liệu, UX, accessibility hoặc khả năng bảo trì - ghi rõ file, dòng, mô tả lỗi vào cột Ghi chú |
| **NA** | Không áp dụng hoặc không thể trả lời - ghi rõ lý do |

---

## BẢNG CHECKLIST

| STT | Nội dung câu hỏi | PASS/FAIL/NA | Số Error | Ghi chú |
|---|---|---|---|---|
| 1 | Các quy tắc lập trình của frontend React đã được tuân thủ ở mức chấp nhận được chưa? | PASS | 0 | |
| 2 | Cấu trúc thư mục shared page có được chia theo domain/page rõ ràng không? | PASS | 0 | Các nhóm `Home`, `Courses`, `CourseDetail`, `Blog`, `Forum`, `Exam`, `Login`, `Register` được tách thư mục riêng. |
| 3 | Tên component, file, hook và helper có rõ nghĩa, nhất quán không? | PASS | 0 | Phần lớn tên component rõ nghĩa như `CourseCard`, `SearchBar`, `BlogList`, `HeroSection`, `ExamGrid`. |
| 4 | Mã nguồn có được định dạng dễ đọc, thống nhất ở mức chấp nhận được không? | FAIL | 1 | `Courses.jsx` có đoạn `let mounted = true; (async () => { ... })();` bị thụt lề lệch, làm khó đọc và dễ hiểu nhầm cleanup effect. |
| 5 | Các comment/JSDoc có hữu ích và không gây nhiễu không? | PASS | 0 | Một số file có comment mô tả chức năng; chưa thấy comment sai nghiêm trọng gây hiểu nhầm nghiệp vụ. |
| 6 | Có mã nguồn thừa, import thừa hoặc component cũ gây ảnh hưởng bảo trì không? | FAIL | 2 | (1) `Blog.jsx` import `Header`, `Footer` nhưng không dùng. (2) `Courses.jsx` import `Layout` nhưng không dùng. |
| 7 | Có dữ liệu mock còn nằm trong shared pages cần phân biệt rõ với dữ liệu thật không? | FAIL | 2 | `Payment/data/mockData.js`, `PublicProfile/data/mockProfileData.js`, `Rankings/data/mockData.js`, `Discover/data/courses.js` vẫn nằm trong shared pages. Nếu đây là production flow thì cần gắn nhãn mock rõ hoặc thay bằng API thật. |
| 8 | Các API base URL có được quản lý thống nhất qua cấu hình/env không? | FAIL | 3 | (1) Nhiều file hard-code `http://localhost:5102` trong BlogDetail/Forum/Blog components. (2) Một số nơi dùng `VITE_API_BASE`, nơi khác dùng `VITE_API_URL`, `VITE_API_BASE_URL`. (3) Có cả `utils/http`, `lib/api`, API module riêng và fetch thủ công, gây khó đổi môi trường deploy. |
| 9 | Các lời gọi API có dùng chung client/interceptor để xử lý token/refresh/error không? | FAIL | 2 | (1) Shared pages dùng lẫn `http`, `fetch`, `axios`, `api/*.api.js`. (2) Các request hard-code trong Blog/Forum không tận dụng logic refresh token chung, dễ lệch hành vi khi token hết hạn. |
| 10 | Các key localStorage dùng cho auth/user có nhất quán không? | FAIL | 3 | Dự án dùng lẫn `app_access_token`, `access_token`, `auth_user`, `app_user`, `auth_state`. Ví dụ `Login.jsx` lưu `auth_user`, `BecomInstructor.jsx` đọc/ghi `app_user`, Blog helpers thử nhiều key khác nhau. |
| 11 | Việc parse localStorage có tránh crash khi dữ liệu hỏng không? | PASS | 0 | Nhiều helper có `try/catch` hoặc fallback `JSON.parse(... || "null")`; chấp nhận được. |
| 12 | Các form public như Login/Register/BecomeInstructor có validate input phía client không? | PASS | 0 | `Login.jsx` và `Register.jsx` dùng `react-hook-form` + `zod`; BecomeInstructor kiểm tra `instruction.trim()` trước submit. |
| 13 | Validation form có đầy đủ theo nghiệp vụ không? | FAIL | 2 | (1) `Register.jsx` có `isTeacher`, `employeeCode`, `instruction` nhưng submit chỉ gọi `signup` register thường; chưa thấy luồng tạo teacher thật trong form đăng ký này. (2) `Register.jsx` chưa validate `dateOfBirth` không được ở tương lai và chưa giới hạn độ dài `instruction`. |
| 14 | Luồng đăng nhập có lưu token và điều hướng sau đăng nhập đúng không? | PASS | 0 | `Login.jsx` lưu token qua `setTokens`, lưu `auth_user`, xử lý redirect query/pending và role dashboard. |
| 15 | Luồng đăng nhập/đăng ký có hiển thị lỗi thân thiện không? | PASS | 0 | Có `setError("root", ...)` với thông báo người dùng đọc được. |
| 16 | Các thông báo tiếng Việt trong shared pages có hiển thị đúng encoding không? | FAIL | 5 | Nhiều file đang mojibake: `Login.jsx`, `Register.jsx`, `Courses.jsx`, `CourseDetail.jsx`, `Blog.jsx`, `BecomInstructor.jsx`. Ví dụ `KhÃ´ng thá»ƒ`, `ÄÄƒng kÃ½`, `Giá»›i thiá»‡u`. |
| 17 | Các trạng thái loading/error/empty state có được xử lý trên các trang dữ liệu chính không? | PASS | 0 | Courses, CourseDetail, Blog, ExamDetail, BlogSearch/BlogMy có loading/error components hoặc state. |
| 18 | Loading state có được tách hợp lý giữa các request độc lập không? | FAIL | 1 | `CourseDetail.jsx` dùng chung `loading` cho detail, lessons và reviews. Các effect chạy độc lập có thể set `loading=false` khi request khác chưa xong, gây nhấp nháy hoặc hiển thị chưa đủ dữ liệu. |
| 19 | Cleanup cho async effect có được viết đúng để tránh setState sau unmount không? | FAIL | 1 | `Courses.jsx` hàm `loadCategories()` tạo `mounted` và return cleanup, nhưng `useEffect(() => { loadCategories(); }, [])` không return cleanup đó, nên cleanup không có tác dụng. |
| 20 | Các request có dùng `AbortController` khi phù hợp không? | PASS | 0 | `CourseDetail.jsx` dùng `AbortController` cho các request course detail, lesson, review. |
| 21 | Các thao tác sau submit có tránh reload toàn trang không? | FAIL | 2 | `CourseDetail/SendReview.jsx` và `CourseDetail/EnrollButton.jsx` dùng `window.location.reload()`. Trong SPA nên cập nhật state hoặc invalidate query để tránh mất trạng thái và trải nghiệm giật. |
| 22 | Search/filter/pagination có giữ đúng state người dùng không? | PASS | 0 | Courses và Blog có state query/tag/page và gọi lại API theo tham số. |
| 23 | Pagination có validate page hợp lệ trước khi gọi API không? | FAIL | 1 | `Courses.jsx` truyền `page` từ `Pagination` vào `loadCourses` nhưng chưa thấy guard ở page level để chặn page < 1 hoặc > totalPages. |
| 24 | Dữ liệu API trả về có được normalize trước khi render không? | PASS | 0 | Blog dùng `normPost`; nhiều component có fallback `??`, `||`. |
| 25 | Truy cập nested data có tránh null/undefined crash không? | PASS | 0 | Nhiều chỗ dùng optional chaining/fallback; CourseDetail kiểm tra `intro &&`, `!course return null`. |
| 26 | Có nguy cơ XSS từ render nội dung người dùng không? | PASS | 0 | Trong phạm vi rà soát không thấy `dangerouslySetInnerHTML`; CourseDetail dùng text với `whitespace-pre-line`. |
| 27 | Các hành động cần đăng nhập trên shared pages có kiểm tra auth trước khi gọi API không? | FAIL | 2 | (1) Blog/Forum like/comment/report đọc token từ localStorage nhiều kiểu khác nhau, dễ bỏ sót case token hợp lệ. (2) Một số hành động dùng token `app_access_token` trong khi login cũng lưu `auth_user`, gây lệch với auth store. |
| 28 | BecomeInstructor có xử lý quyền truy cập và refresh token hợp lý không? | PASS | 0 | Có guard `isLoggedIn`, redirect về login, gọi register-teacher rồi refresh token. |
| 29 | BecomeInstructor có tránh log thông tin người dùng ra console không? | FAIL | 1 | `BecomInstructor.jsx` log thông tin user từ localStorage ra console trong `getLoggedInUser()`. Không nên giữ log user trong production. |
| 30 | UI shared pages có responsive tốt trên mobile/desktop không? | PASS | 0 | Phần lớn dùng Tailwind responsive (`grid-cols-1 lg:grid-cols-2`, `px-6 lg:px-12`, `sm:*`). |
| 31 | Text/nút/card có tránh tràn layout ở viewport nhỏ không? | PASS | 0 | Layout dùng grid/flex và max-width khá ổn; chưa thấy lỗi nghiêm trọng từ code review tĩnh. |
| 32 | Các trang public có dùng ảnh/asset phù hợp và có fallback khi ảnh lỗi không? | FAIL | 1 | Một số component card/hero dùng ảnh API hoặc asset nhưng chưa thấy xử lý fallback nhất quán ở toàn bộ shared pages; cần dùng component `Image`/fallback chung nếu có. |
| 33 | Accessibility cho form input, button, link có được đảm bảo không? | FAIL | 2 | Cần kiểm tra thêm labels/aria trong `LoginForm`, `RegisterForm`, search bars. Từ phạm vi đọc nhanh chưa thấy bằng chứng đầy đủ rằng mọi input có label liên kết và trạng thái lỗi accessible. |
| 34 | Các icon trang trí có được xử lý accessibility phù hợp không? | FAIL | 1 | Shared pages dùng nhiều lucide/icon; chưa thấy pattern thống nhất `aria-hidden` cho icon trang trí. |
| 35 | Các thao tác điều hướng có dùng React Router thay vì reload/truy cập window khi phù hợp không? | FAIL | 2 | (1) Một số trang dùng `window.location.reload()`. (2) Nhiều trang dùng `window.scrollTo`, chấp nhận được cho scroll nhưng nên gom qua hook/helper nếu lặp nhiều. |
| 36 | Các public route có được cấu hình đúng trong `App.jsx` không? | PASS | 0 | Các route chính `/`, `/courses`, `/courses/:id`, `/exam`, `/exam/:id`, `/blog`, `/forum`, `/login`, `/register` đều có khai báo. |
| 37 | Có route bị khai báo trùng hoặc dễ gây nhầm không? | FAIL | 2 | (1) `/i/become-instructor` được khai báo trùng trong `App.jsx`. (2) `/i/courses` xuất hiện ở nhiều block route Teacher, gây khó bảo trì và khó xác định guard thực tế. |
| 38 | Các trang public có tránh hiển thị nội dung riêng tư khi chưa đăng nhập không? | PASS | 0 | Các trang yêu cầu auth như blog editor/forum new được bọc `PrivateRoute`; course learning/student routes có guard. |
| 39 | Blog/Forum có xử lý view/like/comment/report theo hướng tránh spam hoặc gọi lặp quá mức không? | FAIL | 1 | BlogDetail/Forum dùng localStorage để ghi lịch sử view, nhưng logic nằm rải rác trong component và hard-code key; cần chuẩn hóa để tránh tăng view lặp hoặc lệch giữa post/forum. |
| 40 | Error handling khi API fail có giữ được UI ổn định không? | PASS | 0 | Nhiều trang set error state và vẫn render fallback/error component thay vì crash. |
| 41 | Có cơ chế retry hoặc hành động phục hồi khi tải dữ liệu thất bại không? | FAIL | 1 | Một số error state chỉ hiển thị thông báo, chưa có nút thử lại rõ ràng ở các danh sách public như Courses/Blog. |
| 42 | Các hàm/helper có được tách khỏi component khi logic đủ phức tạp không? | PASS | 0 | Blog, Forum, Home, BecomeInstructor có `utils/helpers.js`, `constants.js`, component con. |
| 43 | Có test component/hook cho shared pages quan trọng không? | FAIL | 1 | Hiện chỉ thấy test FE cho `DataTable` và `PaymentHistory`; chưa thấy test cho Login/Register/Courses/CourseDetail/Blog/Forum shared pages. |
| 44 | Các trang public có dùng cache/query library nhất quán khi dữ liệu được dùng lại không? | FAIL | 1 | Dự án có `@tanstack/react-query`, nhưng shared pages chủ yếu tự quản lý fetch/loading/error. Điều này vẫn chạy được nhưng làm cache, retry, invalidation không nhất quán. |
| 45 | Tổng thể Shared Page đã sẵn sàng phê duyệt toàn bộ chưa? | FAIL | 1 | Chưa nên phê duyệt toàn bộ do còn các vấn đề chính: API/localStorage không thống nhất, encoding tiếng Việt lỗi, route trùng, reload toàn trang, thiếu test cho luồng public quan trọng. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 24 | 21 | 0 |

| Tổng số Error phát hiện | 39 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: **PHÊ DUYỆT MỘT PHẦN**

Shared Pages có phạm vi chức năng rộng, UI đã chia component tương đối rõ và có xử lý loading/error cho nhiều trang dữ liệu. Cần sửa các vấn đề chính trước khi phê duyệt toàn bộ: thống nhất API client/env, thống nhất localStorage auth key, sửa encoding tiếng Việt, bỏ reload toàn trang, xử lý route trùng và bổ sung test cho các luồng Login/Register/Courses/Blog/Forum.

