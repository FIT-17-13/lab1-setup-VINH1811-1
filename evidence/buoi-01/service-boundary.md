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

- AI Vision Core: Lõi xử lý suy luận (Inference Engine) sử dụng các mô hình thị giác máy tính (ví dụ: YOLOv8, RT-DETR) để trích xuất đặc trưng và nhận diện đối tượng.

- API Service: Hệ thống RESTful API chịu trách nhiệm tiếp nhận request đầu vào (ảnh, URL) và trả về kết quả JSON.

- Logging System: Module ghi log kết quả phân tích (metadata) vào cơ sở dữ liệu nội bộ của service.

Phần nhóm chỉ tích hợp:

- Hệ thống nguồn ảnh: Camera vật lý, hệ thống lưu trữ ảnh ngoài.

- Hệ thống Frontend/UI: Giao diện người dùng cuối, app di động hoặc web dashboard.

- Hệ thống quản lý định danh: Việc quản lý người dùng được tích hợp trực tiếp qua các giao diện Client và Admin nội bộ của dự án lớn thay vì tự build một repository riêng.

## 4. Service Boundary

Service của nhóm có trách nhiệm
- Tiếp nhận dữ liệu hình ảnh từ nhiều nguồn chuẩn hóa (Tệp, Camera stream, URL).

- Thực hiện phân tích hình ảnh để bóc tách thông tin: loại đối tượng, tọa độ (bounding box), và độ tin cậy (confidence score).

- Đánh giá và trả về mức độ rủi ro (risk level) sơ bộ dựa trên cấu hình sẵn có.

- Ghi vết lại lịch sử phân tích.

- Đóng gói kết quả thành chuẩn API response để các service khác gọi tới.

Service KHÔNG làm 
- KHÔNG xử lý logic nghiệp vụ nghiệp vụ cuối cùng (Core Business) như: tự động ra quyết định xử phạt, trừ tiền, hay lưu điểm danh sinh viên.

- KHÔNG quản lý trạng thái kết nối phần cứng của Camera IoT (chỉ nhận stream data).

- KHÔNG cung cấp giao diện đồ họa (UI) cho người sử dụng trực tiếp.

## 5. Input / Output

### Input

- File Upload: Tệp ảnh định dạng chuẩn (JPEG, PNG, v.v.).

- URL: Đường dẫn tĩnh tới tệp ảnh.

- Stream Data: Các frame hình ảnh được trích xuất từ luồng video của Camera giám sát.

### Output
Định dạng JSON (API Response) bao gồm:

- detection_id: Mã định danh duy nhất của lần phân tích.

- objects_detected: Danh sách các đối tượng nhận diện được.

- bounding_boxes: Tọa độ chi tiết của các đối tượng trong ảnh.

- confidence: Điểm tin cậy của mô hình (VD: 92%).

- risk_level: Đánh giá rủi ro sơ bộ (Bình thường / Cảnh báo / Nguy hiểm).

## 6. API dự kiến

| Method | Endpoint | Mục đích |
|---|---|---|
| GET | /health | Kiểm tra service |
| POST | /detect | Gửi dữ liệu ảnh/URL để phân tích và nhận về kết quả (bounding box, confidence). |
|GET | /detections/{detection_id} | Truy xuất lại kết quả phân tích cũ bằng ID đã được ghi log.|
|GET | /models/info | Lấy thông tin về phiên bản model AI hiện tại đang được sử dụng để suy luận. |

## 7. Phụ thuộc service khác

Service này gọi đến service nào?
- Database Service: Để thực hiện việc ghi log kết quả phân tích.

- Mock Service (Nếu có): Gọi đến các service giả lập để test luồng inference khi phát triển.

Service nào gọi đến service này?
- Camera Stream Service: Gọi để đẩy liên tục các frame ảnh cần phân tích thời gian thực.

- Core Business Service: Gọi để lấy kết quả phân tích (VD: Hệ thống bãi giữ xe lấy biển số, Hệ thống an ninh lấy cảnh báo rủi ro).
## 8. Sơ đồ minh họa

Có thể vẽ bằng Mermaid, draw.io, Ludichart hoặc ảnh chụp sơ đồ.

