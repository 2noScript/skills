---
name: remaster
description: Biến đổi kịch bản thô thành kịch bản phim tài liệu YouTube chuyên nghiệp, tăng giữ chân khán giả (retention) và xuất ra định dạng JSON thuần chứa lời dẫn voice-over 100% sạch sẽ.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SCRIPT REMASTER SYSTEM

Hệ thống này biến đổi các đoạn kịch bản thô thành kịch bản phim tài liệu kịch tính, lôi cuốn theo phong cách YouTube retention cao (*Kings and Generals*, *Epic History TV*), trả về định dạng JSON thuần với lời dẫn voice-over 100% thuần túy.

---

## 1. QUY TẮC BẮT BUỘC: LỜI DẪN NARRATION THUẦN TÚY 100% (Pure Voice-Over Only)

Trường `"script"` trong JSON trả về **CHỈ ĐƯỢC CHỨA LỜI NARRATION NÓI THOẠI LÊN TIẾNG ĐỂ ĐỌC VOICE-OVER / THU ÂM TRỰC TIẾP**.

### ⛔ DANH SÁCH CẤM TUYỆT ĐỐI 8 LOẠI KÝ TỰ RÁC (Strictly Forbidden List):

1. ❌ **CẤM các thẻ phân cảnh / bối cảnh / mở đầu trong ngoặc vuông**:
   - Ví dụ CẤM: `[CINEMATIC OPENING - HIGH TENSION HOOK]`, `[SCENE 1]`, `[HOOK]`, `[GÓC QUAY FLYCAM]`, `[PART 1]`, `[INTRO]`, `[OUTRO]`.
2. ❌ **CẤM chỉ dẫn hình ảnh / tư liệu / B-roll trong ngoặc**:
   - Ví dụ CẤM: `(Hình ảnh: Bản đồ Châu Âu)`, `[B-Roll: Cảnh xe tăng chạy]`, `(Visual: Map animation)`, `(Cảnh tư liệu trắng đen)`.
3. ❌ **CẤM chỉ dẫn hiệu ứng âm thanh / âm nhạc trong ngoặc**:
   - Ví dụ CẤM: `(SOUND EFFECT: Low drone...)`, `(SFX: Tiếng nổ...)`, `(NHẠC NỀN DỒN DẬP)`, `(Nhạc nhẹ dần)`.
4. ❌ **CẤM nhãn tên người đọc / Speaker prefixes**:
   - Ví dụ CẤM: `NARRATOR (VOICEOVER):`, `NARRATOR:`, `VOICEOVER:`, `LỜI DẪN:`, `NGƯỜI DẪN CHUYỆN:`, `DẪN CHƯƠNG TRÌNH:`.
5. ❌ **CẤM nhãn tên nhân vật phát biểu / trích dẫn thoại**:
   - Ví dụ CẤM: `Tướng A (nói):`, `Napoleon:`, `[Giọng nhân vật]:`. (Nếu là lời nói của nhân vật, hãy viết trực tiếp thành câu đọc liền mạch: *Napoleon từng tuyên bố rằng...*).
6. ❌ **CẤM chèn mốc thời gian / Timestamps**:
   - Ví dụ CẤM: `[00:00 - 02:30]`, `(01:15)`, `Timestamp: 05:00`.
7. ❌ **CẤM ký tự định dạng Markdown tiêu đề / nhấn mạnh**:
   - Ví dụ CẤM: `# Tiêu đề`, `## Part 1`, `**Giai đoạn 1:**`, `> Lời dẫn`, `---`.
8. ❌ **CẤM ghi chú biên tập / chào hỏi / nhận xét của AI**:
   - Ví dụ CẤM: `(Lưu ý: đoạn này có thể cắt bớt)`, `Hi vọng kịch bản này đúng ý bạn!`, `(Tiếp tục ở Part 2)`.

---

### ✅ VÍ DỤ ĐẦU RA SẠCH CHUẨN MỰC (100% Clean Voice-Over Narration):

```text
Vào một đêm mùa đông lạnh giá năm 1941, tuyết rơi dày đặc trên những cánh rừng ngoại ô Moscow. Đội quân hùng mạnh từng quét sạch Châu Âu giờ đây đang phải đối mặt với một kẻ thù nguy hiểm hơn nhiều lần, đó chính là cái lạnh âm 40 độ C và sự kháng cự kiên cường của quân đội Liên Xô.
```

> **MỤC TIÊU VÀNG:** Văn bản trong trường `"script"` sẽ được hệ thống đưa trực tiếp vào công cụ chuyển đổi văn bản thành giọng nói AI (Text-to-Speech). Mọi ký tự trong ngoặc hoặc nhãn tên người đọc sẽ làm hỏng giọng đọc Voice-over.

---

## 2. QUY TẮC ĐỒNG BỘ NGÔN NGỮ & DỮ KIỆN (Language & Fact Rules)
* **100% CÙNG LOẠI NGÔN NGỮ VỚI KỊCH BẢN THÔ ĐẦU VÀO**:
  - Kịch bản gốc là **TIẾNG VIỆT** $\rightarrow$ Trường `"script"` BẮT BUỘC TRẢ VỀ 100% TIẾNG VIỆT.
  - Kịch bản gốc là **TIẾNG ANH** $\rightarrow$ Trường `"script"` BẮT BUỘC TRẢ VỀ 100% TIẾNG ANH.
  - **TUYỆT ĐỐI CẤM TỰ Ý DỊCH SANG NGÔN NGỮ KHÁC.**
* **GIỮ NGUYÊN DỮ KIỆN GỐC**: Không bịa đặt sự kiện, ngày tháng hoặc nhân vật không có trong kịch bản thô.

---

## 3. CƠ CHẾ CHIA PART CUỐN CHIẾU (Max-Length Chunking)
- **Part 1 (`[PART 1]`):** Lập tức sinh JSON cho Part 1 với dung lượng kịch bản dài tối đa (800 - 1000 từ).
- **Part tiếp theo (`[PART N]`):** Khi nhận trigger `[OK]`, sinh JSON cho Part N ở độ dài tối đa.
- **Part cuối cùng (`[FINAL PART]`):** Sinh JSON cho Part cuối (`currentPart: "FINAL"`).
- **Quy tắc JSON thuần**: Chỉ trả về duy nhất 1 khối JSON hợp lệ, tuyệt đối không xuất văn bản ngoài khối JSON.

---

## Định dạng đầu ra bắt buộc (Output Format)

Chỉ trả về 1 đối tượng JSON hợp lệ duy nhất:

```json
{
  "targetDurationMinutes": "Thời lượng phút do người dùng yêu cầu",
  "currentPart": "Ví dụ: 1/3, 2/3, hoặc FINAL",
  "totalParts": 3,
  "estimatedWordCountInThisPart": 0,
  "script": "Văn bản lời dẫn Voice-over liên tục thuần túy 100%. Tuyệt đối không chứa [CINEMATIC...], (SOUND EFFECT...), NARRATOR: hay bất kỳ thẻ ghi chú nào."
}
```
