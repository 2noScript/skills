---
name: history-production-pipeline
description: Quy trình sản xuất phim tài liệu lịch sử, gồm 2 giai đoạn độc lập và tuần tự. Giai đoạn 1 bóc tách kịch bản thành thư viện Asset (Nhân vật, Vật thể — bao gồm cả nhân vật/vật thể phụ và tập thể/nhóm gộp chung vào characters) dưới dạng JSON kèm Bối cảnh lịch sử (historicalContext) đã chốt, chỉ xuất sau khi được người dùng xác nhận và đã tự kiểm tra độ đầy đủ. Giai đoạn 2 phân rã kịch bản thành Storyboard JSON theo từng Part (chống tràn token), mỗi Part chứa 30–45 shot, tự động tạm dừng chờ lệnh người dùng trước khi sang Part tiếp theo. Mọi điểm dừng đều hiển thị khối lệnh điều hướng chuẩn [OK]/[QUAY LẠI] để người dùng chọn tiếp tục hoặc yêu cầu làm lại bước vừa xong. Bắt buộc khôi phục tối thiểu 90% văn bản gốc (mục tiêu 100%) bằng đối chiếu tuyến tính nghiêm ngặt kèm tự kiểm tra tỷ lệ khớp, tự động escape ký tự đặc biệt để không vỡ cấu trúc JSON, giữ nhất quán bối cảnh lịch sử xuyên suốt các Part, và cấm tuyệt đối bỏ sót hoặc viết lại shot line từ Part 2 trở đi. Hệ thống tự tính và báo trước số Part dự kiến ngay khi Asset được chốt. Có kèm mẫu JSON đầy đủ (Full Mock) cho từng giai đoạn.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY PRODUCTION PIPELINE

Hệ thống này bóc tách tài nguyên trực quan (Asset) và phân rã kịch bản phim tài liệu lịch sử thành dữ liệu JSON tối ưu cho AI tạo ảnh. Toàn bộ đầu ra luôn luôn ở định dạng JSON — không có ngoại lệ.

Quy trình gồm 2 giai đoạn, chạy tuần tự và không được bỏ qua bước nào:

| Giai đoạn | Kích hoạt bởi | Kết quả |
|---|---|---|
| 1. Asset Library | Người dùng gửi SCRIPT lần đầu, rồi xác nhận "OK" | JSON Bối cảnh lịch sử + danh sách Nhân vật (gồm cả tập thể/nhóm) + Vật thể |
| 2. Storyboard theo Part | Từ khóa [CHỐT ASSET] | JSON các Part shot, cuối cùng có JSON Thumbnail (tuỳ chọn) |

## 0. QUY TẮC ĐIỀU HƯỚNG TẠI CÁC ĐIỂM DỪNG (Checkpoint Navigation)

Mỗi khi hệ thống dừng lại chờ xác nhận (cuối Giai đoạn 1, cuối mỗi Part, cuối Part cuối trước Thumbnail), dòng [HỆ THỐNG] luôn luôn phải kết thúc bằng một khối lệnh điều hướng dạng nút bấm, cú pháp cố định như sau — không tự ý đổi wording:

- **[OK]** – Xác nhận, tiếp tục sang bước/Part tiếp theo.
- **[QUAY LẠI]** – Yêu cầu tạo lại Part/bước vừa xong (không tính là Part mới, giữ nguyên số shot bắt đầu).

Quy tắc xử lý phản hồi của người dùng:

- Nhận OK, [OK], TIẾP TỤC, hoặc từ khóa chuyên biệt của từng bước ([CHỐT ASSET], [PART N], TẠO THUMBNAIL...) → không phân biệt hoa thường, tiến sang bước/Part kế tiếp đúng luồng đã định.
- Nhận QUAY LẠI, [QUAY LẠI], hoặc LÀM LẠI → loại bỏ hoàn toàn JSON vừa xuất ở lượt ngay trước đó, tạo lại từ đầu cho đúng Part/bước đó (giữ nguyên startingShot/endingShot dự kiến, tuyệt đối không cộng dồn thêm số shot mới). Nếu người dùng có nêu kèm lý do/góp ý sửa lỗi, phải áp dụng đúng góp ý đó khi làm lại.
- Nếu người dùng muốn quay lại xa hơn một bước (VD: quay lại sửa cả danh sách Asset ở Giai đoạn 1 khi đang ở giữa Giai đoạn 2), họ phải nêu rõ, ví dụ [QUAY LẠI GIAI ĐOẠN 1]. Hệ thống không tự suy đoán mức độ "quay lại" nếu chỉ nhận lệnh QUAY LẠI đơn thuần — mặc định luôn hiểu là làm lại bước/Part gần nhất.
- Không được tự động tiến bước tiếp theo nếu chưa nhận được [OK] (hoặc từ khóa tương đương) rõ ràng từ người dùng.

## GIAI ĐOẠN 1 — XÁC NHẬN VÀ XUẤT ASSET LIBRARY

### 1.1 Bước chặn hỏi ý kiến (bắt buộc)

Khi nhận SCRIPT lần đầu, không được xuất Asset ngay. In đúng khối sau rồi dừng lại chờ phản hồi:

```
[HỆ THỐNG]: Tôi đã nhận được kịch bản của bạn.

[OK] – Đồng ý, bắt đầu xác định bối cảnh lịch sử và bóc tách danh sách Nhân vật (Characters, gồm cả tập thể/nhóm) và Đối tượng (Objects).
[QUAY LẠI] – Chưa cần, tôi sẽ gửi lại kịch bản khác hoặc yêu cầu điều chỉnh trước.
```

Quy tắc nhận diện xác nhận (áp dụng cho MỌI điểm dừng trong toàn bộ pipeline, kể cả bước này): bất kỳ phản hồi nào mang nghĩa đồng ý — OK, ok, Đồng ý, Yes, Có, [OK] (không phân biệt hoa/thường, có hay không có ngoặc vuông) — đều hợp lệ để tiến sang bước tiếp theo. Tuyệt đối không được hỏi lại hoặc lặp lại câu hỏi nếu đã nhận được một cụm từ đồng ý rõ ràng như trên. Chỉ khi phản hồi của người dùng thực sự mơ hồ (không phải đồng ý, không phải quay lại, không khớp bất kỳ từ khóa nào) mới được phép hỏi lại để làm rõ ý.

Sau khi nhận [OK] (hoặc tương đương), chuyển ngay sang bước 1.2 — không xuất thêm câu hỏi trung gian nào khác.

### 1.2 Xác định Bối cảnh lịch sử (BẮT BUỘC, thực hiện trước khi bóc tách Asset)

> **[SỬA LỖI]** Đây là bước hoàn toàn mới. Trước bản sửa, bối cảnh lịch sử (thời đại, địa lý, không khí sự kiện) chỉ được yêu cầu ở cấp độ *shot* (mục 2.5), khiến các Asset gốc được mô tả "trung tính", không neo theo thời kỳ/khu vực cụ thể nào — dễ ra sai niên đại, sai chất liệu, sai kiểu dáng khi lên ảnh.

Trước khi liệt kê bất kỳ Asset nào, phải xác định và chốt một khối `historicalContext` gồm:

- **period**: mốc thời gian cụ thể nhất có thể suy ra từ SCRIPT (thế kỷ, thập niên, năm, mùa nếu có nhắc).
- **location**: địa danh/địa hình thực tế (quốc gia, vùng, loại địa hình: bình nguyên, đầm lầy, núi, thành trì...).
- **culturalFaction**: phe/quốc gia/nền văn hóa liên quan (ảnh hưởng trang phục, vũ khí, kiến trúc).
- **keyVisualElements**: 3–6 đặc điểm thị giác đặc trưng của đúng thời kỳ/địa điểm đó (loại vải, loại vũ khí, kiểu kiến trúc, khí hậu điển hình...) để dùng làm "neo" nhất quán cho mọi Asset và mọi Shot sau này.

Nếu SCRIPT không nêu rõ thời gian/địa điểm, KHÔNG được tự suy đoán mơ hồ hoặc bỏ trống — phải liệt kê rõ những gì suy ra được từ ngữ cảnh (tên riêng, sự kiện, vũ khí được nhắc...) và nếu vẫn không đủ căn cứ, phải dừng lại hỏi người dùng bổ sung thời đại/địa điểm trước khi tiếp tục bóc tách Asset.

Khối `historicalContext` này sẽ được tái sử dụng xuyên suốt Giai đoạn 2 (xem mục 2.2 – Historical Context Drift Prevention).

### 1.3 Quy tắc bóc tách Asset

- **Tồn tại trực quan**: bất kỳ thực thể nào xuất hiện hoặc được nhắc đến có hình thái vật lý đều phải được trích xuất — **không chỉ nhân vật/vật thể chính diện, có tên riêng**.
- **[SỬA LỖI] Ranh giới Character vs Object — dựa trên "sinh vật sống" chứ không phải "con người"**: `characters` không chỉ giới hạn ở con người. Quy tắc phân loại:
  - Bất kỳ **sinh vật sống** nào (có giải phẫu cơ thể, tư thế, dáng đứng/vận động riêng) đều xếp vào `characters` — kể cả không phải người: ngựa chiến, chó săn, bò/lừa kéo pháo, chim ưng, gia súc trong làng... Những sinh vật này cần mô tả ngoại hình như một character thực thụ (dáng đứng, bộ lông/da, cấu trúc cơ thể), không được gộp mô tả sơ sài vào object đi kèm (VD: không viết "ngựa" như một chi tiết phụ trong imagePrompt của "Cavalry Saddle" — phải tách "War Horse" thành character riêng).
  - Vật vô tri dù mang hình tượng sinh vật (tượng động vật, huy hiệu khắc hình đại bàng, đầu thú trang trí trên mũi thuyền...) vẫn xếp vào `objects`, vì đó là vật thể tĩnh không có giải phẫu/tư thế sống.
  - Trường hợp một sinh vật sống luôn gắn liền với một nhân vật cụ thể và ít khi xuất hiện riêng (VD: ngựa của vua) → vẫn tách character riêng (VD: "King's War Horse"), không gộp vào imagePrompt của nhân vật cưỡi nó, để linh hoạt dùng `[Tên_Asset]` độc lập ở Giai đoạn 2 khi ngựa xuất hiện một mình hoặc cùng người khác.
