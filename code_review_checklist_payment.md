# CHECKLIST CODE REVIEW - MODULE PAYMENT
### Dự án: E-Learning Platform | Ngôn ngữ: C# (.NET 8) | Tích hợp: Sepay Webhook

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | Payment (`PaymentController`, `OrdersController`, `PaymentService`, `OrderService`) |
| **Người review** | _(điền tên)_ |
| **Ngày review** | _(điền ngày)_ |
| **Commit / Branch** | _(điền commit hash hoặc branch)_ |

---

## HƯỚNG DẪN SỬ DỤNG

| Kết quả | Ý nghĩa |
|---|---|
| **PASS** | Không phát hiện lỗi đáng kể |
| **FAIL** | Có >= 1 lỗi ảnh hưởng tới chức năng, bảo mật, dữ liệu hoặc khả năng bảo trì - ghi rõ file, dòng, mô tả lỗi vào cột Ghi chú |
| **NA** | Không áp dụng hoặc không thể trả lời - ghi rõ lý do |

---

## BẢNG CHECKLIST

| STT | Nội dung câu hỏi | PASS/FAIL/NA | Số Error | Ghi chú |
|---|---|---|---|---|
| 1 | Các quy tắc lập trình quy định trong kế hoạch dự án đã được tuân thủ ở mức chấp nhận được chưa? | PASS | 0 | |
| 2 | Comment/tài liệu trong mã nguồn có đủ để hiểu luồng thanh toán chính không? | PASS | 0 | |
| 3 | Các quy ước đặt tên namespace, class, method, biến có nhất quán ở mức chấp nhận được không? | PASS | 0 | |
| 4 | Mã nguồn đã được định dạng đúng cách và dễ đọc chưa? | PASS | 0 | |
| 5 | Các chương trình con dùng chung đã được viết để tránh lặp logic quan trọng chưa? | PASS | 0 | |
| 6 | Có mã nguồn thừa hoặc logic cũ gây ảnh hưởng trực tiếp tới luồng thanh toán không? | FAIL | 1 | `PaymentService.GeneratePaymentQrAsync()` vẫn dùng cơ chế sinh QR bằng `System.Drawing` song song với QR Sepay. Nếu endpoint cũ vẫn được dùng khi deploy Linux/Docker có thể gặp lỗi runtime. |
| 7 | Có method/API endpoint cũ dễ bị gọi nhầm không? | PASS | 0 | |
| 8 | Các đối tượng có khả năng null đã được kiểm tra và xử lý phù hợp chưa? | FAIL | 1 | `PaymentController.GetPaymentQr()` lấy `StudentId` từ claim rồi ném `Exception` nếu thiếu, sau đó trả `BadRequest`; lỗi xác thực nên trả 401/403 rõ ràng hơn. |
| 9 | Việc truy cập object, collection hoặc navigation property có tránh được null reference không? | FAIL | 1 | `PaymentService.GeneratePaymentQrAsync()` truy cập `payment.Order.StudentId` trực tiếp. Nếu navigation `Order` không được load, có thể gây `NullReferenceException`. |
| 10 | Tất cả chỉ số truy cập collection/list có nằm trong phạm vi cho phép không? | PASS | 0 | |
| 11 | Các collection/list có được khởi tạo đầy đủ trước khi sử dụng không? | PASS | 0 | |
| 12 | Các điều kiện rẽ nhánh có chính xác và đúng nghiệp vụ thanh toán không? | PASS | 0 | |
| 13 | Tất cả vòng lặp có điều kiện kết thúc rõ ràng và an toàn không? | PASS | 0 | |
| 14 | Điều kiện kết thúc vòng lặp có thực tế và tránh rủi ro lặp vô hạn không? | PASS | 0 | |
| 15 | Các phép tính tiền tệ có tránh lỗi làm tròn và mất độ chính xác không? | FAIL | 1 | `GenerateSepayQrCode()` làm tròn amount sang `int`, trong khi webhook so sánh với `order.TotalPrice` dạng `decimal`. Cần thống nhất quy tắc làm tròn giữa QR và webhook. |
| 16 | Có câu lệnh nào nằm trong vòng lặp cần đưa ra ngoài để tối ưu rõ rệt không? | FAIL | 1 | `OrderService.CreateOrderAsync()` gọi `_courseRepo.GetCourseByIdAsync()` trong vòng lặp order details, gây N+1 query khi đơn hàng có nhiều khóa học. |
| 17 | Có phần nào trong mã nguồn mà luồng thực thi không bao giờ chạm tới không? | PASS | 0 | |
| 18 | Các câu lệnh if có bị lồng nhau quá sâu, gây khó bảo trì không? | PASS | 0 | |
| 19 | Các tham số truyền giữa Controller - Service - Repository có khớp nhau không? | PASS | 0 | |
| 20 | Có biến, tham số hoặc dependency không sử dụng gây ảnh hưởng chức năng không? | PASS | 0 | |
| 21 | Các đối tượng Order/Payment có được khởi tạo đúng cách trước khi sử dụng không? | PASS | 0 | |
| 22 | Các tài nguyên stream/bitmap/transaction có được giải phóng đúng cách không? | FAIL | 1 | `GeneratePaymentQrAsync()` dùng `System.Drawing.Bitmap`; dù có `using`, API này không phù hợp mặc định trên Linux/Docker và có thể gây lỗi môi trường. |
| 23 | Các truy vấn dữ liệu có hợp lý, tránh truy vấn dư thừa trong luồng webhook không? | FAIL | 1 | `HandleSepayWebhookAsync()` kiểm tra enrollment theo từng `OrderDetail`, có thể tạo N+1 query. Nên load enrollment hiện có theo danh sách course một lần. |
| 24 | Trạng thái lỗi có được kiểm tra sau mỗi thao tác truy cập dữ liệu không? | PASS | 0 | |
| 25 | Transaction có được dùng để đảm bảo toàn vẹn dữ liệu khi xử lý thanh toán không? | PASS | 0 | |
| 26 | Có kiểm tra idempotency khi nhận webhook thanh toán không? | PASS | 0 | |
| 27 | Yêu cầu thời gian phản hồi của webhook có được đáp ứng không? | PASS | 0 | |
| 28 | Có phương án tối ưu hiệu năng rõ ràng cho luồng thanh toán không? | PASS | 0 | |
| 29 | Các kiểm tra dữ liệu rỗng, không tồn tại và định dạng đã được thực hiện chưa? | FAIL | 1 | `HandleSepayWebhookAsync()` format GUID từ nội dung chuyển khoản bằng xử lý chuỗi thủ công. Nên dùng `Guid.TryParse`/validate format để tránh lỗi khi content sai định dạng. |
| 30 | Các thông báo lỗi trả về có rõ ràng, đầy đủ và nhất quán không? | FAIL | 1 | `PaymentController` và `OrdersController` còn xu hướng catch mọi exception rồi trả `BadRequest`, chưa phân biệt 401/403/404/500 rõ ràng. |
| 31 | Các điều kiện lỗi đã được bẫy và xử lý phù hợp chưa? | FAIL | 1 | Các lỗi `UnauthorizedAccessException`, `KeyNotFoundException`, `InvalidOperationException` trong module Payment chưa được map HTTP status nhất quán. |
| 32 | Webhook endpoint có xác thực nguồn gửi hoặc chữ ký từ nhà cung cấp thanh toán không? | FAIL | 1 | `PaymentWebhook()` và `SepayWebhook()` đều `[AllowAnonymous]` nhưng chưa thấy kiểm tra signature/secret từ Sepay. Đây là rủi ro bảo mật quan trọng vì có thể giả webhook để mở enrollment. |
| 33 | Thông tin cấu hình ngân hàng/cổng thanh toán có được lấy từ config thay vì hard code không? | FAIL | 1 | `GetBankInfoForOrderAsync()` có đọc config nhưng vẫn có fallback hardcode như số tài khoản, ngân hàng, tên chủ tài khoản. Nếu thiếu config, hệ thống có thể dùng thông tin sai mà không báo lỗi. |
| 34 | Trong các biểu thức logic xử lý thanh toán, điều kiện có rõ ràng không? | PASS | 0 | |
| 35 | Trong các thao tác dữ liệu, tài nguyên có được mở đúng thời điểm và đóng sau khi hoàn tất không? | PASS | 0 | |
| 36 | Trong khai báo biến và cấu hình, có tránh hard code ở các giá trị nghiệp vụ quan trọng không? | FAIL | 2 | (1) URL `"https://your-payment-gateway.com/pay?...` là placeholder hardcode. (2) Một số magic string như `"in"`, `"paid"`, `"pending"` nên đưa thành constant/enum để tránh sai lệch trạng thái. |
| 37 | Các API trong module đã được bảo vệ bằng xác thực và phân quyền phù hợp chưa? | FAIL | 1 | `OrdersController.CreateOrder()` chỉ `[Authorize]`, chưa giới hạn role Student. Teacher/Admin có thể gọi tạo order nếu có token hợp lệ. |
| 38 | Dữ liệu đầu vào từ webhook bên ngoài có được validate đầy đủ trước khi xử lý không? | FAIL | 2 | (1) Webhook DTO cần validate các trường bắt buộc như content/reference/amount trước khi tạo payment. (2) So sánh số tiền chưa có quy tắc dung sai/làm tròn thống nhất với QR. |
| 39 | Các thông tin nhạy cảm có được cô lập trong cấu hình an toàn thay vì hard code không? | PASS | 0 | |
| 40 | Các luồng xử lý lỗi có ghi log đầy đủ để phục vụ truy vết và debug không? | FAIL | 1 | Một số đoạn dùng `Console.WriteLine()` hoặc không log exception. Nên dùng `ILogger<PaymentService>`/`ILogger<PaymentController>` cho webhook và tạo order. |
| 41 | Các tác vụ nặng hoặc gọi DB có được thiết kế bất đồng bộ khi phù hợp không? | PASS | 0 | |
| 42 | Các thao tác tạo Order và xử lý thanh toán có đảm bảo atomic không? | PASS | 0 | |
| 43 | Module có cơ chế xử lý lỗi thống nhất và phản hồi thân thiện cho client không? | FAIL | 1 | Response format của Payment còn khác một số module khác (`{ message }` thay vì `APIResponse`), và exception mapping chưa thống nhất. |
| 44 | Có kiểm tra trạng thái hợp lệ của Order trước khi cho phép thanh toán không? | FAIL | 1 | `GetBankInfoForOrderAsync()` chỉ chặn order đã `paid`, nhưng chưa thấy chặn rõ các trạng thái `cancelled`/`expired`; nên chỉ cho thanh toán khi order đang `pending`. |
| 45 | Quyền truy cập và trạng thái hợp lệ của người dùng có được kiểm tra trước khi xử lý nghiệp vụ không? | FAIL | 1 | `GeneratePaymentQrAsync()` kiểm tra `payment.Order.StudentId != studentId`, nhưng cần kiểm tra `payment.Order == null` trước để tránh lỗi null và trả đúng lỗi phân quyền. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 27 | 18 | 0 |

| Tổng số Error phát hiện | 19 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: **PHÊ DUYỆT MỘT PHẦN**

Module Payment có luồng tạo order, payment và xử lý webhook khá rõ; transaction và idempotency đã được xử lý ở các điểm quan trọng. Cần sửa trước các vấn đề chính: webhook chưa xác thực chữ ký/secret, phân quyền tạo order chưa giới hạn Student, xử lý lỗi HTTP status chưa thống nhất, và cần chuẩn hóa làm tròn số tiền giữa QR và webhook.

