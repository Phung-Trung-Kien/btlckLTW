# CHECKLIST CODE REVIEW — MODULE PAYMENT
### Dự án: E-Learning Platform | Ngôn ngữ: C# (.NET 8) | Tích hợp: Sepay Webhook

---

## THÔNG TIN CHUNG

| Mục | Nội dung |
|---|---|
| **Module được review** | Payment (PaymentController, OrdersController, PaymentService, OrderService) |
| **Người review** | _(điền tên)_ |
| **Ngày review** | _(điền ngày)_ |
| **Commit / Branch** | _(điền commit hash hoặc branch)_ |

---

## HƯỚNG DẪN SỬ DỤNG

| Kết quả | Ý nghĩa |
|---|---|
| **PASS** | Không phát hiện lỗi |
| **FAIL** | Có ≥ 1 lỗi — ghi rõ file, dòng, mô tả lỗi vào cột Ghi chú |
| **NA** | Không áp dụng hoặc không thể trả lời — ghi lý do |

---

## BẢNG CHECKLIST

| STT | Nội dung câu hỏi | PASS/FAIL/NA | Số Error | Ghi chú |
|---|---|---|---|---|
| 1 | Các quy tắc lập trình quy định trong kế hoạch dự án đã được tuân thủ chưa? | FAIL | 3 | (1) `OrdersController.cs` (L7): namespace là `project.Modules.Payment.Controller` (thiếu 's') nhưng file nằm trong folder `Payments` — không nhất quán với module khác. (2) `OrderService.cs` (L4-5): import `project.Modules.Payment.DTOs` và `project.Modules.Payment.Service.Interfaces` (thiếu 's') — không khớp namespace thực tế. (3) `OrdersController.cs` (L21-25): XML comment bị thụt lề sai (dùng 4 spaces thay vì căn theo class). |
| 2 | Các tài liệu hướng dẫn trực tiếp trong mã nguồn (comment, XML comment) có đầy đủ và cập nhật không? | FAIL | 2 | (1) `PaymentService.GeneratePaymentQrAsync()` (L25): không có XML comment trong khi các method khác cùng file đều có comment nội bộ. (2) `PaymentController` (L92): comment nhúng URL webhook ngrok cố định `ngrok-free.dev` — đây là URL dev tạm thời, không nên để trong source code chính thức vì sẽ hết hạn. |
| 3 | Các quy ước đặt tên (namespace, class, method, biến) có tuân thủ kế hoạch quản lý cấu hình không? | FAIL | 2 | (1) Tên entity `Orders` (số nhiều) không nhất quán với quy ước đặt tên entity trong C# (thường là số ít: `Order`). `OrderService.cs` (L34): `var order = new Orders`. (2) Folder tên `Controller` (số ít) trong khi các module khác dùng `Controllers` (số nhiều). |
| 4 | Mã nguồn đã được định dạng đúng cách và thống nhất trong toàn bộ module chưa? | FAIL | 1 | `OrdersController.cs` (L21-25): XML comment của `CreateOrder` không dùng cùng mức thụt lề với phần còn lại của class (thụt vào 1 level thay vì 2 level). Khác biệt với `GetOrderById` (L47-51) được format đúng. |
| 5 | Các chương trình con dùng chung (method/service dùng lại) đã được viết để tránh lặp logic ở nhiều nơi chưa? | FAIL | 1 | Logic format GUID thành chuỗi có dấu gạch ngang được viết trùng lặp 2 lần: `PaymentService.ExtractOrderIdFromContent()` (L240) và `HandleSepayWebhookAsync()` (L138) — cùng đoạn `Substring` nên gộp vào một private method `FormatGuidWithDashes(string)`. |
| 6 | Có mã nguồn thừa, mã không sử dụng hoặc logic đã lỗi thời không? | FAIL | 1 | `PaymentService.GeneratePaymentQrAsync()` (L34-44): Sử dụng `System.Drawing` (QRCoder + Bitmap) để sinh QR — tuy nhiên đây là API không còn được hỗ trợ trên Linux/Docker. Module đã có `GenerateSepayQrCode()` sinh URL QR từ Sepay (L283). Hai cơ chế song song gây dư thừa, endpoint `/qr` cũ (L23) nên được đánh dấu `[Obsolete]`. |
| 7 | Có method, API endpoint hoặc service nào được khai báo nhưng không được gọi tới không? | FAIL | 1 | `PaymentService.HandleWebhookAsync()` (L59) và endpoint `POST /api/Payment/webhook` (L69): Đây là webhook generic cũ, đã bị thay thế bởi `HandleSepayWebhookAsync()` và `/webhook/sepay`. Endpoint cũ vẫn còn tồn tại và có thể bị gọi nhầm hoặc khai thác. |
| 8 | Các đối tượng có khả năng null đã được kiểm tra và xử lý phù hợp chưa? | FAIL | 2 | (1) `PaymentController.GetPaymentQr()` (L32): `User.FindFirst("StudentId")?.Value ?? throw new Exception(...)` — ném `Exception` thay vì trả về `Unauthorized()`, dẫn đến catch block trả 400 BadRequest thay vì 401 Unauthorized. (2) `PaymentService.HandleSepayWebhookAsync()` (L183-201): vòng lặp `foreach (var detail in orderDetails)` — `detail.CourseId` có thể null nếu dữ liệu DB không nhất quán, chưa có null check. |
| 9 | Việc truy cập object, collection hoặc kết quả truy vấn có tránh được lỗi null reference không? | FAIL | 1 | `PaymentService.GeneratePaymentQrAsync()` (L31): `payment.Order.StudentId` — `payment.Order` được truy cập trực tiếp mà không kiểm tra null. Nếu EF Core không eager-load navigation property `Order`, sẽ ném NullReferenceException. |
| 10 | Tất cả các chỉ số truy cập collection/list có nằm trong phạm vi cho phép không? | PASS | 0 | |
| 11 | Các collection/list có được khởi tạo đầy đủ trước khi sử dụng không? | PASS | 0 | `order.OrderDetails.Add(...)` (L54): EF Core tự khởi tạo collection navigation property — chấp nhận được. |
| 12 | Tất cả các điều kiện rẽ nhánh (if/switch) có chính xác và đúng nghiệp vụ không? | FAIL | 1 | `HandleWebhookAsync()` (L68-69): `if (dto.Status != "success") throw new Exception(...)` — so sánh string cứng `"success"` không dùng `StringComparison.OrdinalIgnoreCase`. Nếu cổng thanh toán gửi `"Success"` hoặc `"SUCCESS"` thì bị từ chối sai. |
| 13 | Tất cả các vòng lặp có điều kiện kết thúc rõ ràng và an toàn không? | PASS | 0 | |
| 14 | Điều kiện để kết thúc vòng lặp có thực tế và tránh rủi ro lặp vô hạn không? | PASS | 0 | |
| 15 | Các phép tính số học liên quan đến tiền tệ có được kiểm tra tránh lỗi làm tròn và mất độ chính xác không? | FAIL | 2 | (1) `OrderService.CreateOrderAsync()` (L52): `priceToUse = course.Price * (1 - course.DiscountPrice.Value / 100m)` — dùng `decimal` đúng. Tuy nhiên nếu `DiscountPrice` = 100 thì `priceToUse = 0`, không có kiểm tra giá âm hay bằng 0. (2) `GenerateSepayQrCode()` (L288): `(int)Math.Round(amount)` — làm tròn số tiền trước khi gửi lên Sepay, nhưng `HandleSepayWebhookAsync()` (L157) so sánh `dto.TransferAmount < order.TotalPrice` với `decimal` chưa làm tròn — có thể dẫn đến từ chối thanh toán hợp lệ do sai lệch làm tròn. |
| 16 | Có câu lệnh nào nằm trong vòng lặp mà có thể đưa ra ngoài để tối ưu không? | FAIL | 1 | `OrderService.CreateOrderAsync()` (L43-61): vòng lặp `foreach (var detail in dto.OrderDetails)` gọi `_courseRepo.GetCourseByIdAsync(detail.CourseId)` từng lần — N lần gọi DB cho N course. Nên load toàn bộ course 1 lần bằng `WHERE id IN (...)`. |
| 17 | Có phần nào trong mã nguồn mà luồng thực thi không bao giờ chạm tới (dead code) không? | PASS | 0 | |
| 18 | Các câu lệnh if có bị lồng nhau quá sâu, gây khó đọc và khó bảo trì không? | PASS | 0 | Logic rõ ràng, các method được tổ chức hợp lý. |
| 19 | Các tham số truyền giữa các tầng (Controller – Service – Repository) có khớp nhau không? | PASS | 0 | |
| 20 | Có biến, tham số hoặc dependency nào được khai báo nhưng không sử dụng không? | FAIL | 1 | `OrderService.cs` (L12, L20): `IPaymentRepository _paymentRepo` được inject và gọi để `AddAsync` và `SaveChangesAsync` cho Payment. Tuy nhiên `PaymentService` cũng inject `IPaymentRepository`. Ranh giới trách nhiệm không rõ — `OrderService` đang tạo `Payment` entity, chức năng nên thuộc `PaymentService`. |
| 21 | Các đối tượng và dữ liệu có được khởi tạo đúng cách trước khi sử dụng không? | FAIL | 1 | `OrderService.CreateOrderAsync()` (L34-41): `order.TotalPrice = 0` được khởi tạo đúng. Tuy nhiên `order.Id` chưa được gán explicit — phụ thuộc vào EF Core auto-generate sau `AddAsync`. Sau đó `payment.OrderId = order.Id` (L70) được dùng ngay — nếu `SaveChangesAsync` chưa được gọi thì `order.Id` có thể là null/empty với một số cấu hình EF. |
| 22 | Các tài nguyên (DB context, stream, connection) có được giải phóng tại tất cả các điểm thoát không? | FAIL | 1 | `PaymentService.GeneratePaymentQrAsync()` (L38-44): dùng `using var` đúng cho QRCodeGenerator, QRCode, MemoryStream. Tuy nhiên `bitmap` (L41) là `System.Drawing.Bitmap` — trên môi trường Linux/non-Windows, `System.Drawing` không được hỗ trợ mặc định, sẽ ném `PlatformNotSupportedException`. |
| 23 | Các truy vấn dữ liệu có được thiết kế hợp lý, tránh truy vấn dư thừa và tận dụng index khi cần không? | FAIL | 2 | (1) `HandleSepayWebhookAsync()` (L145-147): `_dbContext.Orders.Include(o => o.Payments).FirstOrDefaultAsync(o => o.Id == orderId)` — load cả `Payments` navigation nhưng không dùng đến trong luồng chính. (2) `HandleSepayWebhookAsync()` (L179-201): sau khi load `orderDetails`, vòng lặp gọi DB `FirstOrDefaultAsync` kiểm tra enrollment từng course — N+1 query. Nên dùng một query load tất cả enrollment của student trong đơn hàng. |
| 24 | Trạng thái lỗi có được kiểm tra và xử lý sau mỗi thao tác truy cập dữ liệu không? | PASS | 0 | |
| 25 | Việc khóa hoặc transaction có được thực hiện đúng cách để đảm bảo tính toàn vẹn dữ liệu khi xử lý thanh toán không? | PASS | 0 | `HandleWebhookAsync()`, `HandleSepayWebhookAsync()`, `CreateOrderAsync()` đều dùng `using var transaction = await _dbContext.Database.BeginTransactionAsync()` với rollback trong catch. |
| 26 | Có kiểm tra idempotency (chống xử lý trùng lặp) khi nhận webhook thanh toán không? | PASS | 0 | `HandleWebhookAsync()` (L72-73) và `HandleSepayWebhookAsync()` (L153-154): cả hai đều kiểm tra `order.Status == "paid"` → return true sớm, tránh xử lý trùng. |
| 27 | Các yêu cầu về thời gian phản hồi của module có được đáp ứng không? | FAIL | 1 | `HandleSepayWebhookAsync()`: Sepay yêu cầu phản hồi nhanh (thường < 5s) hoặc sẽ retry. Vòng lặp N+1 query enrollment (câu 23) có thể khiến webhook xử lý chậm khi đơn hàng nhiều khóa học, dẫn đến Sepay timeout và retry → tạo payment trùng lặp. |
| 28 | Có phương án nào tốt hơn để cải thiện hiệu năng và thời gian phản hồi không? | FAIL | 1 | `OrderService.CreateOrderAsync()` (L43-61): N+1 query lấy course trong vòng lặp (câu 16). Nên dùng `_dbContext.Courses.Where(c => courseIds.Contains(c.Id)).ToListAsync()` một lần trước vòng lặp để giảm số lần gọi DB từ N xuống 1. |
| 29 | Các kiểm tra dữ liệu rỗng, dữ liệu không tồn tại và lỗi định dạng đã được thực hiện chưa? | FAIL | 1 | `HandleSepayWebhookAsync()` (L136-140): logic format GUID từ 32 ký tự sang 36 ký tự được thực hiện thủ công bằng `Substring` — nên dùng `Guid.Parse(orderId)` hoặc `Guid.TryParse()` để validate và format, tránh lỗi nếu chuỗi không đúng format GUID. |
| 30 | Các thông báo lỗi trả về có rõ ràng, đầy đủ và nhất quán không? | FAIL | 2 | (1) Toàn bộ `PaymentController`: catch block trả `BadRequest(new { message = ex.Message })` cho mọi exception kể cả `UnauthorizedAccessException` và `KeyNotFoundException` — sai HTTP status code. (2) `OrdersController.GetOrderById()` (L68-71): `UnauthorizedAccessException` từ Service bị catch bởi `catch (Exception ex)` và trả 400 BadRequest thay vì 401. |
| 31 | Tất cả các điều kiện lỗi đã được bẫy và xử lý phù hợp chưa? | FAIL | 1 | `PaymentController` (L36-39, L60-63, L80-83): tất cả catch block đều dùng `return BadRequest(...)` bất kể loại exception. Cần phân biệt: `KeyNotFoundException` → 404, `UnauthorizedAccessException` → 401, `InvalidOperationException` → 400, `Exception` → 500. |
| 32 | Webhook endpoint có xác thực chữ ký (signature) từ nhà cung cấp thanh toán không? | FAIL | 1 | `SepayWebhook()` (L111) và `PaymentWebhook()` (L69): cả hai đều `[AllowAnonymous]` và không kiểm tra signature hay secret key từ Sepay. Bất kỳ ai biết URL webhook đều có thể POST dữ liệu giả → tự động enroll vào khóa học mà không thanh toán. Đây là lỗ hổng bảo mật nghiêm trọng. |
| 33 | Thông tin cấu hình ngân hàng có được lấy từ config thay vì hard code không? | FAIL | 1 | `GetBankInfoForOrderAsync()` (L259-261): có dùng `_configuration["Sepay:BankAccount"] ?? "0972229142"` — fallback value `"0972229142"`, `"MB"`, `"NGUYEN VAN A"` vẫn là hardcode. Nếu config bị thiếu, hệ thống dùng thông tin mặc định không chính xác mà không có cảnh báo. |
| 34 | Trong các biểu thức logic xử lý thanh toán, điều kiện có rõ ràng và tránh kết hợp phức tạp không? | PASS | 0 | |
| 35 | Trong các thao tác với dữ liệu, tài nguyên có được mở đúng thời điểm và đóng sau khi hoàn tất không? | PASS | 0 | Transaction và stream đều dùng `using` block. |
| 36 | Trong việc khai báo biến và cấu hình, có tránh lạm dụng global/static và tránh hard code không? | FAIL | 3 | (1) `PaymentService.cs` (L36): URL `"https://your-payment-gateway.com/pay?..."` là placeholder hardcode không hoạt động. (2) `PaymentController` comment (L92): URL ngrok `https://unmetered-elvera-perennially.ngrok-free.dev/...` là URL dev tạm thời hardcode trong source. (3) `HandleSepayWebhookAsync()` (L111): `"in"` là magic string để kiểm tra loại giao dịch — nên là hằng số có tên `TRANSFER_TYPE_IN`. |
| 37 | Các API trong module đã được bảo vệ bằng cơ chế xác thực và phân quyền chưa? | FAIL | 1 | `OrdersController.CreateOrder()` (L27): `[Authorize]` không chỉ định role — Teacher hay Admin cũng có thể tạo Order. Nên thêm `[Authorize(Roles = "Student")]`. Tương tự `GetOrderById` (L53) và các endpoint payment. |
| 38 | Dữ liệu đầu vào từ webhook bên ngoài có được validate đầy đủ trước khi xử lý không? | FAIL | 2 | (1) `SepayWebhookDto`: không rõ có `[Required]` annotation không, nhưng nếu `dto.Content` null thì `ExtractOrderIdFromContent(null)` trả null → throw exception đúng, nhưng `dto.ReferenceCode` null sẽ tạo Payment với `TransactionId = null`. (2) `HandleSepayWebhookAsync()` (L157): chỉ kiểm tra `dto.TransferAmount < order.TotalPrice` mà không có dung sai (tolerance) — thanh toán thừa vài đồng thì OK, nhưng thanh toán đúng bằng đơn vị integer sau làm tròn có thể bị từ chối. |
| 39 | Các thông tin nhạy cảm có được cô lập trong cấu hình an toàn thay vì hard code không? | FAIL | 1 | `PaymentService.GeneratePaymentQrAsync()` (L36): URL payment gateway với `transactionId` và `amount` được nhúng trực tiếp trong URL string — nếu URL này là real gateway thì đây là lỗ hổng lộ thông tin. `IConfiguration` đã được inject nhưng không dùng để cấu hình gateway URL. |
| 40 | Các luồng xử lý lỗi có ghi log đầy đủ để phục vụ truy vết và debug không? | FAIL | 5 | (1) `PaymentService.HandleSepayWebhookAsync()` (L103-108): dùng `Console.WriteLine()` thay vì `ILogger` — log không được thu thập bởi hệ thống log production (Serilog/NLog). (2) `PaymentController.SepayWebhook()` (L131-133): cũng dùng `Console.WriteLine()` trong catch. (3) `HandleWebhookAsync()`, `OrderService` các catch block: không có bất kỳ log nào. Cần inject `ILogger<PaymentService>` và dùng `_logger.LogError(ex, ...)`. |
| 41 | Các tác vụ nặng hoặc gọi dịch vụ bên ngoài có được thiết kế bất đồng bộ khi phù hợp không? | PASS | 0 | Tất cả method đều dùng async/await. Gọi DB qua EF Core async. |
| 42 | Các thao tác tạo Order và xử lý thanh toán có đảm bảo atomic (all-or-nothing) không? | PASS | 0 | `CreateOrderAsync()`, `HandleWebhookAsync()`, `HandleSepayWebhookAsync()` đều wrap trong transaction với rollback trong catch — đảm bảo atomicity. |
| 43 | Module có cơ chế xử lý lỗi thống nhất và phản hồi thân thiện cho client không? | FAIL | 1 | Tất cả catch block trong `PaymentController` đều trả `BadRequest(new { message = ex.Message })` — không có middleware xử lý exception tập trung. Cần thống nhất dùng `APIResponse` (như các module khác) hoặc global exception handler. Hiện tại response format `{ message }` khác với `APIResponse { status, message, data }` của các module Course, Exam. |
| 44 | Có kiểm tra trạng thái hợp lệ của Order trước khi cho phép thanh toán không? | FAIL | 1 | `GetBankInfoForOrderAsync()` (L256-257): chỉ kiểm tra `order.Status == "paid"` để từ chối. Nhưng không kiểm tra status `"cancelled"` hay `"expired"` — nếu order đã bị hủy thì vẫn trả về thông tin ngân hàng để thanh toán. Cần kiểm tra `order.Status == "pending"` mới cho phép. |
| 45 | Quyền truy cập và trạng thái hợp lệ của người dùng có được kiểm tra trước khi xử lý nghiệp vụ không? | FAIL | 1 | `PaymentService.GeneratePaymentQrAsync()` (L31-32): kiểm tra `payment.Order.StudentId != studentId` đúng, nhưng nếu `payment.Order` null (do không eager-load) thì NullReferenceException xảy ra trước khi có thể kiểm tra quyền. Nên kiểm tra null trước: `if (payment.Order == null || payment.Order.StudentId != studentId)`. |

