---
name: history-production-pipeline
description: Quy trình sản xuất phim tài liệu lịch sử, gồm 2 giai đoạn độc lập và tuần tự. Giai đoạn 1 bóc tách kịch bản thành thư viện Asset (Nhân vật/Vật thể) dưới dạng JSON, chỉ xuất sau khi được người dùng xác nhận. Giai đoạn 2 phân rã kịch bản thành Storyboard JSON theo từng Part (chống tràn token), mỗi Part chứa 30–45 shot, tự động tạm dừng chờ lệnh người dùng trước khi sang Part tiếp theo. Mọi điểm dừng đều hiển thị khối lệnh điều hướng chuẩn [OK]/[QUAY LẠI] để người dùng chọn tiếp tục hoặc yêu cầu làm lại bước vừa xong. Bắt buộc khôi phục tối thiểu 90% văn bản gốc (mục tiêu 100%) bằng đối chiếu tuyến tính nghiêm ngặt kèm tự kiểm tra tỷ lệ khớp, tự động escape ký tự đặc biệt để không vỡ cấu trúc JSON, và cấm tuyệt đối bỏ sót hoặc viết lại shot line từ Part 2 trở đi. Hệ thống tự tính và báo trước số Part dự kiến ngay khi Asset được chốt. Có kèm mẫu JSON đầy đủ (Full Mock) cho từng giai đoạn.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY PRODUCTION PIPELINE

Hệ thống này bóc tách tài nguyên trực quan (Asset) và phân rã kịch bản phim tài liệu lịch sử thành dữ liệu JSON tối ưu cho AI tạo ảnh. Toàn bộ đầu ra **luôn luôn** ở định dạng JSON — không có ngoại lệ.

Quy trình gồm 2 giai đoạn, chạy **tuần tự và không được bỏ qua bước nào**:

| Giai đoạn | Kích hoạt bởi | Kết quả |
|---|---|---|
| 1. Asset Library | Người dùng gửi `SCRIPT` lần đầu, rồi xác nhận "OK" | JSON danh sách Nhân vật + Vật thể |
| 2. Storyboard theo Part | Từ khóa `[CHỐT ASSET]` | JSON các Part shot, cuối cùng có JSON Thumbnail (tuỳ chọn) |

---

## 0. QUY TẮC ĐIỀU HƯỚNG TẠI CÁC ĐIỂM DỪNG (Checkpoint Navigation)

Mỗi khi hệ thống dừng lại chờ xác nhận (cuối Giai đoạn 1, cuối mỗi Part, cuối Part cuối trước Thumbnail), dòng `[HỆ THỐNG]` **luôn luôn** phải kết thúc bằng một khối lệnh điều hướng dạng nút bấm, cú pháp cố định như sau — không tự ý đổi wording:

```
[OK] – Xác nhận, tiếp tục sang bước/Part tiếp theo.
[QUAY LẠI] – Yêu cầu tạo lại Part/bước vừa xong (không tính là Part mới, giữ nguyên số shot bắt đầu).
```

**Quy tắc xử lý phản hồi của người dùng:**
- Nhận `OK`, `[OK]`, `TIẾP TỤC`, hoặc từ khóa chuyên biệt của từng bước (`[CHỐT ASSET]`, `[PART N]`, `TẠO THUMBNAIL`...) → không phân biệt hoa thường, tiến sang bước/Part kế tiếp đúng luồng đã định.
- Nhận `QUAY LẠI`, `[QUAY LẠI]`, hoặc `LÀM LẠI` → loại bỏ hoàn toàn JSON vừa xuất ở lượt ngay trước đó, tạo lại từ đầu cho đúng Part/bước đó (giữ nguyên `startingShot`/`endingShot` dự kiến, tuyệt đối không cộng dồn thêm số shot mới). Nếu người dùng có nêu kèm lý do/góp ý sửa lỗi, phải áp dụng đúng góp ý đó khi làm lại.
- Nếu người dùng muốn quay lại xa hơn một bước (VD: quay lại sửa cả danh sách Asset ở Giai đoạn 1 khi đang ở giữa Giai đoạn 2), họ phải nêu rõ, ví dụ `[QUAY LẠI GIAI ĐOẠN 1]`. Hệ thống không tự suy đoán mức độ "quay lại" nếu chỉ nhận lệnh `QUAY LẠI` đơn thuần — mặc định luôn hiểu là làm lại **bước/Part gần nhất**.
- Không được tự động tiến bước tiếp theo nếu chưa nhận được `[OK]` (hoặc từ khóa tương đương) rõ ràng từ người dùng.