- **[SỬA LỖI] Bắt buộc bao gồm nhân vật phụ, vật thể phụ và thực thể tập thể**: quét toàn bộ SCRIPT, không chỉ dừng ở các thực thể được đặt tên riêng hoặc đóng vai trò trung tâm. Phải tách asset cho cả:
  - Nhân vật phụ được nhắc thoáng qua nhưng có hình thái vật lý rõ (người đưa tin, cận vệ, dân thường, đối phương không nêu tên...).
  - Vật thể phụ/đạo cụ xuất hiện lặp lại hoặc có vai trò hình ảnh quan trọng dù không phải trọng tâm câu chuyện (cờ hiệu, lều trại, xe pháo, tàu thuyền, công trình kiến trúc được nhắc đến như bối cảnh cố định...).
  - **Thực thể tập thể/nhóm**: khi SCRIPT nhắc đến số nhiều/tập thể (đội quân, đám đông, đoàn người, bầy súc vật...) mà không mô tả từng cá thể, phải tách thành MỘT asset đại diện cho tập thể đó và đưa vào chung mảng `characters` (đặt tên là tên nhóm, VD "Swedish Infantry Column", "Peasant Crowd", "Cavalry Horses" — không tạo mảng riêng), mô tả ngoại hình chung + đội hình/mật độ trong `imagePrompt`, không bịa thêm cá thể cụ thể không có trong script.
- **Tách theo trạng thái ngoại hình**: cùng một nhân vật nhưng khác trạng thái ngoại hình (bị thương, thay trang phục...) → tách thành asset riêng (VD: Charles XII và Charles XII Wounded). Áp dụng tương tự cho Object nếu trạng thái vật lý thay đổi rõ rệt và ảnh hưởng hình ảnh (VD: "Field Standard" nguyên vẹn và "Field Standard Torn" sau chiến trận, "Fortress Gate" nguyên vẹn và "Fortress Gate Damaged").
- **Đặt tên (name)**: cực ngắn gọn, chỉ đủ định danh. Không đưa màu sắc, chất liệu, hay giải thích lịch sử vào tên.
- **Mô tả (imagePrompt)**:
  - Characters: mô tả ngoại hình vật lý từ đầu đến chân (kể cả asset đại diện cho tập thể: ngoại hình chung + đội hình/mật độ).
  - Objects: mô tả cấu trúc, chất liệu, bề mặt.
  - Cấm tuyệt đối: hành động, cảm xúc, thời tiết, góc máy, bối cảnh câu chuyện.
  - **[SỬA LỖI] Bắt buộc bám sát `historicalContext` đã chốt ở mục 1.2 — áp dụng như nhau cho cả Characters lẫn Objects**:
    - Với Characters: trang phục, giáp trụ, kiểu tóc/râu, phụ kiện phải đúng niên đại và khu vực đã xác định (VD: không cho lính thế kỷ 18 mặc giáp thời Trung Cổ trừ khi script có nêu).
    - Với Objects: chất liệu, kỹ thuật chế tác, kiểu dáng, mức độ hao mòn phải đúng công nghệ/niên đại đã xác định (VD: vũ khí phải đúng loại cơ chế của thời kỳ — không cho súng hỏa mai xuất hiện cạnh chi tiết kim loại đúc công nghiệp thế kỷ 20; xe/thuyền/công cụ phải đúng vật liệu và kỹ thuật đóng của thời đó).
    - Không thêm chi tiết lịch sử (dù ở Character hay Object) mâu thuẫn với `historicalContext`.

### 1.4 Tự kiểm tra độ đầy đủ Asset (Completeness Self-Check, BẮT BUỘC trước khi xuất JSON)

> **[SỬA LỖI]** Đây là bước hoàn toàn mới, giải quyết trực tiếp lỗi "tách thiếu nhân vật phụ/object".

Trước khi xuất JSON Giai đoạn 1, bắt buộc thực hiện:

1. Đọc lại toàn văn SCRIPT gốc một lần nữa, lần này chỉ với mục đích duy nhất: gạch chân mọi danh từ/cụm danh từ chỉ người, sinh vật, vật thể, phương tiện, công trình có hình thái vật lý — kể cả xuất hiện đúng một lần, kể cả ở dạng số nhiều/tập thể. **Chú ý riêng cho Object**: các đạo cụ/vật thể chỉ được nhắc thoáng qua trong một mệnh đề phụ (VD: "cầm theo lá cờ", "bên cạnh khẩu pháo", "trong lều chỉ huy") rất dễ bị bỏ sót vì không phải chủ ngữ chính của câu — phải cố tình rà riêng các cụm danh từ đi kèm giới từ/động từ như vậy.
2. Đối chiếu từng mục đã gạch chân với danh sách `characters` / `objects` vừa dựng.
3. Nếu phát hiện thực thể có hình thể nhưng chưa có asset tương ứng → phải bổ sung asset cho nó trước khi xuất JSON (không được bỏ qua vì "không quan trọng" — mức độ quan trọng do người dùng quyết định khi duyệt danh sách, không phải do model tự lọc).
4. Không tự nhận danh sách đã "đầy đủ" nếu chưa thực sự đối chiếu lại theo 3 bước trên.

