# Game Design Document (GDD): Idle Survivor: Last Stand

## 1. Giới thiệu chung (Overview)
- **Tên dự án (Dự kiến):** Idle Survivor: Last Stand
- **Thể loại:** Single-screen Incremental Idle Game (Lấy cảm hứng từ hệ thống kỹ năng của game *Survivor.io*).
- **Mục tiêu dự án:** Hoàn thành bài Assignment phát triển game Idle 1 màn hình.
- **Ràng buộc:** Cắt bỏ hoàn toàn Đăng nhập, Cloud, Ads, IAP. Game lưu tiến trình thông qua Local Storage (Offline).
- **Công cụ phát triển:** Unity 6 (URP) - Tuân thủ tiêu chuẩn code (Singleton hữu hạn, Object Pooling bắt buộc, Observer Pattern cho UI).
- **Phong cách Đồ họa:** 2D (Tối ưu hóa nguồn tài nguyên bằng AI tạo sinh).

## 2. Ý tưởng chuyển thể (Từ Action Roguelite sang Incremental Idle)
Trong *Survivor.io* gốc, người chơi di chuyển liên tục để né bầy quái vật và tự động xả skill. Trong phiên bản **Idle Survivor**, cơ chế này được tinh chỉnh lại để phù hợp với giới hạn 1 màn hình và lối chơi Idle:
- **Nhân vật cố định:** Nhân vật chính (hoặc ụ súng/pháo đài) đứng bất động ở trung tâm màn hình.
- **Quái vật (Enemies):** Liên tục spawn (xuất hiện) từ các rìa màn hình và di chuyển hướng vào trung tâm.
- **Tương tác cốt lõi:** Thay vì di chuyển né tránh, người chơi sẽ tập trung vào việc **Tap (Nhấp chuột) để tấn công thủ công** kết hợp việc phân bổ tài nguyên để **nâng cấp hệ thống vũ khí tự động (Passive Generators)** tiêu diệt quái.

## 3. Vòng lặp Gameplay Cốt lõi (Core Loop)
Vòng lặp tuân thủ nghiêm ngặt chuẩn của dòng Incremental Game:
1. **Thu thập (Tap & Auto):** Nhấn vào màn hình/quái vật để gây sát thương thủ công + Vũ khí tự động tấn công để tiêu diệt quái, thu thập **Vàng (Gold)**.
2. **Nâng cấp lũy tiến (Exponential Upgrades):** Tiêu hao Vàng để nâng cấp các chỉ số cơ bản (Sát thương, Tốc đánh) hoặc mở khóa/nâng cấp các vũ khí phụ trợ (Drones, Lưới điện, Sấm sét). Chi phí sẽ tăng theo cấp số nhân sau mỗi level.
3. **Mở khóa phần thưởng (Milestones & Progression):** Sức mạnh đội hình tăng lên giúp dọn dẹp các đợt quái (Waves) cao hơn. Tiêu diệt Boss ở các mốc nhất định để mở khóa tính năng/vũ khí mới.

## 4. Chi tiết các Hệ thống (Mechanics)

### 4.1. Hệ thống Chiến đấu & Vũ khí (Kế thừa Survivor.io)
Người chơi sẽ dùng Vàng để mua và nâng cấp các loại vũ khí (hoạt động tự động - Passive Generator):
- **Click Damage (Sát thương thủ công):** Gây sát thương mỗi khi người chơi nhấp chuột vào sân đấu. 
- **Vũ khí cơ bản (Base Gun / Kunai):** Tự động ngắm bắn mục tiêu gần nhất. Nâng cấp giúp tăng Sát thương (Damage) và Tốc độ bắn (Fire Rate).
- **Forcefield (Vòng từ trường):** Tạo một vòng năng lượng bao quanh trung tâm, gây sát thương liên tục cho những quái vật tiến vào quá gần. Tốt để dọn dẹp đám đông quái yếu.
- **Guardian (Vệ binh / Con quay):** Các bánh răng xoay vòng xung quanh nhân vật, gây sát thương và có hiệu ứng đẩy lùi (Knockback).
- **Lightning (Sét giáng):** Định kỳ giáng sét ngẫu nhiên xuống bản đồ, gây sát thương cực lớn (Nuke damage) - chuyên trị quái máu trâu.

### 4.2. Hệ thống Chỉ số & Kinh tế
Hệ thống cửa hàng nâng cấp áp dụng công thức giá trị lũy tiến: 
`Giá_hiện_tại = Giá_gốc * (Hệ_số_nhân ^ Level)`
- Đảm bảo người chơi luôn cảm thấy cần phải farm nhiều hơn, tạo tính chất gây nghiện của game Idle.

