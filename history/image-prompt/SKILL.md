---
name: image-prompt
description: Sinh Image Prompt chi tiết (120-220 từ) cho từng Batch Shot (30 shots/batch) dựa trên Ngữ cảnh Toàn bộ Kịch bản Remaster và Thư viện Asset lịch sử, bảo toàn 100% mạch phim và không bao giờ bỏ sót shot nào.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY IMAGE PROMPT GENERATION SYSTEM (CONTEXT-AWARE BATCHING)

Hệ thống này nhận **Toàn bộ Kịch bản Remaster** (làm ngữ cảnh toàn cục), **Thư viện Asset** và **Danh sách Batch Shot** để tạo `imagePrompt` tĩnh 120-220 từ cho từng Shot.

---

## 1. QUY TẮC HIỂU NGỮ CẢNH TOÀN CỤC & BẢO TOÀN MẠCH PHIM (Context Continuity Rule)

- **Hiểu Toàn bộ Mạch Phim**: AI đọc toàn văn Kịch bản Remaster gốc để nắm bắt cao trào, bầu không khí, thời gian trong ngày và tính liên tục về mặt thị giác (Visual Continuity) giữa các shot.
- **Xử lý 100% Batch Target**: Dù được cung cấp toàn bộ Kịch bản Remaster làm ngữ cảnh, AI chỉ tập trung xuất ra JSON chứa `imagePrompt` cho **ĐÚNG DANH SÁCH BATCH SHOTS ĐƯỢC CHỈ ĐỊNH** (Ví dụ: 30 Shots). CẤM BỎ SÓT SHOT NÀO TRONG BATCH.
- **Quy tắc đầu ra thuần JSON**: Trả về duy nhất 1 đối tượng JSON hợp lệ chứa mảng `shots`. Tuyệt đối không in thêm văn bản dẫn dắt bên ngoài khối JSON.

---

## 2. QUY TẮC PHÁT TRIỂN IMAGE PROMPT

### 2.1 An toàn Asset & Placeholder
- **Gắn `[Tên_Asset]` chính xác**: Copy nguyên văn từng ký tự tên Asset từ Asset Library được cung cấp. Cấm tự tạo tên biến thể khác.
- **Chống trôi bối cảnh lịch sử**: Mọi chi tiết trang phục, vũ khí, địa hình trong `imagePrompt` phải nhất quán tuyệt đối với mô tả của Asset.

### 2.2 Tiêu chuẩn mô tả `imagePrompt`
- **Độ dài**: 120–220 từ bằng Tiếng Anh (dành riêng cho AI sinh ảnh).
- **Mô tả diện mạo vật lý tĩnh**: Subject, Appearance, Environment, bố cục 3 lớp (Foreground / Middleground / Background), khoảng cách & vị trí camera physical.
- **Cấm từ khóa phong cách**: Không dùng `photorealistic`, `cinematic`, `4k`, `8k`, `3D render`, `close-up`, `wide-angle`, `dramatic lighting`. Chỉ mô tả kết cấu vật lý thô.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất:

```json
{
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
