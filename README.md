# Bài tập: Local Storage, Cloud Storage và Hybrid Workflow

## Phần 1. Phân tích vấn đề

### 1. Rủi ro của nhóm CampusPay

| Hạng mục | Rủi ro | Nguyên nhân | Xếp loại |
|---|---|---|---|
| Business Plan | **Cao** – mất toàn bộ | Chỉ lưu trên laptop trưởng nhóm, không sao lưu; ổ cứng hỏng. | Tổ chức + Công nghệ |
| Mã nguồn HTML/CSS | **Cao** – dễ mất | Chỉ có trên USB, dễ hỏng hoặc thất lạc. | Tổ chức + Công nghệ |
| Database test user | **Cao** – dữ liệu lỗi thời | Lưu riêng trên máy thành viên B, không đồng bộ; bản gần nhất đã cũ 2 tuần. | Tổ chức |
| Slide Pitch | **Trung bình** – dễ nhầm bản | Gửi qua Zalo với nhiều tên `final`, không có bản chính thức. | Tổ chức |
| Tài liệu SRS | **Trung bình** – dễ xung đột | Gửi file qua email nhiều lần, không quản lý phiên bản tập trung. | Tổ chức |

**Nhận xét:** Rủi ro chủ yếu do **không có quy trình sao lưu và quản lý phiên bản**, không chỉ do ổ cứng bị hỏng. Các mức trên là đánh giá định tính, không phải phần trăm xác suất thực tế.

### 2. Hiện tượng 23 phiên bản file

Đây là **phân tán phiên bản (version sprawl)**, dễ dẫn đến **xung đột phiên bản (version conflict)**.

- **Nguyên nhân:** Mỗi người sửa một bản riêng, gửi qua Zalo rồi đặt tên `final`, `final_v2`...
- **Hậu quả:** Không biết bản nào mới nhất, dễ ghi đè nội dung, mất công ghép file và làm chậm dự án.

## Phần 2. Sửa lỗi và nghiệm thu

### 3. Thiết kế Hybrid Workflow

**Phần mềm:** OneDrive để tự động đồng bộ tài liệu; Git và GitHub (repository riêng tư) để quản lý phiên bản mã nguồn.

**Luồng dữ liệu 3 bước:**

```text
[1] LOCAL: Sửa file trong OneDrive/CampusPay → Ctrl+S
                     |
                     v
[2] AUTO SYNC: OneDrive phát hiện thay đổi và tải lên khi có mạng
                     |
                     v
[3] CLOUD: File được lưu trên OneDrive, nhóm truy cập bản mới
            (mã nguồn quản lý thêm bằng Git/GitHub)
```

**Cấu trúc thư mục đề xuất:**

```text
OneDrive/
└── CampusPay/                   ← ĐỒNG BỘ
    ├── Code/                    ← HTML, CSS, JS nguồn
    ├── Docs/                    ← Business Plan, SRS
    ├── Slides/                  ← Slide Pitch
    └── Assets/                  ← Hình ảnh, tài nguyên dự án

D:/Local-Only/                   ← KHÔNG ĐỒNG BỘ
├── Games/
├── Movies/
└── Temp-Build/                  ← File tạm, build, node_modules
```

- Dùng **Files On-Demand** để file ít dùng chỉ nằm trên Cloud; chọn **Always keep on this device** cho file cần làm việc ngoại tuyến.
- Không đưa mật khẩu, file `.env` hoặc dữ liệu khách hàng nhạy cảm vào thư mục chia sẻ chung.
- Với dự án JavaScript có `node_modules`, nên phát triển trong thư mục Git **ngoài OneDrive** và đẩy mã nguồn lên GitHub riêng tư; không đồng bộ `node_modules`. OneDrive không có cơ chế loại trừ tùy ý mọi thư mục con bên trong thư mục đang đồng bộ.

### 4. Bẫy dữ liệu: Gập laptop khi chưa Sync xong

- **Biểu tượng:** Hai mũi tên xanh xoay vòng = **đang đồng bộ**; dấu **tích xanh** = file đã đồng bộ xong; dấu **X đỏ** = lỗi đồng bộ.
- **Khi gập máy và máy chuyển sang Sleep:** Đồng bộ thường tạm dừng, tiếp tục khi mở máy và có mạng. Nếu file chưa lên Cloud thì người khác vẫn có thể thấy bản cũ.
- **Cách xử lý:** Sau khi nhấn `Ctrl+S`, giữ máy hoạt động và có Internet → chờ hết biểu tượng mũi tên xoay, xuất hiện tích xanh → kiểm tra OneDrive báo **Up to date**; với file quan trọng, mở OneDrive trên web để xác nhận bản mới đã xuất hiện rồi mới gập máy.

## Kết luận

**Local giúp làm việc nhanh; Cloud giúp sao lưu và chia sẻ; Git giúp quản lý lịch sử mã nguồn.** Nhóm cần dùng chung một nơi lưu trữ, phân quyền rõ ràng và kiểm tra đồng bộ trước khi tắt máy.
