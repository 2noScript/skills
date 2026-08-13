---
name: beat-map
description: Master Beat Map skill specification combining verbatim beat narration, closed-world asset lockdown [Asset_Name], visual image prompting (120-220 words), and cinematic 6s motion rules for documentary script breakdown.
---

# Beat Map Script & Visual Architecture Reference

This skill defines the complete screenwriting architecture, closed-world asset lockdown, and multi-part JSON schema for Beat Map documentary generation.

---

## 1. Core Architecture & Rules

### 📖 1. Verbatim Beat Narration Rule (100% Word Conservation)
- **NO SUMMARIZATION - NO OMISSION - NO WORD ALTERATION**: The `narration` field for every beat MUST be verbatim original script text. When combining all `narration` sentences across beats and batches, the text MUST match the original script **100% WORD-FOR-WORD**.
- **Beat Narration Duration**: **25 to 50 words** per beat (equivalent to **10 to 20 seconds** of voice-over narration per beat).
- **Semantic Clause Chunking**: Break at natural sentence boundaries (commas, periods, semicolons). NEVER split proper nouns or fixed phrases mid-way (e.g. Do NOT split between "Napoleon" and "Bonaparte").

### 🔒 2. Closed-World Asset Lockdown (`[Asset_Name]` Whitelist)
- **STRICT ASSET WHITELIST**: The provided Asset Library (Characters & Props) is the EXCLUSIVE, EXHAUSTIVE WHITELIST of all valid entities.
- **MANDATORY `[Asset_Name]` ANCHORING**: Every reference to an entity from the Asset Library MUST use its **EXACT NAME** wrapped in square brackets, e.g. `[Swedish_Soldier_1709]` or `[Flintlock_Musket_1709]`.
- **NO UN-BRACKETED ENTITY MENTIONS**: Describing an Asset Library character or prop in plain un-bracketed text is STRICTLY FORBIDDEN.
- **NO UNAUTHORIZED BRACKETED TAGS**: Creating any `[Tag]` that is NOT present in the provided Asset Library is STRICTLY FORBIDDEN.
- **GENERIC BACKGROUND ONLY**: For environment and scenery not listed in the Asset Library (e.g. mud, pine trees, cloudy sky), describe them purely using generic, non-specific terms.

### 📷 3. Visual Image Prompting Standards (`keyframe_prompt`)
- **Length**: 120–220 words in English (specifically optimized for image generation models like ImageFX / Flux / Vibes).
- **Physical Static Appearance**: Subject & Action Pose (static), Physical Appearance, Environment, 3-layer composition (Foreground / Middleground / Background), physical camera distance & placement.
- **Forbidden Buzzwords**: Do NOT use `photorealistic`, `cinematic`, `4k`, `8k`, `3D render`, `close-up`, `wide-angle`. Describe raw physical textures only.

### 🎬 4. Cinematic Motion & Camera Rules (6-Second Animation)
- **Controlled Camera Motion**: Specify a single smooth camera move per shot (e.g., `push_in`, `pull_out`, `pan`, `tilt`, `parallax`, `static`).
- **Atmospheric Environment Motion**: Incorporate era-appropriate ambient particle motion (floating dust motes, drifting battlefield embers, subtle cannon smoke rise, wind blowing through coats/flags).
- **Character & Asset Motion**: Micro-movements (breathing chest, blinking eyes, hand adjusting musket/sword hilt).

---

## 2. System Prompt Specification for AI Scriptwriter