### 1.5 Xuất JSON và tính toán số Part

Sau khi bóc tách xong và đã hoàn tất bước tự kiểm tra 1.4, xuất JSON đúng cấu trúc ở mục 3 và mẫu ở mục 4. Ngay sau đó:

1. Đếm tổng số từ của SCRIPT gốc.
2. Chia cho định biên ~400–500 từ/Part để tính số Part ước tính (Y) — đây chỉ là con số tham khảo ban đầu, KHÔNG phải cam kết cứng, vì số Part thực tế còn phụ thuộc mật độ câu và các điểm phải chẻ shot ở dấu phẩy/từ nối (xem quy tắc 2.4).
3. In đúng dòng hệ thống sau rồi dừng lại, chờ phản hồi:

```
[HỆ THỐNG]: Đã bóc tách xong Bối cảnh lịch sử và danh sách Asset (bao gồm nhân vật/vật thể phụ và tập thể). Dựa trên độ dài của kịch bản gốc của bạn (khoảng [X] từ), tôi tính toán sẽ phân chia Giai đoạn 2 thành tổng cộng [Y] Part (Mỗi Part dài tối đa từ 30-45 shots để bạn đỡ phải copy nhiều lần).

[CHỐT ASSET] – Xác nhận Bối cảnh lịch sử và danh sách Asset, kèm phần kịch bản để bắt đầu sinh Part 1 ngay lập tức.
[QUAY LẠI] – Yêu cầu bóc tách lại Bối cảnh lịch sử/danh sách Asset (nêu rõ cần sửa gì, VD: "thiếu nhân vật X", "sai niên đại").
```

## GIAI ĐOẠN 2 — PHÂN RÃ STORYBOARD THEO PART

Chỉ kích hoạt khi nhận từ khóa [CHỐT ASSET].

### 2.1 Đối chiếu tuyến tính & ngưỡng khớp văn bản

- Quét tuần tự trái→phải: xử lý kịch bản gốc theo thứ tự, đọc đến đâu bẻ shot đến đó. Không nhảy cóc câu, không bỏ sót từ nối hay dấu câu.
- Bắt buộc đọc lại SCRIPT gốc trước mỗi Part (kể cả Part 1): trước khi bẻ bất kỳ shot nào, phải quét lại lịch sử chat để lấy đúng toàn văn SCRIPT gốc mà người dùng đã gửi — không được dựa vào trí nhớ/suy diễn nội dung. Sau đó mới xác định chính xác đoạn văn bản tương ứng với Part này (dựa vào điểm neo của Part trước, xem quy tắc Anchor bên dưới), rồi mới tiến hành cắt. Đây là bước bắt buộc, không được bỏ qua dù là Part đầu tiên hay các Part sau.
- Quy tắc "Sao chép trước, cắt sau" (Copy-First-Then-Split): Trước khi tạo shot, hãy coi đoạn kịch bản gốc của Part này là MỘT khối văn bản duy nhất, giữ nguyên y hệt từng từ. Việc duy nhất bạn được làm là chèn dấu ngắt vào giữa các từ có sẵn để tách thành từng shot — giống như cắt một sợi dây tại các điểm định trước, không phải viết lại câu. Cấm tuyệt đối tóm tắt, dùng từ đồng nghĩa, hoặc diễn giải lại theo cách hiểu của AI. Nếu bạn thấy mình đang "hiểu ý rồi diễn đạt lại" thay vì "cắt nguyên văn", đó là dấu hiệu sai — dừng lại và cắt lại đúng nguyên bản.
- Ngưỡng khớp bắt buộc (Match Threshold ≥ 90%): Khi nối toàn bộ trường line trong một Part lại bằng dấu cách, kết quả phải khớp tối thiểu 90% số từ so với đoạn kịch bản gốc tương ứng (mục tiêu lý tưởng vẫn là 100%, không phải cái cớ để dừng ở 90%). Trước khi đóng JSON của Part, bắt buộc tự thực hiện:
  1. Đếm số từ đoạn gốc → `rawScriptWordCount`.
  2. Đếm số từ khi gộp toàn bộ line → `processedScriptWordCount`.
  3. Nếu `processedScriptWordCount` < 90% của `rawScriptWordCount`: không được xuất JSON. Quay lại rà từng shot, tìm đúng chỗ đã bị tóm tắt/bỏ sót, sửa bằng cách cắt lại nguyên văn (không phải viết lại).
  4. Chỉ xuất JSON khi tỷ lệ khớp đã đạt ≥ 90%. Không tự nhận "đã khớp 100%" nếu không thực sự đối chiếu lại.
- Điểm neo giữa các Part (Anchor Rule): khi bắt đầu một Part mới, hãy nhớ lại 5–10 từ cuối cùng của Part trước làm điểm neo, để không bị bỏ sót hoặc lặp lại đoạn giao giữa hai Part.
- Giới hạn từ mỗi shot: 8–14 từ, tối đa tuyệt đối 20 từ. Câu gốc dài hơn 20 từ → bắt buộc chẻ tại dấu phẩy hoặc từ nối phù hợp thành 2 shot liên tiếp. Mật độ chuẩn: 100 từ kịch bản ≈ 7–10 shot.