---

## TỔNG HỢP KẾT QUẢ

| Tổng số câu | PASS | FAIL | NA |
|---|---|---|---|
| 45 | 14 | 31 | 0 |

| Tổng số Error phát hiện | 40 |
|---|---|

---

## ĐÁNH GIÁ CHUNG

### Kết luận: ❌ **TỪ CHỐI** (Reject — Required Changes Before Re-review)

---

### Đánh giá chi tiết

**Ưu điểm:**
- Luồng thanh toán chính (tạo Order → sinh QR → nhận webhook → enroll) được thiết kế rõ ràng và có tài liệu hóa tốt (XML comment đầy đủ ở PaymentController, file explain.txt mô tả luồng nghiệp vụ).
- Transaction được sử dụng nhất quán và đúng cách ở tất cả thao tác ghi phức hợp.
- Idempotency cho webhook được xử lý đúng — check `order.Status == "paid"` trước khi xử lý.
- `OrderService` kiểm tra course tồn tại và đã published trước khi cho phép đặt hàng.

**Vấn đề nghiêm trọng (cần sửa trước khi merge):**
1. **[BẢO MẬT NGHIÊM TRỌNG — Q32]** Webhook Sepay không xác thực chữ ký/secret key — bất kỳ ai cũng có thể POST giả webhook để tự enroll vào khóa học miễn phí.
2. **[BẢO MẬT — Q36]** URL ngrok dev hardcode trong source code — lộ cấu hình môi trường dev.
3. **[Runtime Error — Q22]** Dùng `System.Drawing.Bitmap` (QRCoder) không tương thích Linux/Docker — ứng dụng sẽ crash khi deploy lên server Linux.
4. **[Data Integrity — Q15]** Sai lệch làm tròn tiền giữa `GenerateSepayQrCode()` (int) và `HandleSepayWebhookAsync()` (decimal) có thể từ chối thanh toán hợp lệ.

**Rủi ro kỹ thuật:**
5. Dùng `Console.WriteLine()` thay vì `ILogger` — log không được thu thập trong production.
6. N+1 query khi tạo Order (N course) và khi enroll (N khóa học) — hiệu năng kém khi đơn hàng lớn, nguy cơ Sepay timeout → retry → xử lý trùng.
7. Response format không thống nhất với phần còn lại của hệ thống (`{ message }` thay vì `APIResponse`).
