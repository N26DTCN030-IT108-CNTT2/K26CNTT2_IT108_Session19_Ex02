# Báo Cáo Phân Tích: Chuỗi Tấn Công Từ Mật Khẩu Bị Rò Rỉ (Domino Effect)

------------------------------------------------------------------------

## Thông Tin Bài Nộp
* **Họ và tên:** Lê Hồ Thanh Thảo
* **Mã lớp / MSSV:** N26DTCN030
* **Môn học:** IT108-K26 - Nhập Môn Công Nghệ Thông Tin

---

## 1. Cơ Chế Tấn Công Domino Effect (Credential Stuffing)

### 1.1. Bối cảnh & Kịch bản thực tế
Người dùng đăng ký tài khoản tại một diễn đàn công nghệ nhỏ và đặt mật khẩu là `123456`. Đồng thời, người dùng tái sử dụng chính xác email và mật khẩu này cho các dịch vụ quan trọng khác như **Gmail**, **Facebook**, và **GitHub**.

### 1.2. Quy trình tấn công theo chuỗi
1. **Bước 1 (Data Breach):** Diễn đàn nhỏ có hệ thống bảo mật kém, lưu trữ mật khẩu dưới dạng văn bản thuần (plain text) hoặc mã hóa băm yếu. Hacker xâm nhập thành công và thu thập toàn bộ cơ sở dữ liệu người dùng.
2. **Bước 2 (Credential Stuffing):** Hacker sử dụng công cụ tự động hóa (Botnet) gửi liên tục các yêu cầu đăng nhập chứa cặp `Email + Mật khẩu` vừa trộm được lên các nền tảng lớn (Gmail, Facebook, GitHub,...).
3. **Bước 3 (Hiệu ứng Domino):** Do thói quen dùng chung mật khẩu `123456`, hacker đăng nhập thành công vào Gmail. Từ Gmail, kẻ tấn công kích hoạt tính năng **Quên mật khẩu (Password Reset)** để chiếm quyền kiểm soát toàn bộ các dịch vụ trực tuyến khác liên kết với email này.

---

## 2. Phân Tích: Mật Khẩu Cảm Tính vs. Mật Khẩu Do Máy Tính Sinh Ra

### 2.1. Bảng so sánh chi tiết

| Tiêu chí | Mật khẩu cảm tính (Human-generated) | Mật khẩu máy tính sinh ra (Machine-generated) |
| :--- | :--- | :--- |
| **Ví dụ thực tế** | `123456`, `thanhthao2007`, `iloveyou` | `kX9#mP2$vL9!wQ5z` |
| **Độ hỗn loạn (Entropy)** | **Rất thấp** ($\approx 10 - 20\text{ bits}$). Tuân theo quy luật ngôn ngữ, ngày sinh, tên riêng hoặc chuỗi phím liền kề. | **Rất cao** ($\ge 128\text{ bits}$). Các ký tự được chọn phân bố hoàn toàn ngẫu nhiên và đồng đều. |
| **Kỹ thuật bẻ khóa tương ứng** | Dễ dàng bị bẻ khóa bằng **Dictionary Attack** (Tấn công từ điển), **Rainbow Tables**, hoặc **Rule-based Attack**. | Kháng hoàn toàn tấn công từ điển; tấn công vét cạn (**Brute-force**) mất hàng tỷ năm. |
| **Thời gian phá giải** | Vài millisecond (dưới 1 giây) bằng các công cụ bẻ khóa thông dụng (Hashcat, John the Ripper). | Không khả thi về mặt thời gian lẫn chi phí tính toán thực tế. |

### 2.2. Phân tích góc độ kỹ thuật (Entropy)
* **Mật khẩu cảm tính:** Thường tuân theo cấu trúc ngôn ngữ người hoặc thói quen gõ phím. Kẻ tấn công chỉ cần tập hợp một bộ từ điển vài triệu từ thông dụng là có thể quét trúng mật khẩu trong thời gian ngắn.
* **Mật khẩu máy tính sinh ra:** Sử dụng ngẫu nhiên tập ký tự gồm 94 ký tự ASCII in được (chữ hoa, chữ thường, chữ số, ký tự đặc biệt) với độ dài 16 ký tự, số lượng kết hợp khả dĩ là $94^{16} \approx 3.7 \times 10^{31}$. Việc duyệt toàn bộ không gian khóa này bằng hệ thống máy tính hiện tại là bất khả thi.

---

## 3. Tiêu Chuẩn Mã Hóa AES-256 Trong Password Manager vs. Ghi Chép Thủ Công

### 3.1. Rủi ro từ việc ghi chép thủ công
* **File `.txt` / Ghi chú điện thoại không mã hóa:** Nếu máy tính hoặc điện thoại nhiễm phần mềm độc hại (**Infostealer**), mã độc sẽ quét qua ổ đĩa và tải file văn bản chứa mật khẩu về máy chủ điều khiển (C2 Server) của hacker trong vài giây.
* **Ghi sổ tay / Giấy nốt:** Dễ bị thất lạc vật lý, hư hỏng hoặc rò rỉ qua hình thức **Shoulder Surfing** (nhìn lén màn hình/sổ tay).

### 3.2. Sự an toàn của Password Manager với tiêu chuẩn AES-256
* **Thuật toán AES-256 (Advanced Encryption Standard 256-bit):** Là tiêu chuẩn mã hóa khối đối xứng cấp quân sự. Chiều dài khóa 256-bit tạo ra $2^{256}$ khả năng khác nhau, đòi hỏi năng lượng và thời gian tính toán lớn hơn tuổi thọ của vũ trụ để giải mã nếu không có chìa khóa.
* **Kiến trúc Zero-Knowledge (Kiến thức bằng không):**
  1. Mật khẩu tổng (**Master Password**) chỉ nằm trong bộ nhớ của người dùng và không bao giờ gửi lên server.
  2. Dữ liệu mật khẩu được mã hóa cục bộ ngay trên thiết bị trước khi tải lên Cloud thông qua hàm băm gia cố (PBKDF2 hoặc Argon2).
  3. Ngay cả khi hacker hack được máy chủ của nhà cung cấp Password Manager (như Bitwarden, 1Password), dữ liệu thu được vẫn chỉ là chuỗi mã hóa AES-256 vô nghĩa.

---

## 4. Khuyến Nghị & Giải Pháp Bảo Mật

1. **Sử dụng Trình quản lý mật khẩu (Password Manager):** Tự động tạo và lưu trữ mật khẩu mã hóa AES-256 độc bản cho từng tài khoản.
2. **Kích hoạt xác thực 2 bước (2FA):** Sử dụng các ứng dụng tạo mã OTP (Google Authenticator, Microsoft Authenticator) thay vì SMS để ngăn chặn đợt tấn công Credential Stuffing ngay cả khi mật khẩu bị lộ.
3. **Thường xuyên kiểm tra rò rỉ:** Tra cứu email trên các nền tảng uy tín như *Have I Been Pwned* để kịp thời đổi mật khẩu nếu tài khoản nằm trong các đợt lộ dữ liệu lớn.
