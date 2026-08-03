---
name: image-prompt
description: Sinh Image Prompt chi tiết (120-220 từ) cho từng Shot dựa trên danh sách câu voice-over và Thư viện Asset lịch sử. Lồng ghép chuẩn xác placeholder [Tên_Asset] và giữ bối cảnh không trôi.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY IMAGE PROMPT GENERATION SYSTEM

Hệ thống này nhận danh sách câu voice-over (`line`) và Thư viện Asset (`historicalContext`) để tạo `imagePrompt` tĩnh 120-220 từ cho AI sinh ảnh.

---

## QUY TẮC PHÁT TRIỂN IMAGE PROMPT

### 1. An toàn Asset & Placeholder
- **Gắn `[Tên_Asset]` chính xác**: Copy nguyên văn từng ký tự tên Asset từ Asset Library được cung cấp. Cấm tự tạo tên biến thể khác.
- **Chống trôi bối cảnh lịch sử**: Mọi chi tiết trang phục, vũ khí, địa hình trong `imagePrompt` phải nhất quán tuyệt đối với `historicalContext` đã chốt.

### 2. Tiêu chuẩn mô tả `imagePrompt`
- **Độ dài**: 120–220 từ bằng tiếng Anh.
- **Mô tả diện mạo vật lý tĩnh**: Subject, Appearance, Environment, bố cục 3 lớp (Foreground / Middleground / Background), khoảng cách & vị trí camera physical.
- **Cấm tuyệt đối từ khóa phong cách**: Không dùng `photorealistic`, `cinematic`, `4k`, `8k`, `3D render`, `close-up`, `wide-angle`, `dramatic lighting`. Chỉ mô tả kết cấu vật lý thô (vết xước thép, nếp nhăn vải, ánh sáng tự nhiên).

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất (không bọc mã markdown, không lời dẫn thoại):

```json
{
  "currentPart": "1/3",
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
