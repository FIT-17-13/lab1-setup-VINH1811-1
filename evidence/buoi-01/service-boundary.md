# Service Boundary của nhóm

## 1. Thông tin nhóm

- Tên nhóm: Nhóm số 3 - WL
- Lớp: CNTT17-13
- Thành viên: Nguyễn Văn Vinh, Đỗ Văn Vinh, Bạch Ngọc Lương, Lại Thành Đoàn
- Service nhóm phụ trách: AI Vision
- Sản phẩm tổng thể của lớp: Product A

## 2. Actor

- Hệ thống Camera IoT (Camera Stream)
- Hệ thống Backend Nghiệp vụ (Core Business)
- Quản trị viên AI (AI Administrator)
- Nhân viên An ninh / Quản lý Giám sát
- Người dùng (Sinh viên / Giảng viên)

## 3. System Boundary

Nhóm em xây phần nào?

Phần nhóm kiểm soát:

- ...

Phần nhóm chỉ tích hợp:

- ...

## 4. Service Boundary

Service của nhóm có trách nhiệm gì?

Service KHÔNG làm gì?

## 5. Input / Output

### Input

- ...

### Output

- ...

## 6. API dự kiến

| Method | Endpoint | Mục đích |
|---|---|---|
| GET | /health | Kiểm tra service |
| POST | ... | ... |

## 7. Phụ thuộc service khác

Service này gọi đến service nào?

Service nào gọi đến service này?

## 8. Sơ đồ minh họa

Có thể vẽ bằng Mermaid, draw.io, Ludichart hoặc ảnh chụp sơ đồ.

```mermaid
flowchart LR
    User[Actor] --> Service[Service của nhóm]
    Service --> DB[(Database)]
    Service --> Other[Service khác]
