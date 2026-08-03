---
name: shot
description: Phân rã kịch bản thô thành linh hồn các phân đoạn Shot & Nhịp ngắt Voice-over (8-14 từ/shot), đảm bảo khôi phục nguyên văn 100% kịch bản gốc mà không bịa hay bỏ sót một từ nào.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SHOT BREAKDOWN SYSTEM (CORE SOUL)

Đây là **LINH HỒN CỦA NỘI DUNG PRODUCER**. Hệ thống này quét tuần tự kịch bản phim tài liệu lịch sử và phân rã thành các phân đoạn Shot ngắn chuẩn nhịp thoại voice-over (`line`) để khớp hoàn hảo với nhịp dựng video điện ảnh và phụ đề.

---

## 1. NGUYÊN TẮC BẢO TOÀN NGUYÊN VĂN 100% (Verbatim Conservation Rule)

- **CẤM TÓM TẮT - CẤM BỎ SÓT - CẤM ĐỔI TỪ**: Trường `line` cho từng shot là nguyên văn kịch bản gốc. Khi gộp toàn bộ các câu `line` từ Shot 1 đến Shot N, nội dung phải khớp **ĐÚNG 100% TỪNG CHỮ** với kịch bản thô ban đầu.
- **Quy tắc "Chép nguyên văn → Ngắt câu" (Copy-First-Then-Split)**: Quét câu kịch bản gốc, giữ nguyên từng chữ và chỉ chèn ranh giới chia shot theo nhịp thở voice-over.

---

## 2. QUY TẮC ĐỒNG BỘ NGÔN NGỮ BẮT BUỘC (Strict Language Alignment Rule)

- **CÙNG LOẠI NGÔN NGỮ VỚI KỊCH BẢN ĐẦU VÀO**:
  - Nếu kịch bản đầu vào là **TIẾNG VIỆT** $\rightarrow$ Trường `line` 100% là **TIẾNG VIỆT**.
  - Nếu kịch bản đầu vào là **TIẾNG ANH** $\rightarrow$ Trường `line` 100% là **TIẾNG ANH**.
  - **TỰ Ý DỊCH SANG NGÔN NGỮ KHÁC LÀ VI PHẠM NGHIÊM TRỌNG.**

---

## 3. CHUẨN NHỊP ĐỌC VOICE-OVER & TÁCH SHOT (Pacing & Rhythm)

- **Độ dài lý tưởng mỗi Shot**: Từ **8 đến 14 từ** (tương đương 3–5 giây voice-over/shot).
- **Ngắt theo cụm ý nghĩa (Semantic Clause Chunking)**:
  - Ngắt tại dấu phẩy, dấu chấm, dấu chấm phẩy hoặc ranh giới mệnh đề ngữ pháp tự nhiên.
  - Cấm ngắt giữa chừng cụm từ cố định (Ví dụ: Không ngắt giữa "Đại đế" và "Napoléon", không ngắt giữa "Chiến dịch" và "Normandy").
- **Escape ngoặc kép**: Mọi dấu ngoặc kép trong `line` đổi thành `\"` hoặc `''` để đảm bảo chuỗi JSON không bị vỡ.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất (không bọc mã markdown, không văn bản hỏi đáp thừa):

```json
{
  "currentPart": "1/1",
  "totalShots": 25,
  "rawScriptWordCount": 350,
  "processedScriptWordCount": 350,
  "shots": [
    {
      "shot": 1,
      "line": "Trích nguyên văn kịch bản gốc cho shot 1 bằng đúng ngôn ngữ đầu vào (8-14 từ)..."
    },
    {
      "shot": 2,
      "line": "Trích nguyên văn kịch bản gốc cho shot 2..."
    }
  ]
}
```
