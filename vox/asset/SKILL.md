---
name: asset
description: Extract visual entities (Characters, Props, Documents, Maps) from VOX documentary scripts with paper collage cutout descriptions in pure JSON format.
disable-model-invocation: true
---

# VOX DOCUMENTARY ASSET EXTRACTION SYSTEM

This system extracts all visual entities from VOX documentary scripts into structured JSON data formatted for editorial paper collage visual style.

---

## 1. ASSET CLASSIFICATION & PAPER COLLAGE VISUAL STYLE

### 1.1 Asset Categories
- **Living Entities (`characters`)**: Key historical figures, investigators, suspects, eyewitnesses, group cutouts.
- **Inanimate Objects & Props (`props`)**: Evidence items, passports, cash bundles, historical documents, map cutouts, weapons, stamps.

### 1.2 Paper Collage Visual Description Standard
In each `visual` field, provide a detailed physical description incorporating paper collage attributes:
1. **Subject Identity**: Exact name, role, era, clothing, or gear.
2. **Cutout Texture**: Black and white halftone photograph cutout with rough scissor-cut edges and a red offset accent stroke.
3. **Paper Material**: Mounted on aged newsprint or archival map paper texture, with visible drop shadow layer separation.
4. **No Action/Emotion**: Keep visual descriptions static and material-focused.

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object:

```json
{
  "characters": [
    {
      "name": "Dan_Cooper",
      "visual": "Black and white halftone cutout of a middle-aged man wearing a dark suit and black tie, rough scissor-cut edges with a subtle hot red offset outline stroke, mounted on aged newsprint paper."
    }
  ],
  "props": [
    {
      "name": "Cash_Ransom_Bundle_1971",
      "visual": "Hand-cut paper cutout of a stack of 1970s twenty-dollar bills bound by a paper strap, black and white halftone print texture, offset red marker underline accent, matte paper drop shadow."
    }
  ]
}
```
