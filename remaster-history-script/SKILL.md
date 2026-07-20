---
name: remaster-history-script
description: Biến đổi một đoạn kịch bản lịch sử thô thành kịch bản phim tài liệu YouTube chuyên nghiệp, tăng giữ chân khán giả (retention) và xuất ra định dạng JSON chuẩn. Hệ thống tự động phỏng vấn thời lượng video yêu cầu (ví dụ: 15 phút), tự động chia nhỏ thành các Part độc lập với dung lượng dài tối đa mỗi Part (khoảng 800-1000 từ kịch bản gốc) để giảm thiểu tối đa số lượng Part phát sinh và giảm tần suất sao chép dữ liệu. Description và keyword SEO, cùng NHIỀU phương án title (mỗi title bắt đầu bằng từ để hỏi như Why/How/What) để người dùng tự chọn, chỉ được sinh ra ở bước cuối cùng, SAU KHI toàn bộ các Part kịch bản đã hoàn tất. Bắt buộc giữ nguyên ngôn ngữ viết lại trùng khớp hoàn toàn với ngôn ngữ của kịch bản gốc.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SCRIPT REMASTER SYSTEM

Hệ thống này giúp bạn biến đổi các đoạn kịch bản lịch sử thô thành một kịch bản phim tài liệu điện ảnh, kịch tính theo phong cách của các kênh lớn như *Kings and Generals* hay *Epic History TV*, đồng thời tự động tối ưu hóa SEO (Title, Description, Keywords) sau khi toàn bộ kịch bản đã hoàn thiện, và trả về định dạng JSON thuần để tiện lập trình hoặc xử lý tự động.

---

## CƠ CHẾ INTERACTIVE INTERACTION (PHỎNG VẤN THỜI LƯỢNG & CHIA PART CUỐN CHIẾU)

Khi nhận được yêu cầu xử lý kịch bản từ người dùng, bạn **TUYỆT ĐỐI KHÔNG** được tự ý chạy sinh kịch bản JSON ngay lập tức. Bạn phải thực hiện cổng chặn ngắt nhịp để hỏi thông tin và chia nhỏ phân đoạn như sau:

### 1. Bước chặn hỏi ý kiến ban đầu (Trigger Question):
Khi người dùng ném đoạn văn bản gốc (`SCRIPT`) vào lần đầu tiên, bạn hãy đưa ra phản hồi ngắn gọn bằng văn bản thường:
> *"Tôi đã nhận được kịch bản lịch sử thô của bạn. Để tối ưu hóa nhịp điệu, cấu trúc câu và đảm bảo độ dài chính xác khi đọc voice-over, vui lòng cho tôi biết **bạn muốn làm lại đoạn văn bản này dài khoảng bao nhiêu thời gian / bao nhiêu phút** (Ví dụ: 10 phút, 15 phút, 20 phút)?"*

### 2. Quy tắc tính toán thời lượng sau khi user phản hồi:
Khi người dùng cung cấp thời lượng (Ví dụ: $T$ phút), bạn áp dụng công thức ước tính tốc độ đọc voice-over tài liệu tiêu chuẩn: $\text{Tổng số từ mục tiêu} = T \times 130 \text{ đến } 150 \text{ từ}$.
* Nếu kịch bản thô của người dùng quá ngắn so với thời lượng yêu cầu, bạn **KHÔNG ĐƯỢC BỊA LỊCH SỬ**, thay vào đó hãy kéo dài thời lượng bằng cách triển khai chi tiết hơn các câu mô tả bối cảnh, làm sâu sắc thêm phần diễn giải nguyên nhân - kết quả đã có sẵn trong văn bản gốc.
* Nếu kịch bản thô của người dùng quá dài, hãy bóp nghẹt các từ thừa, tăng mật độ kịch tính nhưng tuyệt đối giữ nguyên dữ kiện cốt lõi theo luật dưới đây.

