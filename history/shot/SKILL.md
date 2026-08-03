---
name: shot
description: Phân rã kịch bản phim tài liệu lịch sử thành danh sách các Shot với câu thoại voice-over (8-14 từ/shot), đảm bảo khôi phục tối thiểu 90% văn bản gốc và không bịa câu từ.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SHOT BREAKDOWN SYSTEM

Hệ thống này phân rã kịch bản phim tài liệu lịch sử thành các phân đoạn Shot ngắn với nhịp đọc voice-over `line` tiêu chuẩn.

---

## QUY TẮC PHÂN RÃ SHOT

### 1. Đối chiếu tuyến tính & Khôi phục nguyên văn (Match Threshold ≥ 90%)
- Quét tuần tự từ đầu đến cuối kịch bản gốc.
- **Quy tắc "Sao chép trước, ngắt sau" (Copy-First-Then-Split)**: Giữ nguyên y hệt từng từ của kịch bản gốc, chỉ chèn dấu ngắt để tách thành các shot (8–14 từ/shot, tối đa 20 từ). Cấm tuyệt đối tóm tắt hay tự ý sửa câu.
- Khi gộp toàn bộ trường `line` của một Part, phải đạt tối thiểu 90% số từ kịch bản gốc tương ứng.

### 2. An toàn dữ liệu
- **Escape ngoặc kép**: Mọi ngoặc kép trong `line` phải đổi thành `\"` hoặc `''`.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất (không bọc mã markdown, không lời dẫn thoại):

```json
{
  "currentPart": "1/3",
  "startingShot": 1,
  "endingShot": 35,
  "totalShots": 35,
  "rawScriptWordCount": 420,
  "processedScriptWordCount": 415,
  "shots": [
    {
      "shot": 1,
      "line": "Trích nguyên văn kịch bản gốc cho shot 1 (8-14 từ)..."
    },
    {
      "shot": 2,
      "line": "Trích nguyên văn kịch bản gốc cho shot 2..."
    }
  ]
}
```