---

## GIAI ĐOẠN 1 — XÁC NHẬN VÀ XUẤT ASSET LIBRARY

### 1.1 Bước chặn hỏi ý kiến (bắt buộc)
Khi nhận `SCRIPT` lần đầu, **không được** xuất Asset ngay. In đúng khối sau rồi dừng lại chờ phản hồi:

```
[HỆ THỐNG]: Tôi đã nhận được kịch bản của bạn.

[OK] – Đồng ý, bắt đầu bóc tách danh sách Nhân vật (Characters) và Đối tượng (Objects).
[QUAY LẠI] – Chưa cần, tôi sẽ gửi lại kịch bản khác hoặc yêu cầu điều chỉnh trước.
```

**Quy tắc nhận diện xác nhận (áp dụng cho MỌI điểm dừng trong toàn bộ pipeline, kể cả bước này):** bất kỳ phản hồi nào mang nghĩa đồng ý — `OK`, `ok`, `Đồng ý`, `Yes`, `Có`, `[OK]` (không phân biệt hoa/thường, có hay không có ngoặc vuông) — đều hợp lệ để tiến sang bước tiếp theo. Tuyệt đối không được hỏi lại hoặc lặp lại câu hỏi nếu đã nhận được một cụm từ đồng ý rõ ràng như trên. Chỉ khi phản hồi của người dùng thực sự mơ hồ (không phải đồng ý, không phải quay lại, không khớp bất kỳ từ khóa nào) mới được phép hỏi lại để làm rõ ý.

Sau khi nhận `[OK]` (hoặc tương đương), chuyển ngay sang bước 1.2 — không xuất thêm câu hỏi trung gian nào khác.

### 1.2 Quy tắc bóc tách Asset
- **Tồn tại trực quan:** bất kỳ thực thể nào xuất hiện hoặc được nhắc đến có hình thái vật lý đều phải được trích xuất.
- **Tách theo trạng thái ngoại hình:** cùng một nhân vật nhưng khác trạng thái ngoại hình (bị thương, thay trang phục...) → tách thành asset riêng (VD: `Charles XII` và `Charles XII Wounded`).
- **Đặt tên (`name`):** cực ngắn gọn, chỉ đủ định danh. Không đưa màu sắc, chất liệu, hay giải thích lịch sử vào tên.
- **Mô tả (`imagePrompt`):**
  - Characters: mô tả ngoại hình vật lý từ đầu đến chân.
  - Objects: mô tả cấu trúc, chất liệu, bề mặt.
  - Cấm tuyệt đối: hành động, cảm xúc, thời tiết, góc máy, bối cảnh câu chuyện.

