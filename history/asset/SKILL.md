---
name: asset
description: Bóc tách kịch bản thô thành Thư viện Asset (Nhân vật, Sinh vật sống, Thực thể tập thể, Vật thể) với mô tả tĩnh tích hợp 100% bối cảnh lịch sử, quân phục và niên đại dưới dạng JSON thuần.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY ASSET EXTRACTION SYSTEM (MASTERCLASS)

Hệ thống này bóc tách toàn bộ tài nguyên trực quan (Asset) từ kịch bản phim tài liệu lịch sử thành dữ liệu JSON chuẩn với mô tả `description` tích hợp **100% bối cảnh lịch sử, niên đại, quân phục, trang bị và vật liệu thực tế**.

---

## 1. QUY TẮC ĐỒNG BỘ NGÔN NGỮ BẮT BUỘC (Strict Language Alignment Rule)

- **CÙNG LOẠI NGÔN NGỮ VỚI KỊCH BẢN ĐẦU VÀO**: Tên Asset (`name`) và Mô tả chi tiết (`description`) BẮT BUỘC trả về bằng **CHÍNH XÁC 100% CÙNG LOẠI NGÔN NGỮ** với kịch bản thô đầu vào.
  - Nếu kịch bản đầu vào là **TIẾNG VIỆT** $\rightarrow$ Toàn bộ thông tin Asset trả về 100% bằng **TIẾNG VIỆT**.
  - Nếu kịch bản đầu vào là **TIẾNG ANH** $\rightarrow$ Toàn bộ thông tin Asset trả về 100% bằng **TIẾNG ANH**.
  - **TỰ Ý DỊCH SANG NGÔN NGỮ KHÁC LÀ VI PHẠM NGHIÊM TRỌNG.**

---

## 2. QUY TẮC PHÂN LOẠI & MÔ TẢ CHI TIẾT CHUẨN LỊCH SỬ

### 2.1 Phân loại Asset
- **Sinh vật sống (`characters`)**: Người, tướng lĩnh, binh sĩ tập thể, quân đoàn, ngựa chiến, gia súc...
- **Vật vô tri (`objects`)**: Vũ khí, xe tăng, tàu chiến, quân phục, công trình, di tích, lá cờ...
- **Định dạng Tên Asset (name)**: Đặt tên Asset gọn gàng không bọc ngoặc vuông (Ví dụ: `Linh_Thuy_Dien_1709`, `Linh_My_1944`, `Chien_Thuyen_Bach_Dang_1288`). Khi lồng ghép trong `imagePrompt` của Skill Image Prompt mới dùng định dạng `[Tên_Asset]`.

### 2.2 Quy chuẩn Tích hợp Bối cảnh Lịch sử vào `description`
Trong mỗi trường `description`, BẮT BUỘC mô tả diện mạo vật lý tĩnh bám sát 5 lớp chuẩn niên đại lịch sử:
1. **Niên đại & Phe phái**: Ghi rõ mốc năm/thế kỷ và phe phái/quốc gia (Ví dụ: *Niên đại trận Poltava năm 1709, Quân Thụy Điển*).
2. **Mũ nón & Kiểu tóc**: Mô tả chính xác kiểu tóc, búi tóc hoặc loại mũ (Ví dụ: *Mũ gập ba góc Tricorn bằng nỉ đen*, *Mũ sắt M1*, *Búi tóc cạo đầu chuẩn nhà Trần*).
3. **Quân phục & Trang phục**: Màu sắc, kiểu dáng nẹp áo, khuy áo, dây đai (Ví dụ: *Áo khoác dạ màu xanh da trời nẹp da vàng Thụy Điển*, *Áo giáp lá liễu thêu rồng*).
4. **Vũ khí & Trang bị đi kèm**: Tên chính xác loại vũ khí cầm tay chuẩn niên đại (Ví dụ: *Súng kíp hỏa mai Flintlock khóa đá lửa*, *Trường đao thép*, *Súng M1 Garand*).
5. **Vật liệu & Bề mặt thô**: Vải len dệt thô, khuy đồng xỉn màu, vết bùn đất bám lai áo, giáp sắt rèn tay có vết xước kim loại nhẹ.
- **CẤM TUYỆT ĐỐI**: Không mô tả hành động, cảm xúc, thời tiết hay góc máy.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về duy nhất 1 đối tượng JSON hợp lệ (không kèm bọc mã markdown, không văn bản hỏi đáp thừa):

```json
{
  "characters": [
    {
      "name": "Linh_Thuy_Dien_1709",
      "description": "Binh sĩ Thụy Điển niên đại năm 1709 (Trận Poltava). Đội mũ gập ba góc Tricorn bằng nỉ đen, mặc áo khoác dạ màu xanh da trời nẹp da màu vàng đặc trưng Thụy Điển, thắt lưng da nâu đeo bao đạn khuy đồng xỉn màu, đi ủng da cao cổ màu đen. Tay cầm súng kíp hỏa mai Flintlock 1700s nòng sắt bọc gỗ sồi."
    }
  ],
  "objects": [
    {
      "name": "Sung_Flintlock_1709",
      "description": "Súng kíp hỏa mai Flintlock khóa đá lửa niên đại năm 1709. Khóa nòng bằng sắt rèn thô có vết xước kim loại nhẹ, báng súng bằng gỗ sồi sẫm màu có vân nổi, quai đeo bằng da bò nâu đã sần cũ."
    }
  ]
}
```
