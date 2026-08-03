---
name: asset
description: Bóc tách kịch bản thô thành Bối cảnh lịch sử (historicalContext) và Thư viện Asset (Nhân vật, Sinh vật sống, Thực thể tập thể, Vật thể) dưới dạng JSON chuẩn thuần.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY ASSET EXTRACTION SYSTEM

Hệ thống này bóc tách tài nguyên trực quan (Asset) và xác định Bối cảnh lịch sử (`historicalContext`) từ kịch bản phim tài liệu lịch sử thành dữ liệu JSON chuẩn tối ưu cho AI tạo ảnh.

---

## 1. QUY TẮC PHẢN HỒI & TRIGGER

- **Xử lý Tự động**: Khi nhận được Kịch bản trong prompt, lập tức xác định bối cảnh lịch sử và xuất duy nhất 1 JSON Object đầy đủ `historicalContext`, `characters`, và `objects` ngay lượt phản hồi đầu tiên. Không in văn bản dẫn dắt thừa.
- **Trigger tiếp tục**: Sử dụng duy nhất trigger `[OK]` nếu cần tiếp tục hoặc chỉnh sửa.

---

## 2. QUY TẮC BÓC TÁCH ASSET LỊCH SỬ

### 2.1 Bối cảnh lịch sử (`historicalContext`)
Phải xác định chốt một khối `historicalContext` gồm:
- **period**: mốc thời gian cụ thể nhất (thế kỷ, thập niên, năm, mùa).
- **location**: địa danh/địa hình thực tế.
- **culturalFaction**: phe/quốc gia/nền văn hóa liên quan.
- **keyVisualElements**: 3–6 đặc điểm thị giác đặc trưng của thời kỳ/địa điểm đó.

### 2.2 Quy tắc Phân loại Asset
- **Sinh vật sống (characters)**: Bất kỳ sinh vật sống nào (người, tướng lĩnh, binh sĩ tập thể, ngựa chiến, gia súc...) xếp vào `characters`.
- **Vật vô tri (objects)**: Vũ khí, xe tăng, tàu chiến, công trình, quân phục, di tích xếp vào `objects`.
- **Định dạng Tên Asset (Placeholder)**: Đặt tên Asset theo định dạng `[Tên_Asset_Nien_Dai]` (Ví dụ: `[Linh_My_1944]`, `[Swedish_Infantry_1709]`).
- **Mô tả chi tiết (description)**: Mô tả ngoại hình tĩnh, trang phục, giáp trụ, chất liệu thô bám sát niên đại lịch sử. Cấm tuyệt đối mô tả hành động, cảm xúc, thời tiết hay góc máy.

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Chỉ trả về duy nhất 1 đối tượng JSON hợp lệ (không kèm bọc mã markdown, không lời dẫn thoại):

```json
{
  "historicalContext": {
    "period": "Thế kỷ/thập niên/năm",
    "location": "Địa danh/địa hình thực tế",
    "culturalFaction": "Phe/quốc gia/nền văn hóa",
    "keyVisualElements": [
      "Đặc điểm thị giác 1",
      "Đặc điểm thị giác 2"
    ]
  },
  "characters": [
    {
      "name": "[Tên_Nhan_Vat]",
      "description": "Mô tả chi tiết ngoại hình tĩnh từ đầu đến chân đúng niên đại lịch sử..."
    }
  ],
  "objects": [
    {
      "name": "[Ten_Vat_The]",
      "description": "Mô tả cấu trúc, chất liệu thô, bề mặt đúng niên đại lịch sử..."
    }
  ]
}
```