### 4.3. Quái vật, Làn sóng (Waves) & Boss
- Game chia thành các **Wave (Đợt)**. Khi tiêu diệt đủ số lượng quái, tiến trình nhảy sang Wave tiếp theo với quái vật có lượng HP và tốc độ di chuyển cao hơn.
- **Trận đấu Boss (Boss Fight):** Cứ mỗi mốc 10 Waves (ví dụ Wave 10, 20, 30...) sẽ xuất hiện một Boss lớn.
- **Cơ chế DPS Check:** Trận Boss có đếm ngược thời gian. Nếu không tiêu diệt được Boss kịp lúc, nhân vật bị hạ gục và tiến trình sẽ lùi lại 1 Wave trước đó để người chơi tiếp tục treo máy (Idle) farm vàng và nâng cấp sức mạnh.

### 4.4. Hệ thống Prestige (Tái sinh)
Khi người chơi chạm ngưỡng giới hạn (không thể vượt qua Boss do chi phí nâng cấp quá đắt), họ sẽ sử dụng tính năng **Tái sinh**:
- Đặt lại tiến trình Waves, cấp độ Nâng cấp và số Vàng về 0.
- Nhận lại tiền tệ cao cấp: **DNA (hoặc Điểm Đột Biến)** dựa trên số Wave đã đạt được.
- Dùng DNA để nâng cấp các **Chỉ số Vĩnh viễn (Permanent Upgrades)** (Ví dụ: +20% Tổng sát thương, +10% Vàng rơi ra).

## 5. Trải nghiệm & Cảm giác (Game Feel & Juiciness)
Game tập trung mạnh vào tối ưu hóa Game Feel theo tiêu chuẩn phát triển (sử dụng Unity `DOTween`):
- **Floating Damage Text:** Hàng loạt các con số sát thương nhảy vọt lên từ quái vật (sử dụng Object Pool), giúp người chơi thỏa mãn với sự mạnh lên của bản thân.
- **Hit Feedback:** Quái vật giật lùi, nháy màu (Flash Red) và hiệu ứng Squash & Stretch mỗi khi nhận đòn.
- **Camera Shake:** Rung lắc màn hình nhẹ mỗi khi có vụ nổ, sét đánh, hoặc khi tiêu diệt Boss.
- **Particle Effects:** Hiệu ứng máu văng, vàng rơi lấp lánh (sử dụng Particle System).

## 6. Giao diện người dùng (UI/UX - Single Screen)
Giao diện phân bổ gọn gàng 1 màn hình duy nhất (Bố cục theo màn hình dọc - Mobile Portrait hoặc ngang - PC Windowed):
- **Nửa trên màn hình (Battlefield):** Hiển thị sân đấu 2D. Thanh máu và Tên Wave hiện ở mép trên cùng. Quái vật spawn từ viền và đi vào giữa màn hình.
- **Nửa dưới màn hình (Menu Nâng cấp):** Khu vực tương tác chính của UI. Gồm các tab chia gọn gàng: `Nâng cấp cơ bản`, `Hệ thống Vũ khí`, `Prestige (Tái sinh)`. Thiết kế trực quan dạng lưới (Grid/List) với các nút "Upgrade" to rõ.

## 7. Tài liệu minh chứng: Giai đoạn thiết kế và ứng dụng AI (Phase 1)
Quá trình phân tích và thiết kế này đã được tối ưu nhờ sự trợ giúp của AI LLM (Gemini/ChatGPT):
1. **Brainstorming Concept:** Đề xuất và chuyển hóa thành công mô hình phức tạp (Survivor.io) về mô hình tĩnh của Incremental Game.
2. **Cấu trúc GDD:** Tạo lập bộ khung tài liệu GDD chuyên nghiệp, đầy đủ các cơ chế (Core Loop, Economy, Game Feel).
3. **Kế hoạch tài nguyên (Asset Plan):** 
   - *Đồ họa:* Sẽ sử dụng AI (Midjourney/Recraft) để tạo Sprite (Nhân vật, Boss, UI Elements, Background).
   - *Lập trình:* Sẽ sử dụng AI hỗ trợ sinh mã nguồn Unity C# (kiến trúc Singleton Manager, Object Pool pattern cho số lượng lớn đạn/quái, Observer pattern cho UI).
4. **Cân bằng Game (Balancing):** Prompt AI hỗ trợ lập bảng thông số (Base Cost, Multiplier, Enemy HP Scaling) để tạo đồ thị cân bằng hoàn chỉnh cho Game.