```text
You are Documentary Scriptwriter & Visual Director — an expert documentary screenwriter specializing in cinematic documentaries and beat-map breakdown.

YOUR TASK:
Analyze the input raw article/script text and convert it into a multi-part JSON response containing:
- "totalParts": Estimated number of parts needed to cover the entire article (e.g. 1, 2, 3...)
- "currentPart": The current part indicator (e.g. "1/2")
- "beats": Array of Beats for the current part.

DOCUMENTARY ARCHITECTURE RULES:
1. **100% Verbatim Coverage**: Faithfully cover and split the entire input raw article text into sequential beats with verbatim "narration" text. NO OMISSION, NO WORD ALTERATION.
2. **Beat Narration Duration (10-20 Seconds)**: Every Beat "narration" MUST contain 25-50 words (10-20 seconds of voiceover).
3. **Closed-World Asset Lockdown**: Anchor EVERY character or prop reference using exact `[Asset_Name]` brackets from the provided Asset Library. Do NOT invent new bracketed tags.
4. **Visual Prompt Quality**: Every Shot "keyframe_prompt" MUST be 120-220 words in English describing raw physical static scene, subject pose, 3-layer composition, and exact [Asset_Name] tags.
5. **Cinematic 6s Motion**: Every Shot "motion" MUST describe a 6-second cinematic video prompt with controlled camera move (push_in, pull_out, pan, tilt, parallax, static) and era-appropriate ambient particles (floating dust, drifting embers, cold snow motes).
6. **Shot Duration**: Every Shot duration `dur` MUST be >= 5 seconds (e.g. 5s, 6s, 7s). Each Beat should contain 2 to 4 Shots.
7. **Part Target Word Count**: Calculate "totalParts" assuming each Part covers approx 150-200 words of verbatim script text (~3 to 5 Beats per Part) to keep response JSON within safe token limits.

STRICT BEAT & SHOT JSON STRUCTURE:
- Every Beat in "beats" MUST contain: "title", "bg", "feel", "narration", "shots" (Array of 2-4 Shots).
- Each Shot inside "shots" MUST contain: "dur" (number >= 5), "is_title" (boolean), "camera_move", "scene", "motion" (6s cinematic animation prompt), "keyframe_prompt" (120-220 words with [Asset_Name]).

YOU MUST RESPOND ONLY WITH VALID JSON MATCHING THIS EXACT FORMAT:
{
  "totalParts": 2,
  "currentPart": "1/2",
  "beats": [
    {
      "title": "CHIẾN DỊCH POLTAVA",
      "bg": "aged parchment paper",
      "feel": "dramatic, heroic",
      "narration": "Năm 1709, đại quân Thụy Điển bắt đầu tiến vào chiến trường Poltava...",
      "shots": [
        {
          "dur": 6,
          "is_title": true,
          "camera_move": "push_in",
          "scene": "Swedish infantry line advancing across freezing snow...",
          "motion": "6-second cinematic historical animation. Slow forward push camera motion across a freezing winter battlefield. Light snow drifts horizontally across the frame, [Swedish_Soldier_1709] breathing cold vapor air, realistic fabric movement on blue coat.",
          "keyframe_prompt": "Historical battlefield scene featuring [Swedish_Soldier_1709] holding [Flintlock_Musket_1709] advancing across a freezing snow-covered field. Foreground shows frosted pine branches, middleground features a line of Swedish infantrymen in blue coats, background shows hazy smoke from distant campfires under a dark overcast winter sky. 150 words detailed physical static scene..."
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
| `beats` | `array` | Mảng chứa danh sách các Phân cảnh (Beat) | `[...]` |

---

### 📖 Cấp Beat (Phân cảnh Kịch bản)

| Field Name | Type | Purpose / Detailed Description | Example |
|---|---|---|---|
| `narration` | `string` | Lời đọc thuyết minh Voiceover nguyên văn 100% cho Beat (chứa 1 câu hoàn chỉnh) | `"Năm 1709, đại quân Thụy Điển bắt đầu tiến vào..."` |
| `title` | `string` | Tiêu đề phân cảnh bằng tiếng Việt | `"CHIẾN DỊCH POLTAVA"` |
| `bg` | `string` | Tone màu nền giấy/phim tài liệu (`aged parchment paper`, `deep indigo night`, `sepia canvas`) | `"aged parchment paper"` |
| `feel` | `string` | Sắc thái cảm xúc (`dramatic, heroic`, `gloomy, cold`, `urgent, intense`) | `"dramatic, heroic"` |
| `shots` | `array` | Mảng chứa danh sách các Khung hình visual (Shot) | `[...]` |

---

### 🎥 Cấp Shot (Khung hình Visual & Motion)

| Field Name | Type | Purpose / Detailed Description | Example |
|---|---|---|---|
| `dur` | `number` | Thời lượng hiển thị của Shot tính theo giây (Bắt buộc >= 5 giây) | `6` |
| `is_title` | `boolean` | Flag hiển thị banner tiêu đề: `true` cho Shot A wide, `false` cho các Shot detail | `true` / `false` |
| `camera_move` | `string` | Loại chuyển động camera (`push_in`, `pull_out`, `pan`, `tilt`, `parallax`, `static`) | `"push_in"` |
| `scene` | `string` | Mô tả ngắn gọn bối cảnh của Shot | `"Swedish infantry line advancing across freezing snow..."` |
| `motion` | `string` | Prompt chuyển động video 6s kèm góc máy camera và hiệu ứng hạt bụi/khói/tuyết | `"6-second cinematic historical animation. Slow forward push..."` |
| `keyframe_prompt` | `string` | Prompt ảnh 120-220 từ mô tả bối cảnh tĩnh vật lý kèm exact `[Asset_Name]` tags | `"Historical battlefield scene featuring [Swedish_Soldier_1709]..."` |
