---
name: vox-director-prompt-generator
description: Comprehensive Vox Director skill specification explaining Beat vs Shot concepts, dynamic multi-shot beat structures, paper collage prompt rules, and JSON schema.
---

# Vox Director Script & Shot Architecture Reference

This skill defines the complete screenwriting architecture, beat-to-shot relationship, and JSON schema for Vox Director editorial paper-collage documentary generation.

---

## 1. Core Concepts: Beat vs. Shot

### 📖 What is a Beat? (`Beat`)
A **Beat** is a single **storytelling unit (Story Beat)** in the documentary script.
- It carries one coherent sentence or thought of spoken narration (`narration`), an emotional tone (`feel`), a background paper color (`bg`), and chapter titles (`title_cn`, `title_en`).
- **Example Beat**: *"Năm 1956 tại đại học Dartmouth, khái niệm AI lần đầu tiên được định hình."*

### 🎥 What is a Shot? (`Shot`)
A **Shot** is an individual **visual camera framing / keyframe image** created to illustrate a part of that Beat.
- Each shot has a duration (`dur`), camera movement (`camera_move`), element motion (`motion`), visual scene description (`scene`), prompt (`keyframe_prompt`), and a title banner flag (`title: true | false`).

### ❓ Can 1 Beat have multiple Shots?
**YES!** The number of shots per beat is **flexible and dynamic (1, 2, 3, or more shots)** depending on the narration content and visual pacing:

1. **Standard Vox Editorial Style (2 Shots per Beat)**:
   - **Shot A (`title: true`)**: Wide establishing shot (`shot_type: "wide"`). Shows the full 2D paper scene and renders the bold uppercase headline banner (`title_en`).
   - **Shot B (`title: false`)**: Detail cut-in shot (`shot_type: "detail"`). Focuses closely on a specific paper cutout element or action without the big headline banner.

2. **Single Keyframe Mode (1 Shot per Beat)**:
   - Used for quick ads, sports tributes (e.g. `ronaldo-9x16-kling.beats.json`), or short 15s videos. Each beat is illustrated by 1 single visual poster.

3. **Multi-Cut Sequence Mode (3+ Shots per Beat)**:
   - Used for longer beats (8–10s) or fast-paced climax scenes: Shot A (Wide) → Shot B (Medium) → Shot C (Detail Cut-in).

---

## 2. System Prompt Specification for AI Scriptwriter

```text
You are Vox Director Scriptwriter — an expert documentary screenwriter specializing in Vox-style editorial paper-collage videos.

YOUR TASK:
Analyze the input raw article text and convert it into a multi-part JSON response containing:
- "totalParts": Estimated number of parts needed to cover the entire article (e.g. 1, 2, 3...)
- "currentPart": The current part indicator (e.g. "1/3")
- "beats": Array of Beats for the current part.

BEAT & SHOT ARCHITECTURE RULES:
1. **Faithful Coverage**: Faithfully cover and split the entire input raw article text into sequential beats from start to finish.
2. **Hook Rule**: Beat 1 MUST hook the viewer in ≤3s with a bold claim, surprising stat, or provocative question.
3. **Flexible Multi-Shot**: Dynamically determine the best number of shots per Beat (1, 2, or 3+ shots) based on narration length and visual pacing:
   - Shot A (first shot): Wide shot orienting the scene, title: true (renders headline_text/title_en banner).
   - Subsequent Shots: Detail cut-in shots, title: false (focuses on item/action).
4. **Shot Duration**: Every Shot duration `dur` MUST be >= 5 seconds (e.g. 5s, 6s, 7s, 8s). Calculate `dur` dynamically based on the length and detail of the shot narration. DO NOT hardcode every shot to 5s.
5. **Anti-Monotony**: Never use the same camera move on 2 adjacent beats. Rotate camera moves across: push_in, pull_out, pan, tilt, parallax, static.
6. **2D Paper Collage Style**: Describe scenes as layered 2D paper cut-outs with torn edges, halftone print dots, newspaper clippings, and printed photo textures (NOT 3D, NOT CGI).

STRICT BEAT & SHOT JSON STRUCTURE:
- Every Beat in "beats" MUST contain: "title_cn", "title_en", "bg" (paper color tone), "feel" (emotional tone), "narration", "shots" (Array of 1, 2, or 3+ Shots).
- Each Shot inside "shots" MUST contain: "dur" (number >= 5), "title" (boolean), "camera_move" ("push_in" | "pull_out" | "pan" | "tilt" | "parallax" | "static"), "scene" (2D paper scene description), "motion" (camera + paper element motion), "keyframe_prompt" (5-layer editorial paper collage prompt).

YOU MUST RESPOND ONLY WITH VALID JSON MATCHING THIS EXACT FORMAT:
{
  "totalParts": 2,
  "currentPart": "1/2",
  "beats": [
    {
      "title_cn": "CÀ PHÊ MUỐI",
      "title_en": "SALT COFFEE",
      "bg": "warm amber paper",
      "feel": "intriguing",
      "narration": "Năm 2010 tại Huế, một tách cà phê kỳ lạ ra đời...",
      "shots": [
        {
          "dur": 6,
          "title": true,
          "camera_move": "push_in",
          "scene": "a wide paper collage of salt coffee shop in Hue...",
          "motion": "slow commanding push-in toward the coffee cup; paper salt grains drift",
          "keyframe_prompt": "Editorial paper collage of salt coffee shop in Hue, hand-cut paper figures, aged yellow newsprint texture, halftone pattern, bold red accent headline SALT COFFEE"
        },
        {
          "dur": 5,
          "title": false,
          "camera_move": "parallax",
          "scene": "a close cut-in of milk foam pouring...",
          "motion": "one slow push-in on the detail; milk foam cut-out rotates softly",
          "keyframe_prompt": "Detail paper collage cutout of milk foam pouring into coffee cup, halftone print pattern, torn paper edges, warm amber background tone"
        }
      ]
    }
  ]
}
```

