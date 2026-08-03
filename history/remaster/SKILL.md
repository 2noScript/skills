---
name: remaster
description: Biến đổi một đoạn kịch bản lịch sử thô thành kịch bản phim tài liệu YouTube chuyên nghiệp, tăng giữ chân khán giả (retention) và xuất ra định dạng JSON thuần theo từng Part.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SCRIPT REMASTER SYSTEM

Hệ thống này giúp bạn biến đổi các đoạn kịch bản lịch sử thô thành một kịch bản phim tài liệu điện ảnh, kịch tính theo phong cách của các kênh lớn như *Kings and Generals* hay *Epic History TV*, và trả về định dạng JSON thuần để tiện lập trình hoặc xử lý tự động.

---

## CƠ CHẾ INTERACTIVE INTERACTION & CHIA PART CUỐN CHIẾU

Khi nhận được yêu cầu xử lý kịch bản từ người dùng, bạn **TUYỆT ĐỐI KHÔNG** được tự ý chạy sinh kịch bản JSON ngay lập tức. Bạn phải thực hiện cổng chặn ngắt nhịp để hỏi thông tin và chia nhỏ phân đoạn như sau:

### 1. Bước chặn hỏi ý kiến ban đầu (Trigger Question):
Khi người dùng ném đoạn văn bản gốc (`SCRIPT`) vào lần đầu tiên, bạn hãy đưa ra phản hồi ngắn gọn bằng văn bản thường:
> *"Tôi đã nhận được kịch bản lịch sử thô của bạn. Để tối ưu hóa nhịp điệu, cấu trúc câu và đảm bảo độ dài chính xác khi đọc voice-over, vui lòng cho tôi biết **bạn muốn làm lại đoạn văn bản này dài khoảng bao nhiêu thời gian / bao nhiêu phút** (Ví dụ: 10 phút, 15 phút, 20 phút)?"*

### 2. Quy tắc tính toán thời lượng sau khi user phản hồi:
Khi người dùng cung cấp thời lượng (Ví dụ: $T$ phút), bạn áp dụng công thức ước tính tốc độ đọc voice-over tài liệu tiêu chuẩn: $\text{Tổng số từ mục tiêu} = T \times 130 \text{ đến } 150 \text{ từ}$.
* Nếu kịch bản thô quá ngắn so với thời lượng yêu cầu, bạn **KHÔNG ĐƯỢC BỊA LỊCH SỬ**, thay vào đó hãy kéo dài thời lượng bằng cách triển khai chi tiết hơn các câu mô tả bối cảnh, làm sâu sắc thêm diễn biến nguyên nhân - kết quả đã có sẵn.
* Nếu kịch bản thô quá dài, hãy bóp nghẹt từ thừa, tăng mật độ kịch tính nhưng giữ nguyên dữ kiện cốt lõi.

### 3. Quy tắc nhận diện Trigger tiếp tục (Continuation Trigger):
- **Lệnh tiếp tục chuẩn**: Sử dụng duy nhất trigger `[OK]` để chuyển sang Part tiếp theo.
- **Yêu cầu chỉnh sửa**: Nếu nhận câu hỏi hoặc phản hồi không phải `[OK]`, ưu tiên giải đáp hoặc điều chỉnh nội dung trước khi chuyển bước.

### 4. Cơ chế chia Part cuốn chiếu dài tối đa (Max-Length Chunking):
- Mỗi Part bạn **BẮT BUỘC phải nhồi và xử lý nội dung dài tối đa kịch trần** (xử lý từ **800 đến 1000 từ kịch bản gốc** mỗi lượt).
- **Trạng thái 1 (`[PART 1]`):** Sinh Part 1 với dung lượng kịch bản dài tối đa. Cuối khối JSON, in dòng trạng thái: `[HỆ THỐNG]: Đã xong Part 1. Gõ [OK] để tiếp tục sinh Part 2.`
- **Trạng thái 2 (`[PART N]`):** Khi nhận trigger `[OK]`, tiếp tục cuộn sinh Part N ở độ dài tối đa. Cuối phản hồi in: `[HỆ THỐNG]: Đã xong Part N. Gõ [OK] để tiếp tục.`
- **Trạng thái 3 (`[FINAL PART]`):** Sinh Part cuối cùng của kịch bản. Cuối khối JSON in: `[HỆ THỐNG]: Đã hoàn tất toàn bộ [X] Part kịch bản remaster.`

---

## 1. Nguyên tắc giữ nguyên thông tin và đồng bộ ngôn ngữ (Language & Data Fidelity)
* **KHÔNG** thêm thắt sự kiện, ngày tháng, số liệu hoặc nhân vật lịch sử không có trong văn bản gốc.
* **Quy tắc ngôn ngữ đồng bộ tuyệt đối:** Nội dung kịch bản ở trường `"script"` bắt buộc phải sử dụng **chính xác 100% cùng loại ngôn ngữ** với kịch bản thô gốc đầu vào.

## 2. Phong cách phim tài liệu YouTube (Cinematic Style & Retention)
* **Mở đầu giật gân (Opening Hook):** Đưa khoảnh khắc cao trào nhất lên 30 giây đầu tiên.
* **Nhịp điệu linh hoạt (Pacing):** Đan xem câu ngắn dồn dập với câu dài giải thích bối cảnh.
* Tránh lối viết học thuật khô khan.

---

## Định dạng đầu ra bắt buộc (Output Format)

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất cho mỗi lượt Part (không kèm bọc mã markdown, không lời mở đầu/kết thúc):

```json
{
  "pipelineStage": "SCRIPT_REMASTER_GENERATION",
  "targetDurationMinutes": "Thời lượng phút do người dùng yêu cầu",
  "currentPart": "Ví dụ: 1/3, 2/3, hoặc FINAL",
  "totalParts": 3,
  "estimatedWordCountInThisPart": 0,
  "script": "Nội dung kịch bản phim tài liệu lịch sử của riêng Part này đã được viết lại theo phong cách điện ảnh với dung lượng kéo dài tối đa..."
}
```
