# Thiệp mời cưới Hương Ly & Đức Mạnh

Thiệp mời cưới dạng web, mở trên điện thoại, gồm hai thiệp riêng cho hai bên gia đình.

| Thiệp | Đường dẫn | Hôn lễ | Tiệc cưới |
|---|---|---|---|
| Nhà gái | `/nha-gai` | 9:00 thứ Bảy 14.11.2026 (06/10 âm lịch), tư gia nhà gái | 18:00 14.11.2026, Capella Gallery Hall, TP. HCM (đón khách 18:00, khai tiệc 19:00) |
| Nhà trai | `/nha-trai` | 9:00 Chủ nhật 29.11.2026 (21/10 âm lịch), tư gia nhà trai | 10:00 29.11.2026, Trung tâm văn hóa thể thao xã Eaktur, tỉnh Đắk Lắk (đón khách 10:00, khai tiệc 11:00) |

Khác biệt giữa hai thiệp: nhà gái để tên cô dâu trước, nhà trai để tên chú rể trước. Nhà gái ghi "Lễ Vu Quy", nhà trai ghi "Lễ Tân Hôn". Thứ tự ông bà hai họ và thứ tự tab mừng cưới cũng đổi theo.

## Cấu trúc

```
nha-gai/index.html    thiệp nhà gái
nha-trai/index.html   thiệp nhà trai
```

Mỗi thiệp là **một file HTML tự chứa**: CSS và JS viết thẳng trong file, ảnh, nhạc, hoa văn đều nhúng dạng base64. Chỉ Google Fonts, Google Maps và Google Apps Script là tài nguyên ngoài. Không có build, package manager, lint hay test. Hai file là hai bản sao độc lập, sửa chung một chỗ thì phải sửa cả hai.

## Chạy thử

```bash
npx serve .
```

Rồi mở `http://localhost:3000/nha-gai` hoặc `/nha-trai`. Có thể mở thẳng file HTML, nhưng bản đồ nhúng và gửi form cần chạy qua http.

## Tên khách mời

Thêm `?to=Tên` vào link, ví dụ `/nha-trai?to=Anh%20Nam`. Tên hiện trên màn cover và được gửi kèm khi xác nhận tham dự. Không có `?to=` thì hiện "Quý khách".

## Các phần của trang

Cover (bấm "Mở thiệp" để vào trang và bật nhạc) → hero phong bì → (nhà trai: đếm ngược, ảnh chú rể, ảnh cô dâu) → thông tin lễ cưới → slider ảnh → lịch tháng 11 → địa điểm và bản đồ → xác nhận tham dự (RSVP).

Nút nổi ở dưới màn hình: RSVP, gửi lời chúc, mừng cưới (QR và số tài khoản, sao chép được), thả tim.

## Chỉnh sửa nội dung

Nội dung chữ viết thẳng trong HTML. Phần cấu hình nằm ở khối `CONFIG` (đầu thẻ `<script>`), có ghi chú "edit here":

- `photos`: ảnh hero và các slide (slot rỗng sẽ hiện ảnh minh họa). Nhà trai có thêm `groom`, `bride`.
- `calendar`: `startOffset` là số ô trống trước ngày 1 (tuần bắt đầu từ thứ Hai), `highlight` là ngày cưới.
- `rsvpApi`, `messageApi`: địa chỉ gửi form (xem phần dưới).
- `music`: file mp3 (base64 hoặc đường dẫn). Để trống thì phát giai điệu chuông tự tạo.

Đếm ngược của nhà trai đếm tới 9:00 ngày 29/11/2026 (giờ Việt Nam), sửa trong khối `đếm ngược đến giờ làm lễ`.

### Lưu ý khi sửa file

Mỗi file nặng 1,9 đến 2,2 MB vì có nhiều dòng base64 rất dài (ảnh, nhạc, hoa, QR). **Đừng đọc hoặc in cả file**, đọc theo khoảng dòng, và cắt bớt dòng dài khi grep (`cut -c1-200`). Đừng dùng công cụ ghi đè nguyên file lên các dòng đó.

Hai tên cô dâu chú rể (`.cv-names`, `.plaque-txt`, `.names2`) phải giữ cấu trúc ba phần tử: tên, dấu `&`, tên. Đoạn JS căn dấu `&` theo nét chữ dựa vào cấu trúc này.

## Gửi dữ liệu: Google Apps Script

Xác nhận tham dự và lời chúc được POST (`Content-Type: text/plain`, để tránh preflight CORS) tới một Web App Google Apps Script. Loại dữ liệu chọn bằng tham số `type` trên URL:

| Hành động | `type` | Body JSON | Ai dùng |
|---|---|---|---|
| Xác nhận tham dự | `confirmation` | `name`, `attending`, `guests` | nhà gái |
| Xác nhận tham dự | `confirmation_nt` | `name`, `attending`, `guests` | nhà trai |
| Lời chúc | `message` | `name`, `message` | cả hai |

Script phải trả JSON `{"ok": true}`, nếu không thiệp sẽ báo "Gửi chưa được".

Script nằm ngoài repo này. Để RSVP nhà trai vào đúng sheet, script cần có nhánh cho `type=confirmation_nt` ghi vào sheet `WeddingConfirmationNT`, và phải deploy lại bản mới sau khi sửa.

Cả hai thiệp đang dùng chung một Web App. Lời chúc (`type=message`) của hai bên hiện đi vào cùng một nơi.

## Triển khai

Đưa cả repo lên một static host (Netlify, GitHub Pages, Vercel…). Thư mục có `index.html` được phục vụ thành URL sạch: `<domain>/nha-gai`, `<domain>/nha-trai`.

## Lưu ý

- Lượt thích và các bong bóng lời chúc chỉ chạy phía trình duyệt, tải lại trang là mất.
- Bản nhà trai đang dùng tạm ảnh hero, slide, nhạc và hoa của nhà gái. Hai tấm ảnh cô dâu chú rể (`groom`, `bride`) là ảnh riêng, đã thu nhỏ xuống rộng 640px để file không quá nặng.