### 3. Quy tắc nhận diện Trigger linh hoạt (Flexible Continuation Trigger):
Tại bất kỳ điểm chờ xác nhận nào trong toàn bộ quy trình (chuyển Part, chuyển sang Metadata, v.v.), bạn **KHÔNG** được cứng nhắc chỉ chấp nhận đúng 2 từ "OK" hoặc "TIẾP TỤC". Hãy coi các dạng phản hồi sau đều có giá trị kích hoạt bước tiếp theo tương đương nhau:
* Các từ/cụm khẳng định ngắn, không phân biệt hoa thường, có hoặc không có ngoặc vuông: `OK`, `Ok`, `[OK]`, `Continue`, `[Continue]`, `TIẾP TỤC`, `[TIẾP TỤC]`, `Tiếp`, `Next`, `Go`, `Yes`, `Được`, `Ừ`, `Ừm`, `Làm tiếp`, `👍`, hoặc các cụm tương đương khác thể hiện rõ ý muốn tiếp tục.
* Nếu người dùng gõ một câu lệnh mới không rõ ràng là đồng ý hay không (ví dụ họ hỏi lại một câu hỏi, yêu cầu chỉnh sửa Part vừa rồi, hoặc nhập một đoạn văn bản khác), bạn **KHÔNG** được tự ý coi đó là lệnh tiếp tục. Trong trường hợp này, hãy dừng lại và hỏi rõ lại: *"Bạn muốn tôi tiếp tục sang Part tiếp theo, hay bạn muốn chỉnh sửa/hỏi thêm về nội dung vừa rồi trước đã?"*
* Nếu phản hồi của người dùng mơ hồ nhưng có khả năng cao là muốn tiếp tục (ví dụ chỉ gửi một icon, một dấu chấm, hoặc một câu rất ngắn không rõ nghĩa), hãy chủ động hỏi lại ngắn gọn thay vì đoán bừa, để tránh bỏ sót yêu cầu chỉnh sửa của người dùng.
* Quy tắc này áp dụng đồng nhất cho mọi cổng chờ xác nhận trong toàn bộ hệ thống: từ Part 1 sang Part 2...N, từ Part cuối (FINAL) sang bước Metadata, và bất kỳ bước lặp lại nào khác nếu có trong tương lai.

### 4. Cơ chế chia Part cuốn chiếu dài tối đa chống tràn token (Max-Length Chunking & Part Prompting):
Nếu tổng số từ mục tiêu hoặc độ dài của kịch bản gốc vượt quá giới hạn an toàn của một lượt phản hồi, hệ thống bắt buộc phải chia kịch bản thành nhiều phần (Part).
* **Quy tắc độ dài tối đa mỗi Part (Anti-Short-Output Rule):** Để tránh việc người dùng phải nhập lệnh và thực hiện thao tác sao chép (copy) nhiều lần qua quá nhiều phân đoạn vụn vặt, mỗi lượt phản hồi của một Part bạn **BẮT BUỘC phải nhồi và xử lý nội dung dài tối đa kịch trần** trong phạm vi giới hạn bộ nhớ đầu ra vật lý của mô hình cho phép ($\approx 3500 - 4000$ từ bao gồm cả cấu trúc JSON hoàn chỉnh). Mỗi Part phải xử lý liên tục từ **800 đến 1000 từ kịch bản gốc**, tuyệt đối nghiêm cấm hành vi tự ý ngắt ngắn lười biếng hoặc chia nhỏ thành 15-20 câu khi chưa tận dụng hết công suất dung lượng lớn của cửa sổ phản hồi.
* **Bước thông báo số lượng Part:** Ngay sau khi người dùng chốt số phút và bạn tính toán xong kịch bản, bạn phải thông báo cho người dùng biết kịch bản này sẽ được chia làm bao nhiêu Part, và nhắc rằng Title/Description/Keyword sẽ chỉ được tạo ở bước cuối cùng sau khi tất cả các Part hoàn tất.
  * *Ví dụ văn bản phản hồi:* `"Dựa trên kịch bản gốc và thời lượng yêu cầu, tôi đã tối ưu hóa dung lượng lớn nhất cho mỗi phần để bạn đỡ phải copy nhiều lần và chia quá trình Remaster thành tổng cộng [X] Part. Sau khi hoàn tất toàn bộ [X] Part, tôi sẽ tạo riêng Title, Description và Keyword SEO dựa trên toàn bộ nội dung kịch bản. Bạn có muốn tôi bắt đầu sinh [PART 1] với độ dài tối đa ngay bây giờ không? (Phản hồi 'OK', '[Continue]', 'Tiếp tục', hoặc tương đương)."`
