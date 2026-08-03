---
name: seo
description: Sinh SEO Metadata chuẩn YouTube (5-8 tiêu đề bắt đầu bằng từ để hỏi Why/How/What/Who/Tại sao/Làm thế nào..., Mô tả video, Từ khóa SEO, 8-15 Hashtags) dựa trên nội dung kịch bản hoàn chỉnh. Trả về định dạng JSON thuần.
disable-model-invocation: true
---

# YOUTUBE SEO METADATA GENERATION SYSTEM

Hệ thống này tổng hợp và tạo bộ SEO Metadata tối ưu tuyệt đối cho video YouTube dựa trên toàn bộ nội dung kịch bản phim tài liệu/câu chuyện đã hoàn chỉnh.

---

## 1. QUY TẮC ĐỒNG BỘ NGÔN NGỮ BẮT BUỘC (Strict Language Alignment Rule)

- **CÙNG LOẠI NGÔN NGỮ VỚI KỊCH BẢN ĐẦU VÀO**: Bộ SEO Metadata (`titleOptions`, `description`, `keyword`) BẮT BUỘC trả về bằng **CHÍNH XÁC 100% CÙNG LOẠI NGÔN NGỮ** với kịch bản đầu vào.
  - Nếu kịch bản đầu vào là **TIẾNG VIỆT** $\rightarrow$ Các tiêu đề (`Tại sao...?`, `Làm thế nào...?`), Mô tả video và Từ khóa SEO BẮT BUỘC 100% là **TIẾNG VIỆT**.
  - Nếu kịch bản đầu vào là **TIẾNG ANH** $\rightarrow$ Tiêu đề (`Why...?`, `How...?`), Mô tả video và Từ khóa SEO BẮT BUỘC 100% là **TIẾNG ANH**.
  - **TỰ Ý DỊCH SANG NGÔN NGỮ KHÁC LÀ VI PHẠM NGHIÊM TRỌNG.**

---

## 2. QUY TẮC SINH METADATA

### 2.1 Phương án Tiêu đề tò mò (Multi-Title Question Hooks)
BẮT BUỘC tạo ra **từ 5 đến 8 phương án title** khác nhau để người dùng lựa chọn:
* Mỗi title **BẮT BUỘC phải bắt đầu bằng một từ để hỏi** (Question Word) tương ứng với ngôn ngữ của kịch bản (Tiếng Việt: `Tại sao`, `Làm thế nào`, `Cái gì`, `Ai`, `Khi nào`; Tiếng Anh: `Why`, `How`, `What`, `Who`, `When`).
* Mỗi title khai thác một góc độ/nhân vật/mâu thuẫn khác nhau trong kịch bản.
* Bám sát dữ kiện có thật trong kịch bản, không bịa đặt sai sự thật.
* Độ dài mỗi title tối ưu cho YouTube (50-70 ký tự).

### 2.2 Mô tả Video (Description)
* Viết tóm tắt lôi cuốn ngữ cảnh và cao trào của kịch bản bằng đúng ngôn ngữ đầu vào.

### 2.3 Từ khóa SEO (Keywords)
* Danh sách từ khóa SEO bằng đúng ngôn ngữ đầu vào, phân cách bằng dấu phẩy.

### 2.4 Hashtags (Hashtags)
* Tạo từ **8 đến 15 hashtag** (bắt đầu bằng `#`, viết liền không dấu cách).

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Trả về duy nhất 1 đối tượng JSON hợp lệ (không kèm ký tự bọc mã markdown, không lời mở đầu/kết thúc):

```json
{
  "titleOptions": [
    "Tại sao / Why ...?",
    "Làm thế nào / How ...?",
    "Cái gì / What ...?",
    "Ai / Who ...?",
    "Khi nào / When ...?"
  ],
  "description": "Mô tả video YouTube chuẩn SEO bằng đúng ngôn ngữ đầu vào...",
  "keyword": "từ khóa 1, từ khóa 2, từ khóa 3...",
  "hashtags": [
    "#History",
    "#Documentary",
    "#WorldWarII"
  ]
}
```
