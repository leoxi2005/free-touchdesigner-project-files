# Claude Bridge

Cầu nối **Claude ⇄ TouchDesigner** gói trong một file `.tox` duy nhất. Kéo vào network là xong — Claude tạo node, sửa tham số, viết Python thẳng trong project của bạn.

A single-file `.tox` that connects **Claude to TouchDesigner**. Drag it into any network and Claude can create nodes, edit parameters and run Python directly in your project.

## File

| | |
|---|---|
| **File** | `claude_bridge.tox` |
| **Kích thước** | 78 KB |
| **TouchDesigner** | Lưu bằng 2025.32460 — nên dùng `2025.32xxx` trở lên |
| **Hệ điều hành** | macOS + Windows |
| **Non-Commercial** | ✅ Mở được bằng bản miễn phí |

[⬇️ Tải claude_bridge.tox](./claude_bridge.tox)

## Cài / Setup

1. Tải file `.tox` (nút **Download raw file**, đừng dùng Save link as).
2. Kéo vào network bất kỳ trong TouchDesigner. Nó **tự kết nối**, không cần bấm gì.
3. Chọn node → `p` → tab **Claude** → bấm **Kiểm tra & hướng dẫn cài**. Nó tự dò máy bạn có gì, thiếu gì và in ra checklist kèm link tải.
4. Nếu đã có Claude Code: bấm **Đăng ký MCP server** là xong. Nếu chưa, bấm **Mở trang tải cái còn thiếu**.

Cần thêm: **Node.js 18+** và **Claude Code** (hoặc Claude Desktop).

Cấu hình MCP thủ công, nếu muốn tự làm:

```json
{ "touchdesigner": { "type": "stdio", "command": "npx",
  "args": ["-y", "touchdesigner-mcp-server@latest", "--stdio"] } }
```

Đổi `Port` khác `9981` thì thêm `"env": { "TD_WEB_SERVER_PORT": "<port>" }`.

## Tham số (tab Claude)

| Tham số | Ý nghĩa |
|---|---|
| `Connect Claude` | Bung package nhúng, mở WebServer, báo trạng thái |
| `Disconnect` | Tắt server |
| `Port` | Mặc định `9981` — phải khớp cấu hình MCP |
| `Auto-connect on load` | Tự kết nối khi thả file / mở project |
| `Status` / `State` | `off` / `busy` / `on` / `error` |
| `API version` | Phiên bản API của package nhúng |
| `Máy này` | Tóm tắt máy có gì / thiếu gì |
| `Kiểm tra & hướng dẫn cài` | Checklist đầy đủ + link tải, lưu ở DAT `setup_report` |
| `Mở trang tải cái còn thiếu` | Mở trình duyệt tới trang tải Node.js / Claude Code |
| `Đăng ký MCP server` | Chạy `claude mcp add` giúp bạn |
| `Reload embedded package` | Bung lại package nhúng |

## Demo — thứ Claude dựng được

`Audio Reactive Particles x Claude Demo.toe` là project dựng **hoàn toàn qua cầu nối này**: mình gõ yêu cầu, Claude tạo node và viết GLSL thẳng trong TouchDesigner.

A project built **entirely through this bridge** — every node and every line of GLSL was written by Claude inside TouchDesigner.

[⬇️ Tải file demo](./Audio%20Reactive%20Particles%20x%20Claude%20Demo.toe) · 104 KB · 54 node

Bên trong:

- Chuỗi phân tích audio tách 4 dải `bass / mid / high / energy`, mỗi dải có gain + lag riêng
- Particle sim chạy bằng **GLSL compute shader** (position/velocity trong texture)
- Render có trail feedback + camera

Mở lên rồi làm 2 bước:

1. Chọn node `audiofilein1` → tham số **File** → trỏ tới một bản nhạc của bạn (file để trống sẵn, không kèm nhạc vì lý do bản quyền).
2. Bấm play.

> 🔊 Patch có `Audio Device Out` — mở lên là **phát tiếng ra loa** ngay. Vặn nhỏ trước nếu bạn đang đeo tai nghe hoặc ngồi chỗ đông người.

Đây chỉ là demo cho vui, không phải "project file được chăm chút" — cứ mổ xẻ thoải mái.

## ⚠️ Lưu ý bảo mật / Security

Khi kết nối, component mở một **WebServer trên localhost** cho phép **chạy Python tuỳ ý bên trong TouchDesigner**. Đó chính là cách Claude điều khiển TD, nhưng cũng có nghĩa:

- Chỉ dùng trên máy cá nhân, mạng tin cậy. **Đừng** port-forward hay mở cổng `9981` ra Internet.
- Bấm `Disconnect` khi không dùng, nhất là lúc đi diễn / máy nối mạng lạ.
- Lần đầu chạy trên Windows, Windows Firewall sẽ hỏi — kết nối chỉ là localhost nên chọn gì cũng thường chạy được.

## Khác gì bản gốc / What's different

Bản gốc `mcp_webserver_base.tox` đọc code Python từ folder `touchdesigner-mcp-td/` nằm cạnh file qua par `externaltox` — đem file đi nơi khác là gãy.

Bản này **nhúng nguyên 69 file Python** (zip → base64) vào một Text DAT. Khi Connect, nó bung ra thư mục tạm rồi gắn vào `sys.path`. Copy đi đâu cũng chạy, không cần folder đi kèm.

Thêm ba thứ bản gốc không có:

- **Schema nạp bằng JSON thay vì YAML.** Bản gốc dùng `yaml`; máy nào thiếu PyYAML thì schema rỗng và *mọi endpoint trả 404 mà không báo lỗi gì*.
- **Chặn trùng cổng.** macOS cho hai socket cùng bind một cổng mà không báo lỗi, khiến hai WebServer DAT luân phiên trả lời ngẫu nhiên. `Connect` kiểm tra trước và từ chối, kèm đường dẫn node đang giữ cổng.
- **Trình dò cài đặt.** TouchDesigner mở từ Finder/Explorer chỉ thừa hưởng PATH tối thiểu nên `shutil.which` trả rỗng dù máy có đủ node/npx/claude. Bản này tự mò các thư mục cài đặt thật.

## Ghi nguồn / Credits

Dựng trên [**touchdesigner-mcp**](https://github.com/8beeeaaat/touchdesigner-mcp) của [8beeeaaat](https://github.com/8beeeaaat) (sadao komaki) — MIT. Package Python của dự án đó được nhúng nguyên vẹn trong file `.tox` này.

## Giấy phép / License

Riêng thư mục này theo **MIT** (không phải CC BY 4.0 như phần còn lại của repo), để đúng với license của code gốc bên trong. Xem [THIRD-PARTY-LICENSE.txt](./THIRD-PARTY-LICENSE.txt).
