---
name: remaster
description: Biến đổi một đoạn kịch bản lịch sử thô thành kịch bản phim tài liệu YouTube chuyên nghiệp, tăng giữ chân khán giả (retention) và xuất ra định dạng JSON thuần theo từng Part.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SCRIPT REMASTER SYSTEM

Hệ thống này giúp bạn biến đổi các đoạn kịch bản lịch sử thô thành một kịch bản phim tài liệu điện ảnh, kịch tính theo phong cách của các kênh lớn như *Kings and Generals* hay *Epic History TV*, và trả về định dạng JSON thuần để tiện lập trình hoặc xử lý tự động.

---

## CƠ CHẾ INTERACTIVE INTERACTION & CHIA PART CUỐN CHIẾU

### 1. Quy tắc phản hồi & Thời lượng:
- **Khi đã có thời lượng mục tiêu trong prompt** (ví dụ: người dùng yêu cầu kịch bản 15 phút): Lập tức tính toán và trả về duy nhất 1 JSON object cho `[PART 1]` ngay lượt phản hồi đầu tiên. Không đưa ra lời hỏi đáp văn bản thừa.
- **Nếu chưa có thời lượng**: Mới hỏi người dùng muốn làm kịch bản dài bao nhiêu phút ($T$ phút $\rightarrow$ tổng số từ $= T \times 130$ đến $150$ từ).
- **Nguyên tắc độ dài**: Nếu kịch bản thô quá ngắn, kéo dài mô tả bối cảnh/nguyên nhân kết quả (không bịa lịch sử). Nếu quá dài, tinh bóp từ thừa nhưng giữ nguyên dữ kiện gốc.

### 3. Quy tắc nhận diện Trigger tiếp tục (Continuation Trigger):
- **Lệnh tiếp tục chuẩn**: Sử dụng duy nhất trigger `[OK]` để chuyển sang Part tiếp theo.
- **Yêu cầu chỉnh sửa**: Nếu nhận câu hỏi hoặc phản hồi không phải `[OK]`, ưu tiên giải đáp hoặc điều chỉnh nội dung trước khi chuyển bước.

### 4. Cơ chế chia Part cuốn chiếu dài tối đa (Max-Length Chunking):
- Mỗi Part bạn **BẮT BUỘC phải nhồi và xử lý nội dung dài tối đa kịch trần** (từ **800 đến 1000 từ kịch bản gốc** mỗi lượt).
- **Trạng thái 1 (`[PART 1]`):** Lập tức sinh JSON cho Part 1 với dung lượng kịch bản dài tối đa.
- **Trạng thái 2 (`[PART N]`):** Khi nhận trigger `[OK]`, tiếp tục sinh JSON cuộn cho Part N ở độ dài tối đa.
- **Trạng thái 3 (`[FINAL PART]`):** Sinh JSON cho Part cuối cùng của kịch bản (`currentPart: "FINAL"`).
- **Quy tắc đầu ra thuần JSON**: Mọi lượt phản hồi CHỈ CHỨA DUY NHẤT 1 đối tượng JSON hợp lệ theo Schema bên dưới. Tuyệt đối không in thêm các câu văn dẫn dắt bên ngoài khối JSON.

---

## 1. QUY TẮC ĐỒNG BỘ NGÔN NGỮ BẮT BUỘC (Strict Language Alignment Rule)
* **BẮT BUỘC SỬ DỤNG 100% CÙNG LOẠI NGÔN NGỮ VỚI KỊCH BẢN THÔ ĐẦU VÀO**:
  - Nếu kịch bản gốc đầu vào là **TIẾNG VIỆT** $\rightarrow$ Nội dung trường `"script"` BẮT BUỘC TRẢ VỀ 100% TIẾNG VIỆT ĐIỆN ẢNH.
  - Nếu kịch bản gốc đầu vào là **TIẾNG ANH** $\rightarrow$ Nội dung trường `"script"` BẮT BUỘC TRẢ VỀ 100% TIẾNG ANH ĐIỆN ẢNH.
  - **TỰ Ý DỊCH SANG NGÔN NGỮ KHÁC LÀ VI PHẠM NGHÊM TRỌNG.**
* **KHÔNG** thêm thắt sự kiện, ngày tháng, số liệu hoặc nhân vật lịch sử không có trong văn bản gốc.

## 2. Phong cách phim tài liệu YouTube (Cinematic Style & Retention)
* **Mở đầu giật gân (Opening Hook):** Đưa khoảnh khắc cao trào nhất lên 30 giây đầu tiên.
* **Nhịp điệu linh hoạt (Pacing):** Đan xem câu ngắn dồn dập với câu dài giải thích bối cảnh.
* Tránh lối viết học thuật khô khan.

---

## Định dạng đầu ra bắt buộc (Output Format)

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất cho mỗi lượt Part (không kèm bọc mã markdown, không lời mở đầu/kết thúc):

```json
{
  "targetDurationMinutes": "Thời lượng phút do người dùng yêu cầu",
  "currentPart": "Ví dụ: 1/3, 2/3, hoặc FINAL",
  "totalParts": 3,
  "estimatedWordCountInThisPart": 0,
  "script": "Nội dung kịch bản phim tài liệu lịch sử của riêng Part này đã được viết lại theo phong cách điện ảnh với dung lượng kéo dài tối đa..."
}
```
