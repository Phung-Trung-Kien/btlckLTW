# CHECKLIST CODE REVIEW - FE ERROR PAGE
### Dự án: E-Learning Platform | Công nghệ: React 19 + Vite + React Router | Phạm vi: Error Pages, Route Guard, Error Boundary

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | FE Error Page (`Unauthorized.jsx`, `Forbidden.jsx`, `NotFound.jsx`, `ErrorBoundary.jsx`, `PrivateRoute.jsx`, `RequireRole.jsx`, cấu hình route lỗi trong `App.jsx`) |
| **Người review** | _(điền tên)_ |
| **Ngày review** | _(điền ngày)_ |
| **Commit / Branch** | _(điền commit hash hoặc branch)_ |

---

## HƯỚNG DẪN SỬ DỤNG

| Kết quả | Ý nghĩa |
|---|---|
| **PASS** | Không phát hiện lỗi đáng kể |
| **FAIL** | Có >= 1 lỗi ảnh hưởng tới chức năng, điều hướng, UX, accessibility hoặc khả năng bảo trì - ghi rõ file, dòng, mô tả lỗi vào cột Ghi chú |
| **NA** | Không áp dụng hoặc không thể trả lời - ghi rõ lý do |

---

## BẢNG CHECKLIST

| STT | Nội dung câu hỏi | PASS/FAIL/NA | Số Error | Ghi chú |
|---|---|---|---|---|
| 1 | Các quy tắc lập trình quy định trong kế hoạch dự án đã được tuân thủ ở mức chấp nhận được chưa? | PASS | 0 | |
| 2 | Các tài liệu hướng dẫn trực tiếp trong mã nguồn (comment, JSDoc) có đủ để hiểu mục đích component không? | PASS | 0 | Các file error page đều có comment mô tả 401/403/404 hoặc Error Boundary. |
| 3 | Các quy ước đặt tên component, file, route và biến có nhất quán không? | PASS | 0 | Tên component `Unauthorized`, `Forbidden`, `NotFound`, `PrivateRoute`, `RequireRole` rõ nghĩa. |
| 4 | Mã nguồn đã được định dạng dễ đọc và thống nhất với frontend hiện tại chưa? | PASS | 0 | |
| 5 | Các đoạn UI dùng chung đã được tách hợp lý, tránh lặp code gây rủi ro bảo trì chưa? | PASS | 0 | Có lặp layout giữa 401/403/404 nhưng phạm vi nhỏ, chưa gây rủi ro đáng kể. |
| 6 | Có mã nguồn thừa, component không dùng hoặc logic cũ gây nhầm lẫn không? | FAIL | 1 | `App.jsx` có function `NotFound()` nội bộ nhưng route cuối lại dùng `NotFoundPage` import từ `pages/errors/NotFound`. Component nội bộ không được dùng, dễ gây nhầm khi bảo trì. |
| 7 | Có method/API/component nào được khai báo nhưng không được sử dụng trong luồng Error Page không? | FAIL | 1 | Trang `/unauthorized` đã khai báo nhưng `PrivateRoute` và `RequireRole` đang redirect user chưa đăng nhập về `/login`, nên trang 401 gần như không xuất hiện trong luồng guard thực tế. |
| 8 | Các đối tượng/state có khả năng null hoặc thiếu dữ liệu đã được xử lý phù hợp chưa? | PASS | 0 | `RequireRole` khởi tạo `userRole = []` và kiểm tra `user` trước khi xác định role. |
| 9 | Việc truy cập localStorage và parse JSON có tránh crash UI khi dữ liệu hỏng không? | PASS | 0 | `PrivateRoute` và `RequireRole` đều dùng `try/catch` khi parse JSON từ localStorage. |
| 10 | Các collection/list hoặc mảng role có được kiểm tra an toàn trước khi sử dụng không? | PASS | 0 | `roles` có default `[]`; `userRole.includes()` được dùng trên mảng đã khởi tạo. |
| 11 | Các route lỗi phổ biến 401, 403, 404 đã được triển khai đầy đủ chưa? | PASS | 0 | Có `Unauthorized.jsx`, `Forbidden.jsx`, `NotFound.jsx`. |
| 12 | Các route lỗi đã được khai báo đúng trong router chính chưa? | PASS | 0 | `App.jsx` có `/unauthorized`, `/forbidden` và route catch-all `*`. |
| 13 | Route catch-all 404 có được đặt cuối danh sách route để không chặn route hợp lệ không? | PASS | 0 | |
| 14 | Các điều kiện rẽ nhánh trong route guard có chính xác và đúng nghiệp vụ không? | FAIL | 2 | (1) `PrivateRoute.jsx` đọc `app_user/app_access_token` trong khi auth store dùng `auth_state/access_token`. (2) `RequireRole.jsx` đọc `auth_user`, không nhất quán với `store/auth.js`. User đã đăng nhập có thể vẫn bị redirect sai. |
| 15 | Các tham số/trạng thái truyền giữa route guard và trang login có khớp nhau không? | FAIL | 1 | `Unauthorized.jsx` truyền `state.from` tới `/login`, nhưng `PrivateRoute` hiện redirect trực tiếp tới `/login`; cần đảm bảo login page thật sự đọc và dùng `state.from` thống nhất. |
| 16 | Trang 401 có điều hướng người dùng chưa đăng nhập tới luồng đăng nhập phù hợp không? | PASS | 0 | Có nút `/login` và truyền `state.from`. |
| 17 | Trang 403 có thể hiện đúng trường hợp đã đăng nhập nhưng không đủ quyền không? | PASS | 0 | |
| 18 | Trang 404 có cung cấp đường quay lại hoặc liên kết hữu ích cho người dùng không? | PASS | 0 | Có nút quay lại, về trang chủ và link tới courses/exam/blog/about. |
| 19 | Các hành động chính trên error page có hợp lý với từng lỗi không? | PASS | 0 | 401 có đăng nhập/trang chủ; 403 và 404 có quay lại/trang chủ. |
| 20 | Nút “Quay lại” có phương án dự phòng khi người dùng không có lịch sử trình duyệt không? | FAIL | 1 | `Forbidden.jsx` và `NotFound.jsx` dùng `window.history.back()` trực tiếp. Nếu user mở trực tiếp URL lỗi trong tab mới, nút có thể không đưa họ tới trang hữu ích. Nên fallback về `/`. |
| 21 | Các trang lỗi có hiển thị tiếng Việt đúng encoding không? | FAIL | 4 | `Unauthorized.jsx`, `Forbidden.jsx`, `NotFound.jsx`, `ErrorBoundary.jsx` đang có nội dung tiếng Việt bị mojibake như `KhÃ´ng cÃ³ quyá»n`, `Vá» trang chá»§`, `ÄÃ£ xáº£y ra lá»—i`. |
| 22 | Nội dung thông báo lỗi có rõ ràng, thân thiện và đúng ngữ cảnh không? | PASS | 0 | Mỗi trang có mã lỗi, tiêu đề, mô tả và hành động chính. |
| 23 | Các trang lỗi có tránh làm lộ thông tin kỹ thuật nhạy cảm cho người dùng production không? | PASS | 0 | `ErrorBoundary.jsx` chỉ hiển thị chi tiết lỗi khi `import.meta.env.DEV`. |
| 24 | Error Boundary có bắt lỗi render và cung cấp UI fallback không? | PASS | 0 | Có `getDerivedStateFromError` và `componentDidCatch`. |
| 25 | Error Boundary có cơ chế phục hồi hợp lý sau khi lỗi xảy ra không? | FAIL | 1 | Nút “Thử lại” chỉ reset state của boundary. Nếu lỗi vẫn đến từ cùng component, UI có thể crash lại ngay; nên cân nhắc reload route hoặc điều hướng về trang an toàn. |
| 26 | Các trang lỗi có đảm bảo layout responsive trên mobile/desktop không? | PASS | 0 | Dùng `min-h-screen`, `max-w-md`, `px-4`, `flex-col sm:flex-row`. |
| 27 | Text và nút trong error page có tránh tràn/đè nội dung ở viewport nhỏ không? | PASS | 0 | Layout dùng cột trên mobile và chiều rộng giới hạn, phù hợp. |
| 28 | Các nút/link có vùng bấm đủ lớn và dễ thao tác không? | PASS | 0 | CTA dùng padding đủ lớn (`px-6 py-3` hoặc tương đương). |
| 29 | Màu sắc, icon và trạng thái lỗi có phân biệt rõ 401/403/404 không? | PASS | 0 | 401 màu vàng, 403 màu đỏ, 404 màu xanh; icon khác nhau. |
| 30 | Các icon trang trí có được xử lý accessibility phù hợp không? | FAIL | 1 | Icon từ `lucide-react` chưa có `aria-hidden="true"` hoặc nhãn thay thế. Vì đã có text mô tả lỗi, nên đánh dấu icon là decorative để screen reader không đọc thừa. |
| 31 | Các trang lỗi có heading hierarchy rõ ràng không? | PASS | 0 | Mỗi trang có `h1` mã lỗi và `h2` tiêu đề. |
| 32 | Có sử dụng semantic/ARIA để thông báo trạng thái lỗi cho công nghệ hỗ trợ không? | FAIL | 1 | Wrapper error page chưa có `role="alert"`, `aria-live` hoặc landmark phù hợp. |
| 33 | Error page có tránh dùng browser global trực tiếp trong render khi không cần thiết không? | FAIL | 1 | `Unauthorized.jsx` dùng `window.location.pathname` trực tiếp trong render. Nên dùng `useLocation()` để dễ test và an toàn hơn nếu sau này SSR/pre-render. |
| 34 | Các route bảo vệ có tránh nhấp nháy UI hoặc redirect sai trong lúc hydrate auth không? | FAIL | 1 | `PrivateRoute`/`RequireRole` tự đọc localStorage và không dùng `useAuth().isHydrated`, có thể lệch với auth store và redirect sai. |
| 35 | Có tránh hard code các route quan trọng theo cách khó bảo trì không? | PASS | 0 | Các route `/login`, `/`, `/courses`, `/exam`, `/blog`, `/about` là route cố định, chấp nhận được trong error page. |
| 36 | Các trang lỗi có tránh phụ thuộc vào dữ liệu API không cần thiết không? | PASS | 0 | Error pages là UI tĩnh, không gọi API. |
| 37 | Các thông báo lỗi runtime có được log đủ để debug trong môi trường phát triển không? | PASS | 0 | `ErrorBoundary.jsx` log lỗi bằng `console.error` trong `import.meta.env.DEV`. |
| 38 | Có cơ chế logging/reporting lỗi production cho Error Boundary không? | NA | 0 | Không thấy yêu cầu/tích hợp dịch vụ monitoring như Sentry trong dự án, nên chưa đủ cơ sở đánh FAIL. |
| 39 | Các error page có nhất quán với phong cách UI chung của ứng dụng không? | PASS | 0 | Giao diện dùng Tailwind và lucide icon, phù hợp với phần lớn frontend. |
| 40 | Các route lỗi có tránh redirect loop không? | PASS | 0 | `/login`, `/forbidden`, route `*` không bị bọc trực tiếp bởi guard trong phần error route cuối. |
| 41 | Có xử lý riêng lỗi route không tồn tại và lỗi render runtime không? | PASS | 0 | 404 route dùng `NotFoundPage`, lỗi render dùng `ErrorBoundary`. |
| 42 | Cấu trúc import/lazy loading của error page có hợp lý không? | PASS | 0 | Error pages được import trực tiếp, phù hợp vì nhẹ và cần sẵn cho fallback route. |
| 43 | Các trang lỗi có đưa người dùng về luồng nghiệp vụ chính của e-learning không? | PASS | 0 | 404 có link tới khóa học, đề thi, blog; 401/403 có login/trang chủ/quay lại. |
| 44 | Khi user sai role, hệ thống có tránh render trang cấm trước khi redirect không? | PASS | 0 | `RequireRole` kiểm tra quyền trước khi render `Outlet`. |
| 45 | Có test component hoặc test route cho error page/route guard không? | FAIL | 1 | Chưa thấy test riêng cho `Unauthorized`, `Forbidden`, `NotFound`, `PrivateRoute`, `RequireRole`. Các luồng 401/403/404 chưa được kiểm thử tự động. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 31 | 13 | 1 |

| Tổng số Error phát hiện | 19 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: **PHÊ DUYỆT MỘT PHẦN**

Phần Error Page đã có đủ 401/403/404, giao diện rõ ràng, responsive và có Error Boundary cho lỗi runtime. Cần sửa các vấn đề chính trước khi phê duyệt toàn bộ: lỗi encoding tiếng Việt, localStorage key không nhất quán trong route guard, trang 401 chưa được dùng đúng luồng, và bổ sung accessibility/test cơ bản cho error pages.