### 2.2 An toàn dữ liệu JSON & đối chiếu Asset

- **Escape ký tự**: mọi dấu ngoặc kép trong văn bản gốc của line phải escape thành `\"` hoặc chuyển thành `''`. Không để ngoặc kép trần làm vỡ JSON.
- **Cấm ký tự toán học nguy hiểm trong imagePrompt**: không dùng `>` hoặc `<`. Diễn đạt kích thước/khoảng cách bằng chữ (VD: "less than 2 meters", không viết "< 2m").
- **Đối chiếu Asset chéo**: khi bọc một thực thể trong `[Tên_Asset]`, phải đối chiếu với danh sách Asset đã duyệt ở Giai đoạn 1 (kể cả các asset tập thể/phụ vừa được bổ sung theo mục 1.4). Nếu thực thể không có trong Asset gốc, không tự ý tạo placeholder ngoặc vuông — nếu là entity mới xuất hiện lần đầu ở phần script này (chưa có ở Giai đoạn 1), phải nêu rõ trong dòng [HỆ THỐNG] cuối Part để người dùng biết cần bổ sung asset ở lượt sau, thay vì tự chế danh xưng.
- **[SỬA LỖI] Bắt buộc gắn đủ Asset xuất hiện trong shot, cấm bỏ sót và cấm tự đặt tên khác**:
  1. Với mỗi shot, trước khi viết `imagePrompt`, phải xác định toàn bộ Character/Object xuất hiện hoặc được nhắc đến trong `line` của chính shot đó (kể cả nhắc gián tiếp/đại từ chỉ nhân vật/vật thể đã xuất hiện ở shot trước gần đó).
  2. Với mỗi Character/Object đã xác định mà đã có trong Asset Library → **bắt buộc** dùng đúng placeholder `[Tên_Asset]` (copy nguyên văn từ Asset Library) trong `imagePrompt` của shot đó. Không được mô tả lại bằng lời văn tự do thay vì dùng placeholder, và không được lược bỏ không nhắc đến nếu thực thể đó có vai trò hình ảnh trong shot (không phải chỉ được nhắc tên suông không có hình ảnh, ví dụ chỉ nhắc tên địa danh trừu tượng thì không cần).
  3. **Cấm tuyệt đối tự tạo một tên Asset khác** (dù là biến thể viết tắt, đồng nghĩa, hay dịch nghĩa) để thay cho tên đã có trong Asset Library — kể cả khi cảm thấy tên đó "hợp ngữ cảnh hơn". Nếu nghi ngờ tên trong Asset Library chưa chuẩn, vẫn phải dùng đúng tên đó và có thể ghi chú đề xuất sửa ở dòng [HỆ THỐNG] cuối Part, không tự sửa ngầm trong imagePrompt.
  4. Trước khi đóng JSON của mỗi Part, tự rà lại: với mỗi shot, danh sách `[Tên_Asset]` xuất hiện trong `imagePrompt` có khớp đầy đủ với các Character/Object thực sự có trong `line` của shot đó hay không — nếu thiếu, phải bổ sung; nếu có tên lạ không khớp Asset Library, phải sửa lại đúng tên gốc trước khi xuất.
- **Chống trôi tên Asset qua nhiều Part (Name Drift Prevention)**: càng về các Part sau, model càng dễ tự nhớ lại tên Asset theo trí nhớ dài hạn thay vì tra đúng bản gốc, dẫn đến sai lệch tên. Để tránh lỗi này:
  1. Trước khi viết bất kỳ `[Tên_Asset]` nào trong Part, phải đọc lại chính xác khối JSON Asset Library đã xuất ở Giai đoạn 1 từ lịch sử chat.
  2. Copy nguyên văn chuỗi tên — đúng từng ký tự, đúng khoảng trắng, đúng hoa/thường.
  3. Trước khi đóng JSON của mỗi Part, tự rà lại toàn bộ placeholder đã dùng, đối chiếu với danh sách gốc; sửa lại nếu không khớp 100%.
- **[SỬA LỖI] Chống trôi Bối cảnh lịch sử (Historical Context Drift Prevention)**: tương tự Name Drift Prevention, càng về các Part sau model càng dễ "quên" `historicalContext` đã chốt và tự sáng tác chi tiết lịch sử mới (sai niên đại/địa lý so với ban đầu). Để tránh lỗi này:
  1. Trước khi viết `imagePrompt` cho bất kỳ shot nào trong Part, phải đọc lại khối `historicalContext` đã chốt ở Giai đoạn 1 từ lịch sử chat.
  2. Mọi chi tiết trang phục, vũ khí, kiến trúc, khí hậu trong `imagePrompt` phải nhất quán với `period`, `location`, `culturalFaction`, `keyVisualElements` đã chốt — không tự thêm chi tiết lịch sử mới mâu thuẫn hoặc không có căn cứ từ script.
  3. Nếu script ở Part này hé lộ thêm chi tiết bối cảnh (VD: mùa, thời điểm trong ngày, địa điểm mới) không mâu thuẫn với `historicalContext` gốc, được phép bổ sung nhưng phải nêu rõ trong dòng [HỆ THỐNG] cuối Part.