### 1.3 Xuất JSON và tính toán số Part
Sau khi bóc tách xong, xuất JSON đúng cấu trúc ở [mục 3](#3-định-nghĩa-trường-dữ-liệu) và mẫu ở [mục 4](#4-mẫu-json-đầy-đủ-mock). Ngay sau đó:

1. Đếm tổng số từ của `SCRIPT` gốc.
2. Chia cho định biên ~400–500 từ/Part để tính số Part **ước tính** (`Y`) — đây chỉ là con số tham khảo ban đầu, KHÔNG phải cam kết cứng, vì số Part thực tế còn phụ thuộc mật độ câu và các điểm phải chẻ shot ở dấu phẩy/từ nối (xem quy tắc 2.4).
3. In đúng dòng hệ thống sau rồi **dừng lại, chờ phản hồi**:

```
[HỆ THỐNG]: Đã bóc tách xong danh sách Asset. Dựa trên độ dài của kịch bản gốc của bạn (khoảng [X] từ), tôi tính toán sẽ phân chia Giai đoạn 2 thành tổng cộng [Y] Part (Mỗi Part dài tối đa từ 30-45 shots để bạn đỡ phải copy nhiều lần).

[CHỐT ASSET] – Xác nhận danh sách Asset, kèm phần kịch bản để bắt đầu sinh Part 1 ngay lập tức.
[QUAY LẠI] – Yêu cầu bóc tách lại danh sách Asset (nêu rõ cần sửa gì).
```

---

## GIAI ĐOẠN 2 — PHÂN RÃ STORYBOARD THEO PART

Chỉ kích hoạt khi nhận từ khóa `[CHỐT ASSET]`.

### 2.1 Đối chiếu tuyến tính & ngưỡng khớp văn bản

- **Quét tuần tự trái→phải:** xử lý kịch bản gốc theo thứ tự, đọc đến đâu bẻ shot đến đó. Không nhảy cóc câu, không bỏ sót từ nối hay dấu câu.

- **Bắt buộc đọc lại SCRIPT gốc trước mỗi Part (kể cả Part 1):** trước khi bẻ bất kỳ shot nào, phải quét lại lịch sử chat để lấy đúng **toàn văn `SCRIPT` gốc** mà người dùng đã gửi — không được dựa vào trí nhớ/suy diễn nội dung. Sau đó mới xác định chính xác đoạn văn bản tương ứng với Part này (dựa vào điểm neo của Part trước, xem quy tắc Anchor bên dưới), rồi mới tiến hành cắt. Đây là bước bắt buộc, không được bỏ qua dù là Part đầu tiên hay các Part sau.

- **Quy tắc "Sao chép trước, cắt sau" (Copy-First-Then-Split):** Trước khi tạo shot, hãy coi đoạn kịch bản gốc của Part này là MỘT khối văn bản duy nhất, giữ nguyên y hệt từng từ. Việc duy nhất bạn được làm là **chèn dấu ngắt** vào giữa các từ có sẵn để tách thành từng shot — giống như cắt một sợi dây tại các điểm định trước, không phải viết lại câu. Cấm tuyệt đối tóm tắt, dùng từ đồng nghĩa, hoặc diễn giải lại theo cách hiểu của AI. Nếu bạn thấy mình đang "hiểu ý rồi diễn đạt lại" thay vì "cắt nguyên văn", đó là dấu hiệu sai — dừng lại và cắt lại đúng nguyên bản.

- **Ngưỡng khớp bắt buộc (Match Threshold ≥ 90%):** Khi nối toàn bộ trường `line` trong một Part lại bằng dấu cách, kết quả phải khớp **tối thiểu 90% số từ** so với đoạn kịch bản gốc tương ứng (mục tiêu lý tưởng vẫn là 100%, không phải cái cớ để dừng ở 90%). Trước khi đóng JSON của Part, bắt buộc tự thực hiện:
  1. Đếm số từ đoạn gốc → `rawScriptWordCount`.
  2. Đếm số từ khi gộp toàn bộ `line` → `processedScriptWordCount`.
  3. Nếu `processedScriptWordCount` < 90% của `rawScriptWordCount`: **không được xuất JSON**. Quay lại rà từng shot, tìm đúng chỗ đã bị tóm tắt/bỏ sót, sửa bằng cách cắt lại nguyên văn (không phải viết lại).
  4. Chỉ xuất JSON khi tỷ lệ khớp đã đạt ≥ 90%. Không tự nhận "đã khớp 100%" nếu không thực sự đối chiếu lại.

- **Điểm neo giữa các Part (Anchor Rule):** khi bắt đầu một Part mới, hãy nhớ lại 5–10 từ cuối cùng của Part trước làm điểm neo, để không bị bỏ sót hoặc lặp lại đoạn giao giữa hai Part.

- **Giới hạn từ mỗi shot:** 8–14 từ, tối đa tuyệt đối 20 từ. Câu gốc dài hơn 20 từ → bắt buộc chẻ tại dấu phẩy hoặc từ nối phù hợp thành 2 shot liên tiếp. Mật độ chuẩn: 100 từ kịch bản ≈ 7–10 shot. *(Shot càng ngắn, điểm cắt càng nhiều nhưng rủi ro diễn giải sai càng thấp — ưu tiên cắt đúng nguyên văn hơn là gộp dài cho gọn.)*

### 2.2 An toàn dữ liệu JSON & đối chiếu Asset
- **Escape ký tự:** mọi dấu ngoặc kép trong văn bản gốc của `line` phải escape thành `\"` hoặc chuyển thành `''`. Không để ngoặc kép trần làm vỡ JSON.
- **Cấm ký tự toán học nguy hiểm trong `imagePrompt`:** không dùng `>` hoặc `<`. Diễn đạt kích thước/khoảng cách bằng chữ (VD: `"less than 2 meters"`, không viết `"< 2m"`).
- **Đối chiếu Asset chéo:** khi bọc một thực thể trong `[Tên_Asset]`, phải đối chiếu với danh sách Asset đã duyệt ở Giai đoạn 1. Nếu thực thể không có trong Asset gốc, không tự ý tạo placeholder ngoặc vuông.

- **Chống trôi tên Asset qua nhiều Part (Name Drift Prevention):** càng về các Part sau, model càng dễ tự nhớ lại tên Asset theo trí nhớ dài hạn thay vì tra đúng bản gốc, dẫn đến sai lệch tên (thiếu/thừa từ, sai chính tả, viết tắt khác bản gốc). Để tránh lỗi này:
  1. Trước khi viết bất kỳ `[Tên_Asset]` nào trong Part, phải đọc lại chính xác khối JSON Asset Library đã xuất ở Giai đoạn 1 từ lịch sử chat (ưu tiên tra ở `characterNames`/`objectNames`, đối chiếu chéo với `characters[].name`/`objects[].name`).
  2. Copy nguyên văn chuỗi tên — đúng từng ký tự, đúng khoảng trắng, đúng hoa/thường — tuyệt đối không gõ lại theo trí nhớ, không tự rút gọn hay diễn giải khác đi.
  3. Trước khi đóng JSON của mỗi Part, tự rà lại toàn bộ placeholder `[Tên_Asset]` đã dùng trong Part đó, đối chiếu từng cái với danh sách gốc; nếu phát hiện tên nào không khớp 100% chuỗi ký tự với bản gốc, phải sửa lại đúng nguyên bản trước khi xuất.

### 2.3 Xử lý theo Part — chống tràn token, tối đa dung lượng mỗi lượt
- **Nhồi dung lượng tối đa (cấm output ngắn):** mỗi Part phải chứa liên tục 30–45 shot (~400–500 từ kịch bản gốc). Chỉ dừng một Part khi chuỗi JSON đã tiệm cận ngưỡng vật lý của cửa sổ đầu ra (~3500–4000 từ kể cả `imagePrompt`). Cấm cắt ngắn tùy tiện ở mức 15–20 shot.
- **Luôn dừng và hỏi sau mỗi Part** (không tự động sinh Part tiếp theo hay Thumbnail khi chưa có lệnh).

**Trạng thái xử lý:**

| Trạng thái | Kích hoạt | Việc cần làm | Dòng hệ thống cuối phản hồi |
|---|---|---|---|
| 1. Part đầu tiên | `[PART 1]` | Đọc lại toàn văn `SCRIPT` gốc từ lịch sử chat. Đánh số shot từ `1`, chạy tối đa đến shot 35–45 | `[HỆ THỐNG]: Đã xử lý xong Part 1. Hiện tại kịch bản vẫn còn nội dung.`<br>`[OK] – Tiếp tục tạo Part 2.`<br>`[QUAY LẠI] – Yêu cầu làm lại Part 1.` |
| 2. Part tiếp theo | `[OK]` hoặc `[PART N]` | Đọc lại toàn văn `SCRIPT` gốc từ lịch sử chat + xác định shot cuối của lượt trước (dựa vào điểm neo), đánh số tiếp nối, áp dụng nghiêm ngặt quy tắc 2.1 | Như trên, thay `Part 1`/`Part 2` bằng `Part N`/`Part N+1` |
| 3. Part cuối | Tự phát hiện khi hết kịch bản, hoặc `[FINAL PART]` | Đọc lại toàn văn `SCRIPT` gốc, tiếp nối số shot, đóng JSON hoàn chỉnh cho Part cuối. **Không tự xuất Thumbnail** | `[HỆ THỐNG]: Toàn bộ phân cảnh kịch bản đã được xử lý xong hoàn toàn!`<br>`[TẠO THUMBNAIL] – Thiết kế ý tưởng và Prompt Image cho Thumbnail.`<br>`[QUAY LẠI] – Yêu cầu làm lại Part cuối vừa xong.`<br>`[KHÔNG CẦN] – Kết thúc quy trình, không cần Thumbnail.` |
| 4. Thumbnail | `[TẠO THUMBNAIL]` | Xuất JSON Thumbnail | — |

### 2.4 Xử lý khi số Part thực tế vượt dự kiến (Part Count Overflow Handling)

Con số `Y` thông báo ở cuối Giai đoạn 1 chỉ là **ước tính**. Khi đang xử lý Part được cho là Part cuối theo dự kiến (VD: Part 3/3) mà phát hiện `SCRIPT` gốc **vẫn còn nội dung chưa xử lý**:

- **Tuyệt đối cấm nhồi ép** toàn bộ phần còn lại vào Part hiện tại cho khớp con số `Y` ban đầu — hành vi này sẽ phá vỡ giới hạn 30–45 shot/Part (mục 2.3) và/hoặc buộc phải tóm tắt để nhồi vừa, vi phạm quy tắc chống bỏ sót chữ (2.1).
- Phải xử lý Part đó bình thường theo đúng giới hạn shot, rồi **tiếp tục phát sinh thêm Part mới** như mọi Part khác (không phải Part cuối), đồng thời chuyển trạng thái hệ thống về "Trạng thái 2" chứ chưa được sang "Trạng thái 3".
- Dòng `[HỆ THỐNG]` ở Part đó bắt buộc phải nêu rõ việc điều chỉnh, ví dụ: `Lưu ý: số Part thực tế đang nhiều hơn dự kiến ban đầu ([Y] Part) để đảm bảo không bỏ sót nội dung kịch bản gốc. Tổng số Part mới dự kiến: khoảng [Y mới].` — không được âm thầm thêm Part mà không báo.
- Trường `currentPart` trong JSON dùng định dạng `"N/~Y"` (dấu `~` biểu thị số tổng vẫn là ước tính) cho mọi Part chưa xác nhận là cuối; chỉ đổi thành `"FINAL"` khi thực sự đã xử lý hết toàn bộ `SCRIPT` gốc — việc xác định "cuối" luôn dựa trên nội dung kịch bản thực tế, không dựa trên con số `Y` đã thông báo trước đó.

### 2.5 Quy tắc `imagePrompt` (chỉ áp dụng trong Giai đoạn 2)
- Độ dài: 120–220 từ. Mô tả diện mạo vật lý **tĩnh**: Subject, Appearance, Environment, bố cục rõ 3 lớp Foreground/Middleground/Background, khoảng cách & vị trí camera, ánh sáng tự nhiên.
- **Bối cảnh lịch sử bắt buộc lồng ghép:**
  - *Mốc thời gian:* thế kỷ, năm, mùa trong năm (ảnh hưởng trang phục, công nghệ, vũ khí thời kỳ đó).
  - *Địa lý thực tế:* địa hình, địa vật đặc trưng của địa danh (bình nguyên, đầm lầy, lũy tre, pháo đài...).
  - *Không khí sự kiện/chiến trận:* khói thuốc súng, doanh trại dã chiến, tuyết rơi cản tầm nhìn...

- **Cấm tuyệt đối từ khóa phong cách & kỹ thuật hiển thị** (vì người dùng sẽ tự chèn style sau):
  - *Chất lượng/đồ họa:* photorealistic, hyperrealistic, ultra realistic, 8k, 4k, cinematic, CGI, octane render, unreal engine, ray tracing, masterpiece, best quality, highly detailed, award winning, render, 3D render, digital art, illustration, painting, sketch.
  - *Khung hình/ánh sáng nghệ thuật:* dramatic lighting, studio lighting, volumetric lighting, depth of field, bokeh, anamorphic, macro, wide-angle lens, close-up, wide shot, medium shot, extreme close-up, overhead view, bird's-eye view, low-angle, high-angle, eye-level.
  - Thay vào đó, mô tả khoảng cách/vị trí vật lý của camera (VD: "Camera đặt cách chủ thể 2 mét, ngang tầm mắt").
- **Chỉ được mô tả vật lý thô:** hình khối, kết cấu, chất liệu bề mặt, màu sắc tự nhiên, vết xước trên thép, bùn bám trên giày, nếp nhăn trên da mặt, vải dệt thô sơ.

---

## 3. ĐỊNH NGHĨA TRƯỜNG DỮ LIỆU

### 3.1 Giai đoạn 1 — Asset Library
| Trường | Mô tả |
|---|---|
| `pipelineStage` | Cố định `"ASSET_CONFIRMATION_REQUIRED"` |
| `characters[].name` | Tên định danh nhân vật/nhóm lính, cực ngắn gọn |
| `characters[].imagePrompt` | Ngoại hình tĩnh, thân thể, trang phục/quân phục/giáp trụ từ đầu đến chân |
| `objects[].name` | Tên định danh vật thể, ngắn gọn |
| `objects[].imagePrompt` | Cấu trúc hình học, chất liệu thô, bề mặt vật thể |
| `characterNames` | Tất cả tên nhân vật, phân tách bằng dấu phẩy |
| `objectNames` | Tất cả tên vật thể, phân tách bằng dấu phẩy |

### 3.2 Giai đoạn 2 — Storyboard theo Part
| Trường | Mô tả |
|---|---|
| `pipelineStage` | Cố định `"STORYBOARD_GENERATION"` |
| `currentPart` | VD `"1/~3"`, `"2/~3"` (dấu `~` = tổng số Part còn là ước tính, có thể tăng), hoặc `"FINAL"` khi đã thực sự xử lý hết `SCRIPT` gốc |
| `startingShot` | Số shot bắt đầu của Part này |
| `endingShot` | Số shot kết thúc của Part này |
| `totalShots` | Tổng shot trong Part này (30–45, trừ Part cuối) |
| `rawScriptWordCount` | Số từ của đoạn kịch bản gốc giao cho Part này |
| `processedScriptWordCount` | Số từ khi gộp toàn bộ `line` của Part này — **bắt buộc đạt tối thiểu 90% của `rawScriptWordCount`** (mục tiêu lý tưởng: bằng nhau tuyệt đối) |
| `shots[].shot` | Số thứ tự tăng dần, kế thừa từ Part trước |
| `shots[].line` | Trích nguyên văn kịch bản gốc cho shot này (đã escape dấu ngoặc kép). Gộp toàn bộ theo thứ tự phải tái dựng 100% văn bản gốc |
| `shots[].imagePrompt` | Theo quy tắc 2.5, dùng `[Tên_Asset]` để đồng bộ hình ảnh |

### 3.3 Thumbnail (chỉ khi được đồng ý)
| Trường | Mô tả |
|---|---|
| `pipelineStage` | Cố định `"THUMBNAIL_GENERATION"` |
| `thumbnailConcept` | Ý tưởng biểu tượng, bắt mắt, cô đọng cao trào lịch sử của kịch bản |
| `thumbnailImagePrompt` | 150–250 từ. Lồng ghép Asset `[Tên_Asset]` chủ chốt, bối cảnh địa lý/thời gian rõ ràng, 3 lớp Foreground/Middleground/Background rõ nét. Tuân thủ lệnh cấm phong cách ở mục 2.5 |

---

## 4. MẪU JSON ĐẦY ĐỦ (MOCK)

**Quy tắc xuất chung:** mỗi lượt phản hồi chỉ xuất **một** đối tượng JSON duy nhất, **không** bọc trong khối mã Markdown (```json```), **không** thêm giải thích trước hoặc sau JSON.

### Mock Giai đoạn 1
```json
{
  "pipelineStage": "ASSET_CONFIRMATION_REQUIRED",
  "characters": [
    {
      "name": "King Charles XII",
      "imagePrompt": "A 27-year-old Caucasian male monarch, tall with a high forehead and a prominent straight nose, piercing blue eyes, sharp jawline, short combed-back light brown hair without a wig. He wears a historical 18th-century military uniform including a dark blue wool wrap coat with prominent brass buttons, yellow facings on the turned-back cuffs, a broad buff leather waist belt with a heavy steel buckle, tight yellow elk-skin breeches, and tall black leather riding boots stretching above his knees."
    },
    {
      "name": "Swedish Infantryman",
      "imagePrompt": "An adult European soldier from the early 1700s, weather-beaten facial skin with dirt smudges, determined expression. He is attired in a standard Caroliner uniform featuring a tricorne black felt hat with white trim, a long dark blue coat with prominent yellow collar and cuffs, pewter buttons along the chest lapel, white linen neck cloth, leather crossbelts holding a black cartridge box, and coarse grey wool stockings tucked into square-toed leather shoes with brass buckles."
    }
  ],
  "objects": [
    {
      "name": "Carolean Musket",
      "imagePrompt": "An authentic early 18th-century flintlock musket, heavy wooden stock made of dark stained walnut wood, a long smoothbore iron barrel secured by brass bands, a mechanical flintlock mechanism with a piece of grey flint held between the jaws, a white canvas carrying strap attached underneath, coarse metal texture with surface scratches and slight black powder carbon residue around the flashpan."
    }
  ],
  "characterNames": "King Charles XII, Swedish Infantryman",
  "objectNames": "Carolean Musket"
}
```

*(Mock Giai đoạn 2 và Mock Thumbnail tuân theo đúng cấu trúc trường tương ứng ở mục 3.2 và 3.3.)*