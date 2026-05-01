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
| 1 | Module đã có đủ các trang lỗi phổ biến 401, 403, 404 chưa? | PASS | 0 | |
| 2 | Các route lỗi đã được khai báo trong router chính chưa? | PASS | 0 | `App.jsx` có `/unauthorized`, `/forbidden` và route catch-all `*`. |
| 3 | Route catch-all 404 có được đặt cuối danh sách route để không chặn route hợp lệ không? | PASS | 0 | |
| 4 | Trang 401 có điều hướng người dùng chưa đăng nhập tới luồng đăng nhập phù hợp không? | PASS | 0 | `Unauthorized.jsx` có nút về `/login` và truyền `state.from`. |
| 5 | Trang 403 có thể hiện đúng trường hợp đã đăng nhập nhưng không đủ quyền không? | PASS | 0 | |
| 6 | Trang 404 có cung cấp đường quay lại hoặc các liên kết hữu ích cho người dùng không? | PASS | 0 | Có nút quay lại, về trang chủ và link tới courses/exam/blog/about. |
| 7 | Các guard có thật sự dẫn người dùng tới đúng error page khi lỗi xác thực/phân quyền không? | FAIL | 2 | (1) `PrivateRoute.jsx` redirect user chưa đăng nhập thẳng tới `/login`, vì vậy trang `/unauthorized` gần như không được dùng trong luồng guard. (2) `RequireRole.jsx` cũng redirect user chưa đăng nhập tới `/login` thay vì `/unauthorized`, chưa thống nhất với việc đã xây trang 401. |
| 8 | Cơ chế đọc trạng thái đăng nhập trong `PrivateRoute`, `RequireRole` có nhất quán với auth store không? | FAIL | 2 | (1) `PrivateRoute.jsx` đọc `app_user` và `app_access_token`. (2) `RequireRole.jsx` đọc `auth_user`, trong khi `store/auth.js` dùng `auth_state`, `access_token`, `refresh_token`. Sai key có thể làm user đã đăng nhập vẫn bị điều hướng sai. |
| 9 | Logic phân quyền trong `RequireRole` có bao phủ đúng các role Student, Teacher, Admin không? | PASS | 0 | Logic nhận diện role theo `studentId`, `teacherId`, `isTeacher`, `adminId`, `role` ở mức chấp nhận được. |
| 10 | Các trang lỗi có hiển thị tiếng Việt đúng encoding không? | FAIL | 4 | `Unauthorized.jsx`, `Forbidden.jsx`, `NotFound.jsx`, `ErrorBoundary.jsx` đang có nội dung tiếng Việt bị mojibake như `KhÃ´ng cÃ³ quyá»n`, `Vá» trang chá»§`, `ÄÃ£ xáº£y ra lá»—i`. Người dùng sẽ thấy chữ lỗi nếu file không được sửa encoding. |
| 11 | Nội dung thông báo lỗi có rõ ràng, thân thiện và đúng ngữ cảnh không? | PASS | 0 | Các trang đều có mã lỗi, tiêu đề, mô tả và hành động chính. |
| 12 | Các hành động chính trên error page có hợp lý với từng lỗi không? | PASS | 0 | 401 có đăng nhập/trang chủ; 403 và 404 có quay lại/trang chủ. |
| 13 | Nút “Quay lại” có phương án dự phòng khi người dùng không có lịch sử trình duyệt không? | FAIL | 1 | `Forbidden.jsx` và `NotFound.jsx` dùng `window.history.back()` trực tiếp. Nếu user mở trực tiếp URL lỗi trong tab mới, nút có thể không đưa họ tới trang hữu ích. Nên fallback về `/`. |
| 14 | Error page có tránh làm lộ thông tin kỹ thuật nhạy cảm cho người dùng production không? | PASS | 0 | `ErrorBoundary.jsx` chỉ hiển thị chi tiết lỗi khi `import.meta.env.DEV`. |
| 15 | Error Boundary có bắt lỗi render và cung cấp UI fallback không? | PASS | 0 | `ErrorBoundary.jsx` dùng class component với `getDerivedStateFromError` và `componentDidCatch`. |
| 16 | Error Boundary có cơ chế phục hồi hợp lý sau khi lỗi xảy ra không? | FAIL | 1 | Nút “Thử lại” chỉ reset state của boundary. Nếu lỗi đến từ cùng route/component chưa đổi điều kiện, component có thể crash lại ngay; nên cân nhắc reload route hoặc điều hướng về trang an toàn. |
| 17 | Các trang lỗi có đảm bảo layout responsive trên mobile/desktop không? | PASS | 0 | Dùng `min-h-screen`, `max-w-md`, `px-4`, `flex-col sm:flex-row`, phù hợp màn hình nhỏ. |
| 18 | Các nút/link có vùng bấm đủ lớn và dễ thao tác không? | PASS | 0 | Các CTA dùng padding `px-6 py-3` hoặc tương đương. |
| 19 | Màu sắc, icon và trạng thái lỗi có phân biệt rõ 401/403/404 không? | PASS | 0 | 401 dùng vàng, 403 dùng đỏ, 404 dùng xanh; icon khác nhau. |
| 20 | Các icon trang trí có được xử lý accessibility phù hợp không? | FAIL | 1 | Icon từ `lucide-react` đang không có `aria-hidden="true"` hoặc nhãn thay thế. Vì đã có text mô tả lỗi, nên nên đánh dấu icon là decorative để screen reader không đọc thừa. |
| 21 | Các trang lỗi có heading hierarchy rõ ràng không? | PASS | 0 | Mỗi trang có `h1` mã lỗi và `h2` tiêu đề. |
| 22 | Có sử dụng thẻ semantic/ARIA để thông báo trạng thái lỗi cho công nghệ hỗ trợ không? | FAIL | 1 | Các wrapper error page chưa có `role="alert"`/`aria-live` hoặc landmark phù hợp. Với trang lỗi toàn màn hình, nên thêm semantic để screen reader nhận biết trạng thái. |
| 23 | Error page có tránh dùng `window` trực tiếp trong render khi không cần thiết không? | FAIL | 1 | `Unauthorized.jsx` truyền `state={{ from: window.location.pathname }}` trực tiếp trong render. Với SPA hiện tại vẫn chạy, nhưng kém test-friendly và không phù hợp nếu sau này SSR/pre-render. Nên dùng `useLocation()`. |
| 24 | Các route bảo vệ có tránh nhấp nháy UI hoặc redirect sai trong lúc hydrate auth không? | FAIL | 1 | `PrivateRoute`/`RequireRole` tự đọc localStorage và có loading riêng, không dùng `useAuth().isHydrated`. Có thể lệch trạng thái với store auth và redirect sai khi auth store chưa hydrate xong. |
| 25 | Các trang lỗi có tránh phụ thuộc vào dữ liệu API không cần thiết không? | PASS | 0 | Error pages hiện là UI tĩnh, không gọi API. |
| 26 | Có test component hoặc test route cho error page/route guard không? | FAIL | 1 | Chưa thấy test riêng cho `Unauthorized`, `Forbidden`, `NotFound`, `PrivateRoute`, `RequireRole` trong `frontend/src`. Các luồng 401/403/404 chưa được kiểm thử tự động. |
| 27 | Các error page có nhất quán với phong cách UI chung của ứng dụng không? | PASS | 0 | Giao diện đơn giản, dùng Tailwind và lucide icon, phù hợp với UI hiện tại. |
| 28 | Có tránh trùng lặp component/lặp markup quá nhiều giữa 401/403/404 không? | PASS | 0 | Có lặp layout giữa 3 trang, nhưng phạm vi nhỏ và chưa gây rủi ro bảo trì lớn. |
| 29 | Có route hoặc component lỗi dư thừa gây nhầm lẫn không? | FAIL | 1 | `App.jsx` định nghĩa function `NotFound()` nội bộ nhưng route cuối lại dùng `NotFoundPage` import từ `pages/errors/NotFound`. Component nội bộ này không dùng, dễ gây nhầm khi bảo trì. |
| 30 | Các trang lỗi có đưa người dùng về luồng nghiệp vụ chính của e-learning không? | PASS | 0 | 404 có link tới khóa học, đề thi, blog; 401/403 có login/trang chủ/quay lại. |
| 31 | Khi user sai role, hệ thống có tránh lộ dữ liệu hoặc render trang cấm trước khi redirect không? | PASS | 0 | `RequireRole` kiểm tra trước khi render `Outlet`, nên không render trang con khi không đủ quyền. |
| 32 | Các thông báo lỗi có dùng thuật ngữ thống nhất với hệ thống không? | PASS | 0 | Dùng các thuật ngữ quen thuộc: đăng nhập, quyền truy cập, khóa học, đề thi, blog. |
| 33 | Có xử lý riêng lỗi route không tồn tại và lỗi render runtime không? | PASS | 0 | 404 route dùng `NotFoundPage`, lỗi runtime dùng `ErrorBoundary`. |
| 34 | Error page có tránh redirect loop không? | PASS | 0 | `/login`, `/forbidden`, route `*` không bị bọc trực tiếp bởi guard trong phần error route cuối. |
| 35 | Cấu trúc import và lazy loading của error page có hợp lý không? | PASS | 0 | Error pages được import trực tiếp, phù hợp vì cần dùng cho fallback route và dung lượng nhỏ. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 35 | 23 | 12 | 0 |

| Tổng số Error phát hiện | 18 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: **PHÊ DUYỆT MỘT PHẦN**

Phần Error Page đã có đủ 401/403/404, giao diện rõ ràng, responsive và có Error Boundary cho lỗi runtime. Cần sửa các vấn đề chính trước khi phê duyệt toàn bộ: lỗi encoding tiếng Việt, key localStorage không nhất quán trong route guard, trang 401 chưa được dùng đúng luồng, và bổ sung accessibility/test cơ bản cho error pages.

