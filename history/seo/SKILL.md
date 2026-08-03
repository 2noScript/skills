---
name: seo
description: Sinh SEO Metadata chuẩn YouTube (5-8 tiêu đề bắt đầu bằng từ để hỏi Why/How/What/Who..., Mô tả video, Từ khóa SEO, 8-15 Hashtags) dựa trên nội dung kịch bản hoàn chỉnh. Trả về định dạng JSON thuần.
disable-model-invocation: true
---

# YOUTUBE SEO METADATA GENERATION SYSTEM

Hệ thống này tổng hợp và tạo bộ SEO Metadata tối ưu tuyệt đối cho video YouTube dựa trên toàn bộ nội dung kịch bản phim tài liệu/câu chuyện đã hoàn chỉnh.

---

## QUY TẮC SINH METADATA

### 1. Phương án Tiêu đề tò mò (Multi-Title Question Hooks)
BẮT BUỘC tạo ra **từ 5 đến 8 phương án title** khác nhau để người dùng lựa chọn:
* Mỗi title **BẮT BUỘC phải bắt đầu bằng một từ để hỏi** (Question Word) như: `Why`, `How`, `What`, `Who`, `When`, `Where`, hoặc các cụm mở đầu câu hỏi tương đương (`Why Did...`, `How Did...`, `What Happened When...`, `Who Really...`).
* Mỗi title khai thác một góc độ/nhân vật/mâu thuẫn khác nhau trong kịch bản.
* Bám sát dữ kiện có thật trong kịch bản, không bịa đặt sai sự thật (không clickbait sai lệch).
* Độ dài mỗi title tối ưu cho YouTube (50-70 ký tự).

### 2. Mô tả Video (Description)
* Viết bằng tiếng Anh chuẩn SEO YouTube, tóm tắt lôi cuốn ngữ cảnh và cao trào của kịch bản.

### 3. Từ khóa SEO (Keywords)
* Danh sách từ khóa SEO tiếng Anh, phân cách bằng dấu phẩy.

### 4. Hashtags (Hashtags)
* Tạo từ **8 đến 15 hashtag** tiếng Anh (bắt đầu bằng `#`, viết liền không dấu cách, ví dụ: `#History`, `#Documentary`, `#WorldWarII`).

---

## ĐỊNH DẠNG JSON ĐẦU RA BẮT BUỘC

Trả về duy nhất 1 đối tượng JSON hợp lệ (không kèm ký tự bọc mã markdown, không lời mở đầu/kết thúc):

```json
{
  "pipelineStage": "METADATA_GENERATION",
  "titleOptions": [
    "Why Did ...?",
    "How Was ...?",
    "What Really Happened To ...?",
    "Who Was Behind ...?",
    "When Did ...?"
  ],
  "description": "Mô tả video YouTube chuẩn SEO tiếng Anh...",
  "keyword": "từ khóa 1, từ khóa 2, từ khóa 3...",
  "hashtags": [
    "#History",
    "#Documentary",
    "#WorldWarII"
  ]
}
```