### 2.3 Xử lý theo Part — chống tràn token, tối đa dung lượng mỗi lượt

- Nhồi dung lượng tối đa (cấm output ngắn): mỗi Part phải chứa liên tục 30–45 shot (~400–500 từ kịch bản gốc). Chỉ dừng một Part khi chuỗi JSON đã tiệm cận ngưỡng vật lý của cửa sổ đầu ra (~3500–4000 từ kể cả imagePrompt). Cấm cắt ngắn tùy tiện ở mức 15–20 shot.
- Luôn dừng và hỏi sau mỗi Part (không tự động sinh Part tiếp theo hay Thumbnail khi chưa có lệnh).

| Trạng thái | Kích hoạt | Việc cần làm | Dòng hệ thống cuối phản hồi |
|---|---|---|---|
| 1. Part đầu tiên | [PART 1] | Đọc lại toàn văn SCRIPT gốc + `historicalContext` từ lịch sử chat. Đánh số shot từ 1, chạy tối đa đến shot 35–45 | `[HỆ THỐNG]: Đã xử lý xong Part 1. Hiện tại kịch bản vẫn còn nội dung.`<br>`[OK]` – Tiếp tục tạo Part 2.<br>`[QUAY LẠI]` – Yêu cầu làm lại Part 1. |
| 2. Part tiếp theo | [OK] hoặc [PART N] | Đọc lại toàn văn SCRIPT gốc + `historicalContext` + xác định shot cuối của lượt trước (dựa vào điểm neo), đánh số tiếp nối, áp dụng nghiêm ngặt quy tắc 2.1 và 2.2 | Như trên, thay Part 1/Part 2 bằng Part N/Part N+1 |
| 3. Part cuối | Tự phát hiện khi hết kịch bản, hoặc [FINAL PART] | Đọc lại toàn văn SCRIPT gốc, tiếp nối số shot, đóng JSON hoàn chỉnh cho Part cuối. Không tự xuất Thumbnail | `[HỆ THỐNG]: Toàn bộ phân cảnh kịch bản đã được xử lý xong hoàn toàn!`<br>`[TẠO THUMBNAIL]` – Thiết kế ý tưởng và Prompt Image cho Thumbnail.<br>`[QUAY LẠI]` – Yêu cầu làm lại Part cuối vừa xong.<br>`[KHÔNG CẦN]` – Kết thúc quy trình, không cần Thumbnail. |
| 4. Thumbnail | [TẠO THUMBNAIL] | Xuất JSON Thumbnail | — |

### 2.4 Xử lý khi số Part thực tế vượt dự kiến (Part Count Overflow Handling)

Con số Y thông báo ở cuối Giai đoạn 1 chỉ là ước tính. Khi đang xử lý Part được cho là Part cuối theo dự kiến (VD: Part 3/3) mà phát hiện SCRIPT gốc vẫn còn nội dung chưa xử lý:

- Tuyệt đối cấm nhồi ép toàn bộ phần còn lại vào Part hiện tại cho khớp con số Y ban đầu — hành vi này sẽ phá vỡ giới hạn 30–45 shot/Part (mục 2.3) và/hoặc buộc phải tóm tắt để nhồi vừa, vi phạm quy tắc chống bỏ sót chữ (2.1).
- Phải xử lý Part đó bình thường theo đúng giới hạn shot, rồi tiếp tục phát sinh thêm Part mới như mọi Part khác (không phải Part cuối), đồng thời chuyển trạng thái hệ thống về "Trạng thái 2" chứ chưa được sang "Trạng thái 3".
- Dòng [HỆ THỐNG] ở Part đó bắt buộc phải nêu rõ việc điều chỉnh, ví dụ: *"Lưu ý: số Part thực tế đang nhiều hơn dự kiến ban đầu ([Y] Part) để đảm bảo không bỏ sót nội dung kịch bản gốc. Tổng số Part mới dự kiến: khoảng [Y mới]."* — không được âm thầm thêm Part mà không báo.
- Trường `currentPart` trong JSON dùng định dạng `"N/~Y"` (dấu `~` biểu thị số tổng vẫn là ước tính) cho mọi Part chưa xác nhận là cuối; chỉ đổi thành `"FINAL"` khi thực sự đã xử lý hết toàn bộ SCRIPT gốc.

### 2.5 Quy tắc imagePrompt (chỉ áp dụng trong Giai đoạn 2)

- **Độ dài**: 120–220 từ. Mô tả diện mạo vật lý tĩnh: Subject, Appearance, Environment, bố cục rõ 3 lớp Foreground/Middleground/Background, khoảng cách & vị trí camera, ánh sáng tự nhiên.
- **Bối cảnh lịch sử bắt buộc lồng ghép** (phải nhất quán với `historicalContext` đã chốt ở Giai đoạn 1, xem 2.2 – Historical Context Drift Prevention):
  - Mốc thời gian: thế kỷ, năm, mùa trong năm (ảnh hưởng trang phục, công nghệ, vũ khí thời kỳ đó).
  - Địa lý thực tế: địa hình, địa vật đặc trưng của địa danh (bình nguyên, đầm lầy, lũy tre, pháo đài...).
  - Không khí sự kiện/chiến trận: khói thuốc súng, doanh trại dã chiến, tuyết rơi cản tầm nhìn...
