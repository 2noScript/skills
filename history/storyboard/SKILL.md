---
name: storyboard
description: Phân rã kịch bản phim tài liệu lịch sử thành Storyboard JSON theo từng Part (30–45 shot/Part), tự động lồng ghép placeholder [Tên_Asset], mô tả imagePrompt tĩnh 120-220 từ nhất quán bối cảnh lịch sử, và đảm bảo khôi phục tối thiểu 90% văn bản gốc.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY STORYBOARD GENERATION SYSTEM

Hệ thống này phân rã kịch bản phim tài liệu lịch sử thành Storyboard JSON theo từng Part độc lập (chống tràn token), gắn placeholder `[Tên_Asset]` chuẩn xác và sinh `imagePrompt` tối ưu cho AI tạo ảnh.

---

## 0. QUY TẮC ĐIỀU HƯỚNG TẠI CÁC ĐIỂM DỪNG (Checkpoint Navigation)

Mỗi khi hệ thống hoàn tất một Part, dòng [HỆ THỐNG] ở cuối phản hồi luôn luôn có khối lệnh điều hướng:

- **[OK]** – Tiếp tục tạo Part tiếp theo.
- **[QUAY LẠI]** – Yêu cầu làm lại Part vừa xong.
- **[TẠO THUMBNAIL]** – Thiết kế ý tưởng và Prompt Image cho Thumbnail (ở Part cuối).

---

## QUY TẮC THỰC THI STORYBOARD THEO PART

### 1. Đối chiếu tuyến tính & Khôi phục nguyên văn (Match Threshold ≥ 90%)
- Quét tuần tự từ đầu đến cuối kịch bản gốc.
- **Quy tắc "Sao chép trước, cắt sau" (Copy-First-Then-Split)**: Giữ nguyên y hệt từng từ của kịch bản gốc, chỉ chèn dấu ngắt để tách thành các shot (8–14 từ/shot, tối đa 20 từ). Cấm tuyệt đối tóm tắt hay viết lại câu.
- Khi gộp toàn bộ trường `line` của một Part, phải đạt tối thiểu 90% số từ kịch bản gốc tương ứng.

### 2. An toàn dữ liệu & Placeholder Asset
- **Escape ngoặc kép**: Mọi ngoặc kép trong `line` phải đổi thành `\"` hoặc `''`.
- **Gắn `[Tên_Asset]` chính xác**: Copy nguyên văn từng ký tự tên Asset từ Asset Library đã chốt. Cấm tự tạo tên biến thể khác.
- **Chống trôi bối cảnh lịch sử (Historical Context Drift Prevention)**: Mọi chi tiết trang phục, vũ khí, địa hình trong `imagePrompt` phải nhất quán tuyệt đối với `historicalContext` đã chốt.

### 3. Quy tắc `imagePrompt`
- **Độ dài**: 120–220 từ. Mô tả diện mạo vật lý tĩnh: Subject, Appearance, Environment, bố cục 3 lớp (Foreground / Middleground / Background), khoảng cách & vị trí camera physical.
- **Cấm tuyệt đối từ khóa phong cách**: Không dùng `photorealistic`, `cinematic`, `4k`, `8k`, `3D render`, `close-up`, `wide-angle`, `dramatic lighting`. Chỉ mô tả kết cấu vật lý thô (vết xước thép, nếp nhăn vải, ánh sáng tự nhiên).

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Trả về duy nhất 1 đối tượng JSON hợp lệ cho mỗi Part:

```json
{
  "pipelineStage": "STORYBOARD_GENERATION",
  "currentPart": "1/~3 (hoặc FINAL cho Part cuối)",
  "startingShot": 1,
  "endingShot": 35,
  "totalShots": 35,
  "rawScriptWordCount": 420,
  "processedScriptWordCount": 415,
  "shots": [
    {
      "shot": 1,
      "line": "Trích nguyên văn kịch bản gốc cho shot 1 (đã escape ngoặc kép)...",
      "imagePrompt": "Mô tả chi tiết 120-220 từ có chứa [Tên_Asset_1] và [Tên_Asset_2] chuẩn xác..."
    }
  ]
}
```
