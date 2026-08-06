---
name: seo
description: Generate YouTube-ready SEO Metadata (5-8 question hook titles, video description note, hashtags) and 3 paper-collage thumbnail prompts in pure JSON format.
disable-model-invocation: true
---

# VOX DOCUMENTARY SEO & THUMBNAIL GENERATION SYSTEM

This system generates SEO metadata and 3 high-impact paper-collage thumbnail prompts based on the completed documentary script.

---

## 1. METADATA & THUMBNAIL STANDARDS

### 1.1 Question Hook Titles (`titles`)
Generate **5 to 8 different title options**. Each title MUST start with a Question Word (`Why`, `How`, `What`, `Who`, `When`, `Tại sao`, `Làm thế nào`, `Ai`...) or documentary title formula (`How [Event] Unfolded`, `What Really Happened to [Subject]`).

### 1.2 Video Description Note (`formattedNote`)
Write a compelling summary capturing context and story climax from the script.

### 1.3 Hashtags (`hashtags`)
Generate **8 to 15 hashtags** (starting with `#`).

### 1.4 Thumbnail Prompts (`thumbnails`)
Generate 3 thumbnail prompts:
- 1 high-contrast B&W halftone hero cutout (~70% space). *If real person, place a black censor bar across eyes.*
- Large condensed ALL CAPS headline on torn paper strip (1 to 3 words).
- 1 red marker circle / underline emphasis device.
- Mandatory Thumbnail Closer: `Every element must appear physically hand-cut and layered from real paper, with visible cutout edges, halftone print texture, and soft shadow separation between layers. The composition stays clean, minimal, and editorial with generous negative space. NOT digital illustration, NOT cartoon, NOT 3D render, NOT glossy, no gradients, no clutter, no watermark, no logos, no text beyond the specified thumbnail words. Premium documentary collage aesthetic, 16:9, ultra-detailed, 8K.`

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object:

```json
{
  "titles": [
    "How Did Dan Cooper Vanish Into Thin Air?",
    "Why Did the FBI Never Find Flight 305?",
    "What Really Happened to the $200,000 Ransom?",
    "Who Was the Man Behind the Alias Dan Cooper?",
    "When Will the Mystery of Flight 305 Finally Be Solved?"
  ],
  "formattedNote": "On November 24, 1971, a man claiming to be Dan Cooper hijacked Flight 305, extorted $200,000 in cash, and parachuted into the night. He was never seen again.",
  "hashtags": [
    "#TrueCrime",
    "#Documentary",
    "#DBCooper",
    "#ColdCase",
    "#History",
    "#Mysteries"
  ],
  "thumbnails": [
    {
      "concept": "Concept 1 - High Contrast Hero Portrait",
      "prompt": "High contrast black and white halftone hero cutout of Dan Cooper with rough scissor-cut edges and a red offset accent stroke, black censor bar placed over his eyes, torn aged newsprint background, large condensed ALL CAPS headline 'VANISHED' on a torn red paper strip, red marker circle highlighting the 1971 date stamp. Every element must appear physically hand-cut and layered from real paper, with visible cutout edges, halftone print texture, and soft shadow separation between layers. The composition stays clean, minimal, and editorial with generous negative space. NOT digital illustration, NOT cartoon, NOT 3D render, NOT glossy, no gradients, no clutter, no watermark, no logos, no text beyond the specified thumbnail words. Premium documentary collage aesthetic, 16:9, ultra-detailed, 8K."
    }
  ]
}
```
