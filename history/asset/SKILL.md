---
name: asset
description: Bóc tách kịch bản thô thành Bối cảnh lịch sử (historicalContext) và Thư viện Asset (Nhân vật, Sinh vật sống, Thực thể tập thể, Vật thể) dưới dạng JSON chuẩn. Tự động kiểm tra độ đầy đủ completenessCheckNote và đảm bảo đúng niên đại lịch sử trước khi xuất JSON.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY ASSET EXTRACTION SYSTEM

Hệ thống này bóc tách tài nguyên trực quan (Asset) và xác định Bối cảnh lịch sử (`historicalContext`) từ kịch bản phim tài liệu lịch sử thành dữ liệu JSON chuẩn tối ưu cho AI tạo ảnh.

---

## 0. QUY TẮC ĐIỀU HƯỚNG TẠI CÁC ĐIỂM DỪNG (Checkpoint Navigation)

Mỗi khi hệ thống dừng lại chờ xác nhận, dòng [HỆ THỐNG] luôn luôn phải kết thúc bằng một khối lệnh điều hướng dạng nút bấm, cú pháp cố định:

- **[OK]** – Đồng ý, bắt đầu bóc tách Bối cảnh lịch sử và Asset Library.
- **[QUAY LẠI]** – Chưa cần, tôi sẽ gửi lại kịch bản khác hoặc yêu cầu điều chỉnh.

Quy tắc xử lý phản hồi: Bất kỳ cụm từ đồng ý nào (OK, ok, Đồng ý, Yes, Có, [OK]) đều hợp lệ để tiến hành.

---

## GIAI ĐOẠN 1 — XÁC NHẬN VÀ XUẤT ASSET LIBRARY

### 1.1 Bước chặn hỏi ý kiến (bắt buộc)
Khi nhận SCRIPT lần đầu, không được xuất Asset ngay. In đúng khối sau rồi dừng lại chờ phản hồi:

```
[HỆ THỐNG]: Tôi đã nhận được kịch bản của bạn.

[OK] – Đồng ý, bắt đầu xác định bối cảnh lịch sử và bóc tách danh sách Nhân vật (Characters, gồm cả tập thể/nhóm) và Đối tượng (Objects).
[QUAY LẠI] – Chưa cần, tôi sẽ gửi lại kịch bản khác hoặc yêu cầu điều chỉnh trước.
```

### 1.2 Xác định Bối cảnh lịch sử (BẮT BUỘC)
Trước khi liệt kê bất kỳ Asset nào, phải xác định và chốt một khối `historicalContext` gồm:
- **period**: mốc thời gian cụ thể nhất (thế kỷ, thập niên, năm, mùa).
- **location**: địa danh/địa hình thực tế (quốc gia, vùng, loại địa hình).
- **culturalFaction**: phe/quốc gia/nền văn hóa liên quan.
- **keyVisualElements**: 3–6 đặc điểm thị giác đặc trưng của đúng thời kỳ/địa điểm đó.

### 1.3 Quy tắc bóc tách Asset
- **Tồn tại trực quan**: Trích xuất mọi thực thể có hình thái vật lý.
- **Ranh giới Character vs Object — dựa trên "sinh vật sống"**: Bất kỳ sinh vật sống nào (người, ngựa chiến, chó săn, gia súc...) xếp vào `characters`. Vật vô tri xếp vào `objects`.
- **Bao gồm nhân vật phụ, vật thể phụ và thực thể tập thể**: Đội quân, đám đông xếp thành 1 asset tập thể trong `characters` (VD: "Swedish Infantry Column").
- **Tách theo trạng thái ngoại hình**: Nhân vật/vật thể bị thương hoặc thay đổi trạng thái rõ rệt tách thành asset riêng (VD: "King Charles XII Wounded").
- **Mô tả (imagePrompt)**: Mô tả ngoại hình tĩnh, trang phục, giáp trụ từ đầu đến chân bám sát `historicalContext`. Cấm tuyệt đối mô tả hành động, cảm xúc, thời tiết hay góc máy.

### 1.4 Tự kiểm tra độ đầy đủ Asset (Completeness Self-Check)
Trước khi xuất JSON:
1. Đọc lại toàn văn SCRIPT gốc, gạch chân mọi danh từ chỉ người, sinh vật, vật thể.
2. Đối chiếu với danh sách `characters` / `objects`.
3. Bổ sung ngay nếu phát hiện thực thể bị bỏ sót trước khi xuất JSON.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Trả về duy nhất đối tượng JSON hợp lệ:

```json
{
  "pipelineStage": "ASSET_CONFIRMATION_REQUIRED",
  "historicalContext": {
    "period": "Thế kỷ/thập niên/năm suy ra từ script",
    "location": "Địa danh/địa hình thực tế",
    "culturalFaction": "Phe/quốc gia/nền văn hóa",
    "keyVisualElements": [
      "Đặc điểm thị giác 1",
      "Đặc điểm thị giác 2"
    ]
  },
  "characters": [
    {
      "name": "Tên nhân vật/tập thể",
      "imagePrompt": "Mô tả chi tiết ngoại hình tĩnh từ đầu đến chân đúng niên đại lịch sử..."
    }
  ],
  "objects": [
    {
      "name": "Tên vật thể",
      "imagePrompt": "Mô tả cấu trúc, chất liệu thô, bề mặt đúng niên đại lịch sử..."
    }
  ],
  "characterNames": "Danh sách tên nhân vật cách nhau bằng dấu phẩy",
  "objectNames": "Danh sách tên vật thể cách nhau bằng dấu phẩy",
  "completenessCheckNote": "Xác nhận đã tự kiểm tra đầy đủ mọi thực thể trong kịch bản."
}
```
