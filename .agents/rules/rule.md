---
trigger: always_on
---

# QUY TẮC LẬP TRÌNH DÀNH CHO AGENT (ANTIGRAVITY IDE)


**0. QUY TẮC CHUNG**
- Luôn dùng tiếng Việt Nam trong quá trình làm dự án, mọi file impementation, task, walkthrough, ghi chú trong code.

**1. QUY TẮC KIẾN TRÚC HỆ THỐNG (ZERO-COST STACK)**
- **Công nghệ cốt lõi:** Bắt buộc triển khai hệ thống tĩnh trên `GitHub Pages`, kết hợp `Firebase` (Auth & Firestore) để đồng bộ dữ liệu Real-time và `Google Apps Script` (`GAS`) làm Webhook/Backend[cite: 3].
- **Middleware (GAS):** Chỉ xây dựng các `RESTful APIs` thông qua hàm `doPost` hoặc `doGet`[cite: 1]. Mọi yêu cầu bắt buộc phải xác thực bảo mật qua `secret_key` và định tuyến (routing) động thông qua `query parameters`[cite: 1].
- **Tự động hóa (Automation):** Sử dụng `GitHub Actions` thiết lập các `Cron Job` để lập lịch quét hệ thống định kỳ và thực thi các API bên thứ ba (như Zalo)[cite: 3].

**2. QUY TẮC CƠ SỞ DỮ LIỆU (FIRESTORE) & BẢO MẬT**
- **Phi chuẩn hóa (Denormalization):** Nhúng trực tiếp các thông tin cần hiển thị cơ bản vào `Document` đích; tuyệt đối không sử dụng logic Join phức tạp tại Client[cite: 1].
- **Tối ưu truy vấn:** Nghiêm cấm sử dụng `db.collection('...').get()` tĩnh[cite: 1]. Tất cả các truy vấn đọc dữ liệu danh sách bắt buộc đi kèm bộ lọc `.where()`[cite: 1].
- **Xác thực quyền truy cập:** Xác thực 100% qua `Google OAuth 2.0`[cite: 3]. Thiết lập cơ chế tự động ánh xạ (map) quyền hạn thông qua `Gmail` dựa trên cơ sở dữ liệu `Whitelist` tạo sẵn[cite: 3].
- **Kiểm soát truy cập (RBAC):** Bắt buộc định nghĩa `Firebase Security Rules` chặt chẽ, giới hạn quyền thay đổi dữ liệu theo 3 cấp độ: `Super Admin`, `Admin/Teacher`, và `Student/User`[cite: 3].

**3. QUY TẮC GIAO DIỆN (UI/UX - PREMIUM MINI-CRM)**
- **Kiến trúc CSS/JS:** Không viết mã CSS hoặc JS nội tuyến (`inline`) vào file HTML[cite: 1, 2]. Ưu tiên sử dụng `Vanilla CSS` kết hợp hệ thống biến `:root` tokens; tuyệt đối không dùng các Framework lớn như Tailwind/Bootstrap nếu không cần thiết[cite: 2].
- **Bố cục chức năng (Layout):** Áp dụng thiết kế `Single-Pane 3-in-1 Dashboard` sử dụng `Accordion Inline`[cite: 2]. Hạn chế tối đa thao tác mở `Modal popup` làm gián đoạn luồng làm việc[cite: 2].
- **Hiển thị trực quan (Visualization):** Thay thế các giá trị văn bản đơn điệu bằng `Health Bar` (Thanh tiến trình) hoặc `Badge` trạng thái có màu sắc Semantic (Xanh/Vàng/Đỏ)[cite: 2].

**Bảng quy chuẩn Responsive cho Data Grid**[cite: 2]:

| Kích thước thiết bị | Breakpoint | Cấu trúc Bảng | Xử lý UX/UI bắt buộc |
| :--- | :--- | :--- | :--- |
| Desktop/Tablet | `min-width: 993px` | Dạng `Table` (Tối đa 6 cột) | Gộp các trường chung ngữ cảnh (`Stacked Data`) bằng `flex-direction: column` |
| Mobile | `max-width: 992px` | Dạng `Card-based Layout` | Ẩn thẻ `thead`, chuyển `tr` thành block, biến `td` thành flexbox với `data-label`. Tuyệt đối không cho phép cuộn ngang |

**4. QUY TẮC VIẾT MÃ & QUẢN LÝ PHIÊN BẢN**
- Phân tách cấu trúc rõ ràng: Chia nhỏ các module JS/HTML; nghiêm cấm viết mã nguyên khối rườm rà[cite: 1].
- Giữ nguyên 100% thuật ngữ chuyên ngành bằng tiếng Anh trong quá trình đặt tên và bình luận.
- Mọi thay đổi về cấu trúc phải được lưu lại vào file `log.md` nằm trong thư mục `docs`[cite: 1].

```javascript
// Cấu trúc chuẩn cho Webhook trên GAS (Google Apps Script)
function doPost(e) {
  // 1. Phân tách payload và xác thực secret_key bắt buộc
  const payload = JSON.parse(e.postData.contents);
  if (payload.secret_key !== "SECRET_KEY_ENV") {
    return ContentService.createTextOutput("Unauthorized").setMimeType(ContentService.MimeType.JSON);
  }
  
  // 2. Định tuyến (routing) dựa trên query parameters
  const action = e.parameter.action;
  
  if (action === "webhook-event") {
    // 3. Thực thi logic nghiệp vụ (ví dụ: bóc tách tác giả từ Git và đẩy lên Firestore)
    return ContentService.createTextOutput("Success").setMimeType(ContentService.MimeType.JSON);
  }
}