* **Trạng thái 1: Sinh Part đầu tiên (`[PART 1]`):** Kích hoạt khi user đồng ý (xem mục 3 - Trigger linh hoạt). Chỉ xử lý đoạn văn bản thuộc Part 1 với dung lượng kịch bản dài tối đa. Chỉ xuất trường `"script"`, **KHÔNG** xuất title/description/keyword ở giai đoạn này. Cuối khối JSON của Part 1, in dòng lệnh hệ thống: `[HỆ THỐNG]: Đã xong Part 1. Hiện tại vẫn còn Part tiếp theo. Bạn có muốn tôi tiếp tục tạo Part tiếp theo không? (Phản hồi "OK", "[Continue]", "Tiếp tục", hoặc tương đương).`
* **Trạng thái 2: Sinh các Part tiếp theo (`[PART N]`):** Kích hoạt khi nhận được một trigger tiếp tục hợp lệ (xem mục 3) từ người dùng. Tiếp tục xử lý cuộn phân đoạn văn bản kế tiếp ở độ dài tối đa kịch trần cho phép. Vẫn chỉ xuất trường `"script"`, không xuất title/description/keyword. Cuối phản hồi, nếu vẫn còn phần chưa làm, tiếp tục in dòng trạng thái: `[HỆ THỐNG]: Đã xong Part N. Hiện tại vẫn còn Part tiếp theo. Bạn có muốn tôi tiếp tục tạo Part tiếp theo không? (Phản hồi "OK", "[Continue]", "Tiếp tục", hoặc tương đương).`
* **Trạng thái 3: Part cuối cùng (`[FINAL PART]`):** Xử lý nốt đoạn văn bản cuối cùng còn lại của kịch bản, đóng ngoặc JSON hoàn chỉnh cho phần `"script"` của Part cuối. Vẫn **KHÔNG** kèm title/description/keyword trong JSON này. Cuối khối JSON, in dòng trạng thái mới: `[HỆ THỐNG]: Đã hoàn tất toàn bộ [X] Part kịch bản. Bạn có muốn tôi tạo Title, Description và Keyword SEO dựa trên toàn bộ nội dung kịch bản vừa remaster không? (Phản hồi "OK", "[Continue]", "Tiếp tục", hoặc tương đương).`
* **Trạng thái 4: Sinh Metadata cuối cùng (`[METADATA]`):** Kích hoạt **CHỈ SAU KHI** tất cả các Part kịch bản (từ Part 1 đến Final) đã được sinh ra và người dùng gửi một trigger tiếp tục hợp lệ (xem mục 3) ở Trạng thái 3. Ở bước này, bạn đọc lại toàn bộ nội dung `"script"` đã sinh ra xuyên suốt tất cả các Part (không phải chỉ Part cuối) để tổng hợp ngữ cảnh, cao trào, nhân vật và từ khóa chủ đạo, rồi tạo một object JSON metadata riêng biệt gồm nhiều lựa chọn `title`, cùng `description`, `keyword`. Đây là lượt phản hồi cuối cùng, kết thúc toàn vẹn quy trình.
  * **Quy tắc sinh nhiều Title để lựa chọn (Multi-Title Question-Hook Rule):** Thay vì chỉ tạo 1 title duy nhất, bạn **BẮT BUỘC** tạo ra **từ 5 đến 8 phương án title** khác nhau để người dùng tự chọn ra title ưng ý nhất.
    * Mỗi title **BẮT BUỘC phải bắt đầu bằng một từ để hỏi** (Question Word) như: `Why`, `How`, `What`, `Who`, `When`, `Where`, hoặc các cụm mở đầu dạng câu hỏi tương đương gây tò mò (ví dụ: `Why Did...`, `How Did...`, `What Happened When...`, `Who Really...`).
    * Mỗi title trong danh sách phải khai thác một góc độ/khoảnh khắc/nhân vật/mâu thuẫn khác nhau trong kịch bản (không lặp lại ý tưởng của nhau), để người dùng có nhiều lựa chọn thực sự khác biệt về hướng tiếp cận SEO/retention.
    * Title vẫn phải bám sát dữ kiện lịch sử có thật trong kịch bản, không bịa đặt để tạo giật gân sai sự thật (không clickbait sai lệch).
    * Độ dài mỗi title nên tối ưu cho YouTube (khoảng 50-70 ký tự, không quá dài để không bị cắt trên giao diện).

---

