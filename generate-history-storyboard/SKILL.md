---
name: generate-history-storyboard
description: Chuyển đổi kịch bản lịch sử thành chuỗi Storyboard chi tiết tối ưu cho AI Image/Video Generation (Midjourney, Runway), tự động phân rã khung hình, chèn mã định danh tài sản (Asset Placeholders) và xuất JSON thuần.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY STORYBOARD GENERATOR

Hệ thống này chịu trách nhiệm biến một kịch bản phim tài liệu lịch sử thô thành một chuỗi phân cảnh (Storyboard) chi tiết, được tối ưu hóa tối đa cho các công cụ AI Tạo ảnh (Midjourney, Stable Diffusion) và AI Tạo video (Runway Gen-3, Luma Dream Machine).

## Quy tắc phân rã kịch bản (Shot Segmentation)

### 1. Độ dài và Mật độ phân cảnh (Shot Length & Density)
* **Thời lượng mục tiêu:** Mỗi shot tương ứng với 5–7 giây video.
* **Giới hạn từ ngữ:** Mỗi phân cảnh chỉ nên chứa **8–14 từ** từ kịch bản gốc. Hạn mức tối đa tuyệt đối là **20 từ**. Bất kỳ câu nào dài hơn 20 từ **BẮT BUỘC** phải được cắt đôi/chia nhỏ thành nhiều shot.
* **Mật độ ước tính:** Cứ mỗi 100 từ kịch bản sẽ tạo ra khoảng 7–10 shot hình.

### 2. Nguyên tắc giữ nguyên văn bản gốc (Script Fidelity Rule)
* Trường dữ liệu `line` trong JSON phải chứa văn bản được **sao chép chính xác 100%** từng ký tự, từng dấu câu từ kịch bản gốc.
* **CẤM TUYỆT ĐỐI:** Không viết lại, không tóm tắt, không sửa ngữ pháp, không đổi thứ tự câu. Khi ghép toàn bộ các trường `line` lại với nhau, kết quả phải tái dựng lại chính xác toàn vẹn kịch bản gốc.

---

## Quy tắc sử dụng Tài sản (Asset Placeholder Rule)

* Khi một nhân vật hoặc vật thể nằm trong danh sách `CHARACTERS` hoặc `OBJECTS` được nhắc đến hoặc xuất hiện trực quan, bạn **BẮT BUỘC** phải bao bọc tên của chúng trong dấu ngoặc vuông `[...]`.
* **Khớp từ ngữ chính xác (Exact Match):** Tên trong ngoặc vuông phải trùng khớp hoàn toàn với thư viện (Ví dụ: Dùng `[Charles XII]`, không dùng `[King Charles XII]`). Không thêm tính từ, không dịch nghĩa, không tự bịa ra placeholder mới.
* Các placeholder này phải được lan truyền đồng bộ vào cả `imagePrompt` và `videoPrompt`.

---

## Cấu trúc Prompt chuyên dụng cho AI

### 1. Quy tắc viết `imagePrompt` (Ảnh tĩnh - Khung hình đóng băng)
* **Độ dài:** 120–220 từ. Chỉ mô tả những gì nhìn thấy được bằng mắt (Chủ thể, Trang phục, Bối cảnh, Bố cục Tiền cảnh/Trung cảnh/Hậu cảnh).
* **Góc máy & Ánh sáng:** Xác định rõ loại khung hình (e.g., *wide shot*, *close-up*) và ánh sáng (e.g., *dramatic side-lighting*, *dawn atmosphere*).
* **CẤM TỪ KHÓA ĐỒ HỌA (Style Ban):** Tuyệt đối không dùng các từ như: *photorealistic, 8k, cinematic, hyperrealistic, CGI...* Hãy mô tả chi tiết vật lý thay vì dùng tính từ sáo rỗng.

### 2. Quy tắc viết `videoPrompt` (Chuyển động - Động lực học)
* **Độ dài:** 60–120 từ. Chỉ mô tả chuyển động của nhân vật (e.g., *advancing, fighting*), vật thể (e.g., *banners fluttering, artillery recoiling*) và chuyển động máy ảnh (Chỉ dùng các lệnh tiêu chuẩn như *slow push-in, dolly forward, tracking shot, pan left*). Không lặp lại phần mô tả của `imagePrompt`.

---

## Định dạng đầu ra bắt buộc (Output Format)

Hệ thống **CHỈ ĐƯỢC PHÉP** trả về một đối tượng JSON duy nhất, không bao gồm ký tự bọc mã hay bất kỳ dòng văn bản nào nằm ngoài JSON.

### Cấu trúc Schema JSON:
```json
{
  "totalShots": 0,
  "shots": [
    {
      "shot": 1,
      "line": "Đoạn text trích chính xác tuyệt đối từ kịch bản gốc",
      "imagePrompt": "Prompt mô tả hình ảnh tĩnh chi tiết chứa các [Asset]",
      "videoPrompt": "Prompt mô tả chuyển động và góc máy chứa các [Asset]"
    }
  ]
}