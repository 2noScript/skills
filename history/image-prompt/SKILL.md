---
name: image-prompt
description: Sinh Image Prompt chi tiết (120-220 từ) cho từng Shot theo từng Part cuốn chiếu. Lồng ghép chuẩn xác placeholder [Tên_Asset] và sử dụng duy nhất trigger [OK] để sinh Part tiếp theo.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY IMAGE PROMPT GENERATION SYSTEM

Hệ thống này nhận danh sách câu voice-over (`line`) và Thư viện Asset (`historicalContext`) để tạo `imagePrompt` tĩnh 120-220 từ cho từng Shot theo cơ chế chia Part cuốn chiếu dài tối đa.

---

## 1. QUY TẮC CƠ CHẾ CHIA PART CUỐN CHIẾU (Part Chunking)

- **Dung lượng tối đa mỗi Part (Tối đa 25-30 Shots/Part)**: Mỗi Part được phép xử lý **tối đa từ 25 đến 30 Shots**, tự động tính toán tổng số Part (`totalParts`). Mỗi lượt phản hồi trả về duy nhất 1 JSON object cho Part tương ứng.
- **Trạng thái 1 (`[PART 1]`):** Lập tức sinh JSON cho Part 1 (Ví dụ: Shots 1–30).
- **Trạng thái 2 (`[PART N]`):** Khi nhận duy nhất trigger `[OK]`, tiếp tục cuộn sinh JSON cho Part N (Ví dụ: Shots 31–60).
- **Trạng thái 3 (`[FINAL PART]`):** Sinh JSON cho Part cuối cùng (`currentPart: "FINAL"`).
- **Quy tắc đầu ra thuần JSON**: Mọi lượt phản hồi CHỈ CHỨA DUY NHẤT 1 đối tượng JSON hợp lệ. Không in văn bản dẫn dắt bên ngoài khối JSON.

---

## 2. QUY TẮC PHÁT TRIỂN IMAGE PROMPT

### 2.1 An toàn Asset & Placeholder
- **Gắn `[Tên_Asset]` chính xác**: Copy nguyên văn từng ký tự tên Asset từ Asset Library được cung cấp. Cấm tự tạo tên biến thể khác.
- **Chống trôi bối cảnh lịch sử**: Mọi chi tiết trang phục, vũ khí, địa hình trong `imagePrompt` phải nhất quán tuyệt đối với `historicalContext`.

### 2.2 Tiêu chuẩn mô tả `imagePrompt`
- **Độ dài**: 120–220 từ bằng Tiếng Anh (dành riêng cho AI sinh ảnh).
- **Mô tả diện mạo vật lý tĩnh**: Subject, Appearance, Environment, bố cục 3 lớp (Foreground / Middleground / Background), khoảng cách & vị trí camera physical.
- **Cấm từ khóa phong cách**: Không dùng `photorealistic`, `cinematic`, `4k`, `8k`, `3D render`, `close-up`, `wide-angle`, `dramatic lighting`. Chỉ mô tả kết cấu vật lý thô.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất cho mỗi Part:

```json
{
  "currentPart": "1/3",
  "totalParts": 3,
  "shots": [
    {
      "shot": 1,
      "imagePrompt": "Detailed physical static scene 120-220 words featuring [Tên_Asset]..."
    },
    {
      "shot": 2,
      "imagePrompt": "Detailed physical static scene 120-220 words..."
    }
  ]
}
```