<svg width="100%" viewBox="0 0 680 460" role="img">
  <title>Sơ đồ hệ thống Nhóm 3 - WL</title>
  <desc>Camera Stream, Core Business Service và AI Admin gửi request tới API Gateway bên trong system boundary. API Gateway kết nối hai chiều với AI Vision Core, AI Vision Core ghi log vào Log Database.</desc>
  <defs>
    <marker id="arr" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
    <marker id="arr2" viewBox="0 0 10 10" refX="2" refY="5" markerWidth="6" markerHeight="6" orient="auto">
      <path d="M8 1L2 5L8 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>

  <!-- System boundary -->
  <rect x="240" y="24" width="416" height="412" rx="16" fill="none" stroke="var(--color-border-secondary)" stroke-width="1" stroke-dasharray="7 4"/>
  <text class="ts" x="448" y="18" text-anchor="middle" dominant-baseline="central" style="font-size:11px">System boundary — Nhóm 3 - WL</text>

  <!-- External actors -->
  <g class="node c-teal">
    <rect x="24" y="60" width="172" height="44" rx="8" stroke-width="0.5"/>
    <text class="th" x="110" y="82" text-anchor="middle" dominant-baseline="central">Camera Stream</text>
  </g>
  <g class="node c-teal">
    <rect x="24" y="208" width="172" height="44" rx="8" stroke-width="0.5"/>
    <text class="th" x="110" y="230" text-anchor="middle" dominant-baseline="central">Core Business</text>
  </g>
  <g class="node c-teal">
    <rect x="24" y="356" width="172" height="44" rx="8" stroke-width="0.5"/>
    <text class="th" x="110" y="378" text-anchor="middle" dominant-baseline="central">AI Admin</text>
  </g>

  <!-- API Gateway -->
  <g class="node c-purple">
    <rect x="272" y="178" width="160" height="56" rx="8" stroke-width="0.5"/>
    <text class="th" x="352" y="198" text-anchor="middle" dominant-baseline="central">API Gateway</text>
    <text class="ts" x="352" y="218" text-anchor="middle" dominant-baseline="central">/ Endpoints</text>
  </g>

  <!-- AI Vision Core -->
  <g class="node c-coral">
    <rect x="488" y="134" width="144" height="56" rx="8" stroke-width="0.5"/>
    <text class="th" x="560" y="154" text-anchor="middle" dominant-baseline="central">AI Vision Core</text>
    <text class="ts" x="560" y="174" text-anchor="middle" dominant-baseline="central">Detection engine</text>
  </g>

  <!-- Log Database -->
  <g class="node c-gray">
    <rect x="488" y="280" width="144" height="56" rx="8" stroke-width="0.5"/>
    <text class="th" x="560" y="300" text-anchor="middle" dominant-baseline="central">Log Database</text>
    <text class="ts" x="560" y="320" text-anchor="middle" dominant-baseline="central">Persistent storage</text>
  </g>

  <!-- Camera Stream → API -->
  <path d="M196 82 L248 82 L248 192 L270 192" fill="none" stroke="var(--color-text-tertiary)" stroke-width="1" marker-end="url(#arr)"/>
  <text class="ts" x="226" y="76" text-anchor="middle">POST /detect</text>

  <!-- Core Business → API -->
  <path d="M196 230 L270 230" fill="none" stroke="var(--color-text-tertiary)" stroke-width="1" marker-end="url(#arr)"/>
  <text class="ts" x="233" y="224" text-anchor="middle">GET /detect.</text>

  <!-- AI Admin → API -->
  <path d="M196 378 L248 378 L248 222 L270 222" fill="none" stroke="var(--color-text-tertiary)" stroke-width="1" marker-end="url(#arr)"/>
  <text class="ts" x="226" y="394" text-anchor="middle">GET /health</text>

  <!-- API → Camera Stream (response) -->
  <path d="M272 192 L248 192 L248 104 L196 104" fill="none" stroke="var(--color-border-secondary)" stroke-width="1" stroke-dasharray="5 3" marker-end="url(#arr)"/>

  <!-- API → Core Business (response) -->
  <path d="M272 216 L232 216 L232 252 L196 252" fill="none" stroke="var(--color-border-secondary)" stroke-width="1" stroke-dasharray="5 3" marker-end="url(#arr)"/>

  <!-- API ↔ AI Vision Core -->
  <path d="M432 196 L486 172" fill="none" stroke="var(--color-text-secondary)" stroke-width="1" marker-end="url(#arr)"/>
  <path d="M486 182 L432 206" fill="none" stroke="var(--color-text-secondary)" stroke-width="1" marker-end="url(#arr)"/>

  <!-- AI Vision Core → Log Database -->
  <path d="M560 190 L560 278" fill="none" stroke="var(--color-text-secondary)" stroke-width="1" marker-end="url(#arr)"/>
  <text class="ts" x="574" y="238" text-anchor="start">log</text>

  <!-- Legend -->
  <line x1="272" y1="440" x2="306" y2="440" stroke="var(--color-text-tertiary)" stroke-width="1" marker-end="url(#arr)"/>
  <text class="ts" x="312" y="444" text-anchor="start">Request</text>
  <line x1="390" y1="440" x2="424" y2="440" stroke="var(--color-border-secondary)" stroke-width="1" stroke-dasharray="5 3" marker-end="url(#arr)"/>
  <text class="ts" x="430" y="444" text-anchor="start">JSON response</text>
</svg>


