---
name: shot
description: Phân rã kịch bản thô thành linh hồn các phân đoạn Shot & Nhịp ngắt Voice-over (8-14 từ/shot) theo từng Part cuốn chiếu, đảm bảo khôi phục nguyên văn 100% kịch bản gốc tuyệt đối không bỏ sót từ nào.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SHOT BREAKDOWN SYSTEM (CORE SOUL)

Đây là **LINH HỒN CỦA NỘI DUNG PRODUCER**. Hệ thống này quét tuần tự từ đầu đến cuối kịch bản phim tài liệu lịch sử và phân rã thành các phân đoạn Shot ngắn chuẩn nhịp thoại voice-over (`line`) theo cơ chế chia Part cuốn chiếu để **đảm bảo 100% không bao giờ bỏ sót hay tóm tắt bất kỳ câu chữ nào**.

---

## 1. QUY TẮC CƠ CHẾ CHIA PART CUỐN CHIẾU CHỐNG BỎ SÓT (Anti-Omission Part Chunking)

- **Giới hạn tối đa 30 Shots/Part (hoặc ~400–500 từ/Part)**: Đối với kịch bản dài, tự động tính toán tổng số Part (`totalParts`). Mỗi lượt phản hồi phân rã tối đa 30 Shots để đảm bảo độ chính xác tuyệt đối.
- **Trạng thái 1 (`[PART 1]`):** Lập tức phân rã và trả về JSON cho Part 1 (Ví dụ: Shots 1–30).
- **Trạng thái 2 (`[PART N]`):** Khi nhận duy nhất trigger `[OK]`, tiếp tục đọc NỐI TIẾP NGAY TỪ TỪ TIẾP THEO của kịch bản gốc để phân rã Part N (Ví dụ: Shots 31–60). CẤM BỎ SÓT HAY NHẢY CÂU.
- **Trạng thái 3 (`[FINAL PART]`):** Phân rã đoạn cuối cùng của kịch bản và đánh dấu `"isScriptFullyCovered": true`.

---

## 2. NGUYÊN TẮC BẢO TOÀN NGUYÊN VĂN 100% (Verbatim Conservation Rule)

- **CẤM TÓM TẮT - CẤM BỎ SÓT - CẤM ĐỔI TỪ**: Trường `line` cho từng shot là nguyên văn kịch bản gốc. Khi gộp toàn bộ các câu `line` từ tất cả các Part, nội dung phải khớp **ĐÚNG 100% TỪNG CHỮ** với kịch bản thô ban đầu.
- **Quy tắc "Chép nguyên văn → Ngắt câu" (Copy-First-Then-Split)**: Quét câu kịch bản gốc, giữ nguyên từng chữ và chỉ chèn ranh giới chia shot theo nhịp thở voice-over.

---

## 3. QUY TẮC ĐỒNG BỘ NGÔN NGỮ BẮT BUỘC (Strict Language Alignment Rule)

- **CÙNG LOẠI NGÔN NGỮ VỚI KỊCH BẢN ĐẦU VÀO**:
  - Nếu kịch bản đầu vào là **TIẾNG VIỆT** $\rightarrow$ Trường `line` 100% là **TIẾNG VIỆT**.
  - Nếu kịch bản đầu vào là **TIẾNG ANH** $\rightarrow$ Trường `line` 100% là **TIẾNG ANH**.
  - **TỰ Ý DỊCH SANG NGÔN NGỮ KHÁC LÀ VI PHẠM NGHIÊM TRỌNG.**

---

## 4. CHUẨN NHỊP ĐỌC VOICE-OVER & TÁCH SHOT (Pacing & Rhythm)

- **Độ dài lý tưởng mỗi Shot**: Từ **8 đến 14 từ** (tương đương 3–5 giây voice-over/shot).
- **Ngắt theo cụm ý nghĩa (Semantic Clause Chunking)**:
  - Ngắt tại dấu phẩy, dấu chấm, dấu chấm phẩy hoặc ranh giới mệnh đề ngữ pháp tự nhiên.
  - Cấm ngắt giữa chừng cụm từ cố định (Ví dụ: Không ngắt giữa "Đại đế" và "Napoléon", không ngắt giữa "Chiến dịch" và "Normandy").
- **Escape ngoặc kép**: Mọi dấu ngoặc kép trong `line` đổi thành `\"` hoặc `''` để đảm bảo chuỗi JSON không bị vỡ.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất cho mỗi Part:

```json
{
  "currentPart": "1/2",
  "totalParts": 2,
  "isScriptFullyCovered": false,
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
