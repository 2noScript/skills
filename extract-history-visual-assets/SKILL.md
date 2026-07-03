---
name: extract-history-visual-assets
description: Phân tích toàn bộ kịch bản lịch sử để trích xuất một thư viện tài sản hình ảnh tái sử dụng (Characters và Objects), cung cấp `imagePrompt` chi tiết phục vụ cho việc tạo ảnh AI (Midjourney/Stable Diffusion) và xuất dữ liệu dạng JSON thuần.
disable-model-invocation: true
---

# HISTORICAL VISUAL ASSET EXTRACTION SYSTEM

Hệ thống này đóng vai trò như một chuyên gia bóc tách tài nguyên hình ảnh (Visual Assets) từ kịch bản phim tài liệu lịch sử. Mục tiêu tối thượng là đảm bảo **độ phủ tối đa (Maximum Coverage)**, không bỏ sót bất kỳ thực thể nào có thể định dạng bằng mắt (nhân vật, trang phục, vũ khí, bối cảnh kiến trúc, biểu tượng).

## Nguyên tắc bóc tách cốt lõi

### 1. Tiêu chí tồn tại trực quan (Visual Existence)
* Chỉ cần một thực thể xuất hiện hoặc được nhắc đến có hình thái vật lý, nó phải được trích xuất. Không quan tâm đến thời lượng lên hình hay tầm quan trọng của nó trong cốt truyện.
* **Quy tắc khác biệt ngoại hình:** Cùng một nhân vật nhưng ở hai trạng thái ngoại hình khác nhau phải tách thành hai asset riêng biệt (ví dụ: `Charles XII` và `Charles XII Wounded`, hoặc `Swedish Soldier` và `Swedish Officer`).

### 2. Quy tắc đặt tên (Asset Name Rules)
* Tên của asset phải cực kỳ ngắn gọn, dùng số lượng từ tối thiểu để định danh.
* **KHÔNG** đưa mô tả màu sắc, chất liệu, hay giải thích lịch sử vào phần tên (ví dụ: Đặt là `Flintlock Musket`, không đặt là `Old Brown Wooden Flintlock Musket`). Tất cả chi tiết hình ảnh sẽ nằm ở phần `imagePrompt`.

### 3. Quy tắc viết ImagePrompt (Dành cho AI Image Generation)
* **Đối với Characters:** Mô tả chi tiết diện mạo từ đầu đến chân (độ tuổi, giới tính, tỷ lệ cơ thể, khuôn mặt, râu tóc, các lớp quần áo, giáp trụ, phù hiệu, vũ khí mang theo, độ sờn rách của vải/da/kim loại).
* **Đối với Objects:** Mô tả hình dáng, cấu trúc, chất liệu, màu sắc, hoa văn khắc/chạm, các khớp nối, vết xước, dấu vết thời gian hoặc hư hại do chiến trận.
* **CẤM TUYỆT ĐỐI:** Không đưa các mô tả về hành động, cảm xúc, bối cảnh thời tiết, góc máy, ánh sáng hay ý nghĩa lịch sử vào `imagePrompt`. Chỉ tập trung vào đặc điểm vật lý tĩnh.

---

## Định dạng đầu ra bắt buộc (Output Format)

Hệ thống **CHỈ ĐƯỢC PHÉP** trả về một đối tượng JSON hợp lệ duy nhất, không bao gồm các ký tự bọc mã (như \`\`\`json), không có văn bản thừa trước hoặc sau chuỗi JSON.

### Cấu trúc Schema JSON:
```json
{
  "characters": [
    {
      "name": "Tên nhân vật/lính/sĩ quan ngắn gọn",
      "imagePrompt": "Mô tả chi tiết ngoại hình tĩnh từ đầu đến chân"
    }
  ],
  "objects": [
    {
      "name": "Tên vũ khí/vật dụng/công trình ngắn gọn",
      "imagePrompt": "Mô tả chi tiết cấu trúc vật lý, chất liệu và bề mặt vật thể"
    }
  ],
  "characterNames": "Danh sách tất cả tên nhân vật, phân tách bằng dấu phẩy",
  "objectNames": "Danh sách tất cả tên vật thể, phân tách bằng dấu phẩy"
}