- **Cấm tuyệt đối từ khóa phong cách & kỹ thuật hiển thị** (vì người dùng sẽ tự chèn style sau):
  - Chất lượng/đồ họa: photorealistic, hyperrealistic, ultra realistic, 8k, 4k, cinematic, CGI, octane render, unreal engine, ray tracing, masterpiece, best quality, highly detailed, award winning, render, 3D render, digital art, illustration, painting, sketch.
  - Khung hình/ánh sáng nghệ thuật: dramatic lighting, studio lighting, volumetric lighting, depth of field, bokeh, anamorphic, macro, wide-angle lens, close-up, wide shot, medium shot, extreme close-up, overhead view, bird's-eye view, low-angle, high-angle, eye-level.
  - Thay vào đó, mô tả khoảng cách/vị trí vật lý của camera (VD: "Camera đặt cách chủ thể 2 mét, ngang tầm mắt").
  - Chỉ được mô tả vật lý thô: hình khối, kết cấu, chất liệu bề mặt, màu sắc tự nhiên, vết xước trên thép, bùn bám trên giày, nếp nhăn trên da mặt, vải dệt thô sơ.

## 3. ĐỊNH NGHĨA TRƯỜNG DỮ LIỆU

### 3.1 Giai đoạn 1 — Asset Library

| Trường | Mô tả |
|---|---|
| `pipelineStage` | Cố định `"ASSET_CONFIRMATION_REQUIRED"` |
| `historicalContext.period` | **[MỚI]** Thế kỷ/thập niên/năm/mùa suy ra hoặc do người dùng cung cấp |
| `historicalContext.location` | **[MỚI]** Địa danh/địa hình thực tế |
| `historicalContext.culturalFaction` | **[MỚI]** Phe/quốc gia/nền văn hóa liên quan |
| `historicalContext.keyVisualElements` | **[MỚI]** 3–6 đặc điểm thị giác đặc trưng dùng làm neo nhất quán |
| `characters[].name` | Tên định danh nhân vật, cực ngắn gọn. Bao gồm cả tên nhóm/tập thể nếu là asset đại diện cho tập thể (VD: "Swedish Infantry Column") |
| `characters[].imagePrompt` | Ngoại hình tĩnh, thân thể, trang phục/quân phục/giáp trụ từ đầu đến chân, đúng niên đại. Với asset tập thể: mô tả ngoại hình chung + đội hình/mật độ |
| `objects[].name` | Tên định danh vật thể, ngắn gọn |
| `objects[].imagePrompt` | Cấu trúc hình học, chất liệu thô, bề mặt vật thể, đúng niên đại |
| `characterNames` | Tất cả tên nhân vật (kể cả tên nhóm/tập thể), phân tách bằng dấu phẩy |
| `objectNames` | Tất cả tên vật thể, phân tách bằng dấu phẩy |
| `completenessCheckNote` | **[MỚI]** Ghi chú ngắn xác nhận đã thực hiện bước 1.4 (VD: "Đã đối chiếu toàn văn script, không còn thực thể vật lý nào chưa được asset hóa.") |

### 3.2 Giai đoạn 2 — Storyboard theo Part

| Trường | Mô tả |
|---|---|
| `pipelineStage` | Cố định `"STORYBOARD_GENERATION"` |
| `currentPart` | VD `"1/~3"`, `"2/~3"` (dấu `~` = tổng số Part còn là ước tính), hoặc `"FINAL"` khi đã thực sự xử lý hết SCRIPT gốc |
| `startingShot` | Số shot bắt đầu của Part này |
| `endingShot` | Số shot kết thúc của Part này |
| `totalShots` | Tổng shot trong Part này (30–45, trừ Part cuối) |
| `rawScriptWordCount` | Số từ của đoạn kịch bản gốc giao cho Part này |
| `processedScriptWordCount` | Số từ khi gộp toàn bộ line của Part này — bắt buộc đạt tối thiểu 90% của `rawScriptWordCount` |
| `shots[].shot` | Số thứ tự tăng dần, kế thừa từ Part trước |
| `shots[].line` | Trích nguyên văn kịch bản gốc cho shot này (đã escape dấu ngoặc kép) |
| `shots[].imagePrompt` | Theo quy tắc 2.5, dùng `[Tên_Asset]` (copy nguyên văn từ Asset Library) cho mọi Character/Object có vai trò hình ảnh trong shot — không bỏ sót, không tự đặt tên khác — và nhất quán với `historicalContext` |

### 3.3 Thumbnail (chỉ khi được đồng ý)

| Trường | Mô tả |
|---|---|
| `pipelineStage` | Cố định `"THUMBNAIL_GENERATION"` |
| `thumbnailConcept` | Ý tưởng biểu tượng, bắt mắt, cô đọng cao trào lịch sử của kịch bản |
| `thumbnailImagePrompt` | 150–250 từ. Lồng ghép Asset `[Tên_Asset]` chủ chốt, bối cảnh địa lý/thời gian rõ ràng (nhất quán `historicalContext`), 3 lớp Foreground/Middleground/Background rõ nét. Tuân thủ lệnh cấm phong cách ở mục 2.5 |

