---
name: image-prompt
description: Generate detailed VOX editorial paper collage visual prompts for each Batch Shot based on Global Remaster Script Context and Asset Library, in pure JSON format.
disable-model-invocation: true
---

# VOX EDITORIAL PAPER COLLAGE VISUAL PROMPT SYSTEM

This system receives **Global Remaster Script Context**, **Asset Library Data**, and a **Batch Shot List** to generate editorial paper collage `visual` prompts for each Shot.

---

## 1. ASSET ANCHORING & COMPOSITION RULES

1. **Exact `[Asset_Name]` Anchoring**: Whenever referencing characters or props from the provided Asset Library, wrap their exact name in square brackets (e.g. `[Dan_Cooper]`, `[Cash_Ransom_Bundle_1971]`).
2. **Hero Dominance**: 1 hero element occupying **~70%** visual emphasis.
3. **Supporting Cutouts**: Max 2–3 supporting elements.
4. **Negative Space**: Generous clean archival newsprint space around the main subject.
5. **Mandatory Suffixes**: Append the exact **STYLE BLOCK** and **CLOSER** to every generated `visual` prompt.

---

## MANDATORY STYLE BLOCK & CLOSER

### STYLE BLOCK (Exact Suffix)
`hand-cut documentary paper collage on aged newsprint and archival map surfaces, black and white halftone photograph cutouts with rough scissor-cut edges and offset accent strokes, torn paper edges, masking tape fragments, typewriter caption strips, rubber stamp marks, red string and brass pins where the story calls for connections, desaturated archival palette of tan, ink black, and halftone gray with ONE hot red signal accent and a restrained mustard yellow secondary, condensed bold headline lettering only where a label is specified, visible print grain and paper fiber, matte, flat even documentary lighting with soft cutout drop shadows.`

### CLOSER (Exact Suffix)
`Every element must appear physically hand-cut and layered from real paper, with visible cutout edges, halftone print texture, and soft shadow separation between layers. The composition stays clean, minimal, and editorial with generous negative space. NOT digital illustration, NOT cartoon, NOT 3D render, NOT glossy, no gradients, no clutter, no watermark, no logos, no text beyond the specified label. Premium documentary collage aesthetic, 16:9, ultra-detailed, 8K.`

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object per batch:

```json
{
  "shots": [
    {
      "shot": 1,
      "visual": "Black and white halftone cutout of [Dan_Cooper] occupying seventy percent of the frame over an aged map of Portland International Airport, torn newsprint background, hand-cut documentary paper collage on aged newsprint and archival map surfaces, black and white halftone photograph cutouts with rough scissor-cut edges and offset accent strokes, torn paper edges, masking tape fragments, typewriter caption strips, rubber stamp marks, red string and brass pins where the story calls for connections, desaturated archival palette of tan, ink black, and halftone gray with ONE hot red signal accent and a restrained mustard yellow secondary, condensed bold headline lettering only where a label is specified, visible print grain and paper fiber, matte, flat even documentary lighting with soft cutout drop shadows. Every element must appear physically hand-cut and layered from real paper, with visible cutout edges, halftone print texture, and soft shadow separation between layers. The composition stays clean, minimal, and editorial with generous negative space. NOT digital illustration, NOT cartoon, NOT 3D render, NOT glossy, no gradients, no clutter, no watermark, no logos, no text beyond the specified label. Premium documentary collage aesthetic, 16:9, ultra-detailed, 8K."
    }
  ]
}
```