---

## 3. Field Specification & Schema Reference

### 🌐 Cấp Root Payload (Root Level)

| Field Name | Type | Purpose / Detailed Description | Example |
|---|---|---|---|
| `totalParts` | `number` | Tổng số phần (Batch Parts) được AI ước tính để phủ toàn bộ bài viết thô | `2` |
| `currentPart` | `string` | Chỉ số Part hiện tại mà AI vừa trả về | `"1/2"` |
| `music` | `string` *(optional)* | Mô tả phong cách nhạc nền BGM độc đáo phù hợp với chủ đề bài viết | `"cinematic acoustic score with warm percussion"` |
| `beats` | `array` | Mảng chứa danh sách các Phân cảnh (Beat) | `[...]` |

---

### 📖 Cấp Beat (Phân cảnh Kịch bản)

| Field Name | Type | Purpose / Detailed Description | Example |
|---|---|---|---|
| `narration` | `string` | Lời đọc thuyết minh Voiceover cho Beat (chứa 1 câu hoàn chỉnh ~15–25 từ) | `"Năm 2010 tại Huế, một tách cà phê kỳ lạ ra đời..."` |
| `title_cn` | `string` | Tiêu đề phân cảnh ngắn gọn bằng tiếng Việt / tiếng Hoa đại diện cho nhịp phim | `"CÀ PHÊ MUỐI"` |
| `title_en` | `string` | Banner chữ in hoa tiếng Anh nổi bật trên khung hình Shot A | `"SALT COFFEE"` |
| `bg` | `string` | Tone màu nền giấy dán (Paper Background Tone). Định hình màu sắc tấm nền giấy 2D flat cho phân cảnh. <br/>**Các Option khuyên dùng (Options)**: <br/>- `warm amber paper` (Giấy ngả vàng ấm / hoài niệm) <br/>- `imperial deep-red` (Đỏ sẫm cổ điển / hoàng gia) <br/>- `warm ochre` (Màu vàng đất ochre / vintage) <br/>- `deep indigo night-blue` (Xanh chàm đêm sẫm / huyền bí) <br/>- `earthy clay tan` (Màu đất sét nung / cổ xưa) <br/>- `aged yellow newsprint` (Giấy báo cũ ngả vàng) <br/>- `flat cream paper` (Giấy kem mịn tối giản) <br/>- `neon green zine paper` (Giấy xanh neon nổi bật phong cách zine) | `"warm amber paper"`, `"imperial deep-red"`, `"warm ochre"` |
| `feel` | `string` | Sắc thái cảm xúc (Emotional Tone & Pacing Anchor). Định hình tone màu visual grading và nhịp chuyển động của AI model. <br/>**Các Option khuyên dùng (Options)**: <br/>- `intriguing, grand` (Tò mò, hoành tráng) <br/>- `ancient, humble, rough` (Mộc mạc, cổ xưa, thô ráp) <br/>- `brisk, building` (Sôi động, dồn dập tích tụ) <br/>- `revolutionary, clever, bold` (Đột phá, thông minh, táo bạo) <br/>- `institutional, cold, modern` (Lạnh lẽo, hiện đại, thể chế) <br/>- `electric, frontier, cold neon energy` (Năng lượng điện tử, bùng nổ neon) <br/>- `reflective, profound, resonant, cinematic` (Trầm lắng, sâu sắc, dư ối phim tài liệu) | `"intriguing, grand"`, `"ancient, humble"`, `"reflective, cinematic"` |
| `shots` | `array` | Mảng chứa danh sách các Khung hình visual (Shot) của Beat (1, 2, hoặc 3+ Shots) | `[...]` |

---

### 🎥 Cấp Shot (Khung hình Visual)

| Field Name | Type | Purpose / Detailed Description | Example |
|---|---|---|---|
| `dur` | `number` | Thời lượng hiển thị của Shot tính theo giây (Bắt buộc >= 5 giây) | `5` |
| `title` | `boolean` | Flag hiển thị banner tiêu đề: `true` cho Shot A wide, `false` cho các Shot detail cận cảnh | `true` / `false` |
| `scene` | `string` | Mô tả chi tiết bối cảnh các mảng giấy dán 2D (Cutout paper scene description) | `"a wide paper collage of salt coffee shop in Hue..."` |
| `motion` | `string` | Mô tả kết hợp góc máy camera và hiệu ứng trôi/xoay của phần tử giấy dán | `"slow commanding push-in toward the coffee cup..."` |
| `camera_move` | `string` *(optional)* | Loại chuyển động camera (`push_in`, `pull_out`, `pan`, `tilt`, `parallax`, `static`) | `"push_in"` |
| `keyframe_prompt` | `string` *(optional)* | Prompt 5 lớp editorial paper collage đầy đủ dùng để tạo ảnh keyframe AI | `"Editorial paper collage of salt coffee shop..."` |

