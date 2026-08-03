---
name: shot
description: Phân rã kịch bản thô thành linh hồn các phân đoạn Shot & Nhịp ngắt Voice-over (8-14 từ/shot) dựa trên Ngữ cảnh Toàn bộ Kịch bản Remaster và Đoạn kịch bản mục tiêu của Batch, đảm bảo khôi phục nguyên văn 100% kịch bản gốc tuyệt đối không bỏ sót từ nào.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SHOT BREAKDOWN SYSTEM (CONTEXT-AWARE BATCHING)

Đây là **LINH HỒN CỦA NỘI DUNG PRODUCER**. Hệ thống này nhận **Toàn bộ Kịch bản Remaster** (làm ngữ cảnh toàn cục) và **Đoạn kịch bản mục tiêu của Batch** để phân rã thành các phân đoạn Shot ngắn chuẩn nhịp thoại voice-over (`line`) bảo toàn $100\%$ kịch bản gốc mà không bỏ sót bất kỳ từ nào.

---

## 1. NGUYÊN TẮC BẢO TOÀN NGUYÊN VĂN 100% (Verbatim Conservation Rule)

- **CẤM TÓM TẮT - CẤM BỎ SÓT - CẤM ĐỔI TỪ**: Trường `line` cho từng shot là nguyên văn kịch bản gốc. Khi gộp toàn bộ các câu `line` từ tất cả các Batch, nội dung phải khớp **ĐÚNG 100% TỪNG CHỮ** với kịch bản thô ban đầu.
- **Xử lý 100% Đoạn Kịch bản Mục tiêu**: AI dựa vào Ngữ cảnh Toàn bộ Kịch bản để nắm rõ mạch chuyện, nhưng BẮT BUỘC phân rã ĐỦ 100% câu chữ của Đoạn Kịch bản Mục tiêu được chỉ định trong Batch.

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

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất cho mỗi Batch:

```json
{
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