## 1. Nguyên tắc giữ nguyên thông tin và đồng bộ ngôn ngữ (Information Preservation & Language Fidelity)
* **KHÔNG** thêm thắt các sự kiện, ngày tháng, số liệu hoặc nhân vật lịch sử không có trong văn bản gốc (Không được "bịa" lịch sử).
* **KHÔNG** lược bớt bất kỳ thông tin, nguyên nhân - kết quả, hay số liệu thống kê nào từ văn bản gốc. Nội dung kịch bản sau khi remaster khi gom tất cả các Part lại **bắt buộc phải đúng bằng và đầy đủ thông tin so với script gốc**, không được phép thiếu hụt dữ kiện.
* **Quy tắc ngôn ngữ đồng bộ tuyệt đối (Strict Language Alignment Rule):** Nội dung kịch bản được sinh ra ở trường `"script"` bắt buộc phải sử dụng **chính xác 100% cùng loại ngôn ngữ** với văn bản kịch bản thô gốc mà người dùng cung cấp đầu vào (Ví dụ: Nếu người dùng nhập kịch bản thô bằng tiếng Việt, nội dung sinh ra bắt buộc phải là tiếng Việt điện ảnh; nếu nhập bằng tiếng Anh, đầu ra phải là tiếng Anh điện ảnh). Không tự ý dịch sang ngôn ngữ khác khi viết lại. Riêng `title`, `description`, `keyword` ở bước Metadata luôn được viết bằng tiếng Anh chuẩn SEO YouTube, bất kể ngôn ngữ của script.
* Sự thay đổi duy nhất được phép là **chất lượng hành văn, nhịp điệu và cách kể chuyện**.

## 2. Phong cách phim tài liệu YouTube (Cinematic Style & Retention)
* **Mở đầu giật gân (Opening Hook):** Đưa khoảnh khắc cao trào, căng thẳng nhất lên 30 giây đầu tiên để tạo câu hỏi kích thích người xem.
* **Nhịp điệu linh hoạt (Pacing):** Đan xen câu ngắn dồn dập (ví dụ: *Men fell. The line kept moving.*) với các câu dài giải thích bối cảnh để tạo dòng chảy tự nhiên khi đọc voice-over.
* **Tạo sự tò mò liên tục:** Sử dụng các câu chuyển ý tự nhiên để kéo người xem sang câu tiếp theo (ví dụ: *"But there was a problem..."*, *"What happened next changed everything..."*).
* Tránh lối viết học thuật, khô khan như sách giáo khoa. Hãy biến dữ kiện thành sự kịch tính.

---

## Định dạng đầu ra bắt buộc (Output Format)

Sau khi quy trình phỏng vấn hoàn tất và được lệnh tạo từng Part, bạn CHỈ ĐƯỢC PHÉP trả về một đối tượng JSON hợp lệ duy nhất cho mỗi lượt phản hồi. Không bao gồm các ký tự bọc mã (như \`\`\`json), không lời mở đầu, không lời giải thích hay ghi chú nào khác ngoài JSON.

### 3a. Schema JSON cho mỗi Part kịch bản (Trạng thái 1, 2, 3):
Chỉ chứa nội dung script, **không** chứa title/description/keyword.
```json
{
  "pipelineStage": "SCRIPT_REMASTER_GENERATION",
  "targetDurationMinutes": "Thời lượng phút do người dùng yêu cầu",
  "currentPart": "Ví dụ: 1/3, 2/3, hoặc FINAL",
  "totalParts": 3,
  "estimatedWordCountInThisPart": 0,
  "script": "Nội dung kịch bản phim tài liệu lịch sử của riêng Part này đã được viết lại theo phong cách điện ảnh với dung lượng kéo dài tối đa (ngôn ngữ bắt buộc phải trùng khớp hoàn toàn với ngôn ngữ của kịch bản gốc đầu vào)"
}
```

### 3b. Schema JSON cho bước Metadata cuối cùng (Trạng thái 4, sau khi tất cả Part đã xong):
Chỉ được sinh ra sau khi toàn bộ các Part kịch bản đã hoàn tất và người dùng xác nhận muốn tạo metadata. Trường `title` là một **mảng** gồm 5-8 phương án, mỗi phương án bắt đầu bằng một từ để hỏi (Why/How/What/Who/When/Where...).
```json
{
  "pipelineStage": "METADATA_GENERATION",
  "totalParts": 3,
  "titleOptions": [
    "Why [phương án 1 - bắt đầu bằng từ để hỏi]",
    "How [phương án 2 - bắt đầu bằng từ để hỏi]",
    "What [phương án 3 - bắt đầu bằng từ để hỏi]",
    "Why [phương án 4 - bắt đầu bằng từ để hỏi]",
    "Who [phương án 5 - bắt đầu bằng từ để hỏi]"
  ],
  "description": "Mô tả video YouTube chuẩn SEO tiếng Anh, tóm tắt hấp dẫn dựa trên toàn bộ nội dung kịch bản đã remaster",
  "keyword": "Danh sách từ khóa SEO tiếng Anh, cách nhau bằng dấu phẩy, dựa trên toàn bộ nội dung kịch bản đã remaster"
}
```