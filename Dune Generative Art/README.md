# Dune Generative Art

Cồn cát chuyển động — vân sóng chảy liên tục, dựng hoàn toàn bằng TOP, không dùng GLSL. Nhẹ, chạy realtime, dễ mổ.

Shifting dunes — flowing ridge patterns built entirely from TOPs, no GLSL. Light, realtime, easy to take apart.

## File

| | |
|---|---|
| **File** | `Dune Generative Art.toe` |
| **Kích thước** | 13 KB · 51 node |
| **TouchDesigner** | 2025.33230 (macOS) |
| **Non-Commercial** | ✅ Mở được bằng bản miễn phí |
| **Media ngoài** | Không cần — mở là chạy |

[⬇️ Tải file .toe](./Dune%20Generative%20Art.toe)

## Bên trong / Inside

- **8 Noise TOP** chồng lớp, đẩy qua chuỗi **3 Displace** → sinh vân cồn cát
- **2 vòng Feedback** giữ vệt chảy, cho cát trôi thay vì nhấp nháy
- `pointtransform` → `geo` / `render`: hạt được instance từ texture vị trí
- **Bloom** (base COMP) + `ramp` đổ màu + `antialias` khép cuối
- `keyboardin` — bấm **F1** vào Perform Mode xem toàn màn hình

Trong patch có sẵn 2 ghi chú vàng chỉ đúng chỗ nên vặn:

- Noise điều khiển **cấu trúc tổng thể** của vân — tăng `period` thì vân trải rộng ra.
- Node điều khiển **số lượng hạt** — máy yếu thì hạ xuống, đây là chỗ ăn fps nhất.

## Lưu ý / Note

Node `moviefileout1` còn giữ đường dẫn xuất của máy mình (`.../Downloads/Dune.mov`). Muốn quay ra file thì **sửa tham số `File` sang đường dẫn của bạn** trước khi bấm record, nếu không TouchDesigner sẽ báo lỗi ghi file.

## Giấy phép / License

[CC BY 4.0](../LICENSE) — dùng tự do kể cả thương mại, ghi nguồn **LEOXI** khi chia sẻ lại.