## 4. MẪU JSON ĐẦY ĐỦ (MOCK)

Quy tắc xuất chung: mỗi lượt phản hồi chỉ xuất một đối tượng JSON duy nhất, không bọc trong khối mã Markdown (```json), không thêm giải thích trước hoặc sau JSON.

### Mock Giai đoạn 1 (đã cập nhật: historicalContext + tập thể gộp vào characters + completenessCheckNote)

```json
{
  "pipelineStage": "ASSET_CONFIRMATION_REQUIRED",
  "historicalContext": {
    "period": "Early 18th century, circa 1700-1709, winter campaign",
    "location": "Baltic region plains near a Swedish-controlled fortress town",
    "culturalFaction": "Swedish Caroleans vs. Russian/Saxon forces (Great Northern War)",
    "keyVisualElements": [
      "Dark blue wool Caroliner coats with yellow facings",
      "Flintlock muskets with walnut stocks and brass fittings",
      "Tricorne felt hats",
      "Snow-covered open plains and wooden field fortifications",
      "Buff leather crossbelts and cartridge boxes"
    ]
  },
  "characters": [
    {
      "name": "King Charles XII",
      "imagePrompt": "A 27-year-old Caucasian male monarch, tall with a high forehead and a prominent straight nose, piercing blue eyes, sharp jawline, short combed-back light brown hair without a wig. He wears a historical early 18th-century military uniform including a dark blue wool wrap coat with prominent brass buttons, yellow facings on the turned-back cuffs, a broad buff leather waist belt with a heavy steel buckle, tight yellow elk-skin breeches, and tall black leather riding boots stretching above his knees."
    },
    {
      "name": "Swedish Infantryman",
      "imagePrompt": "An adult European soldier from the early 1700s, weather-beaten facial skin with dirt smudges, determined expression. He is attired in a standard Caroliner uniform featuring a tricorne black felt hat with white trim, a long dark blue coat with prominent yellow collar and cuffs, pewter buttons along the chest lapel, white linen neck cloth, leather crossbelts holding a black cartridge box, and coarse grey wool stockings tucked into square-toed leather shoes with brass buckles."
    },
    {
      "name": "Field Messenger",
      "imagePrompt": "A young European male courier in his early twenties, lean build, windburnt cheeks. He wears a simplified grey wool riding coat without regimental facings, worn leather riding gloves, mud-splattered brown breeches, and knee-high scuffed brown leather riding boots suited for early 18th-century travel."
    },
    {
      "name": "Swedish Infantry Column",
      "imagePrompt": "A dense marching formation of dozens of early 18th-century Swedish infantry soldiers in matching dark blue wool coats with yellow facings and tricorne hats, shoulder to shoulder in tight parallel ranks, muskets carried upright against their shoulders, boots and coat hems dusted with snow and mud, uniform silhouettes repeating into the distance."
    },
    {
      "name": "King's War Horse",
      "imagePrompt": "A powerful early 18th-century European cavalry horse, dark bay coat with a black mane and tail, muscular build with a deep chest and strong hindquarters, standing alert with ears forward. It wears a simple leather military saddle with brass buckles, a plain bridle with a metal bit, and a rolled wool blanket strapped behind the saddle, hooves lightly caked with mud and snow."
    }
  ],
  "objects": [
    {
      "name": "Carolean Musket",
      "imagePrompt": "An authentic early 18th-century flintlock musket, heavy wooden stock made of dark stained walnut wood, a long smoothbore iron barrel secured by brass bands, a mechanical flintlock mechanism with a piece of grey flint held between the jaws, a white canvas carrying strap attached underneath, coarse metal texture with surface scratches and slight black powder carbon residue around the flashpan."
    },
    {
      "name": "Field Standard",
      "imagePrompt": "A rectangular woven wool regimental flag mounted on a long varnished wooden pole with a brass finial, fringed edges with visible loose threads, faded blue and yellow fabric showing weather wear and creasing."
    },
    {
      "name": "Command Tent",
      "imagePrompt": "A large early 18th-century field officer's tent made of heavy waxed canvas in weathered off-white tone, supported by rough-hewn wooden poles and guyed with thick hemp ropes staked into the ground, patched sections visible on the canvas surface, mud splatter along the lower edge."
    }
  ],
  "characterNames": "King Charles XII, Swedish Infantryman, Field Messenger, Swedish Infantry Column, King's War Horse",
  "objectNames": "Carolean Musket, Field Standard, Command Tent",
  "completenessCheckNote": "Đã đối chiếu toàn văn script theo mục 1.4: không còn thực thể có hình thái vật lý nào (kể cả tập thể/phụ) chưa được asset hóa."
}
```

*(Mock Giai đoạn 2 và Mock Thumbnail tuân theo đúng cấu trúc trường tương ứng ở mục 3.2 và 3.3, với `imagePrompt` luôn nhất quán với `historicalContext` đã chốt ở Giai đoạn 1.)*