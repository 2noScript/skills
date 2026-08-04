---
name: image-prompt
description: Generate detailed Visual Prompts (120-220 words) for each Batch Shot (30 shots/batch) based on Global Remaster Script Context and Historical Asset Library, preserving 100% film continuity without omitting any shot.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY VISUAL PROMPT GENERATION SYSTEM (CONTEXT-AWARE BATCHING)

This system receives **Global Remaster Script Context**, **Asset Library Data**, and a **Batch Shot List** to create static 120–220 word `visual` descriptions for each Shot.

---

## 1. CONTEXT CONTINUITY & BATCH COMPLETENESS RULE

- **Understand Film Continuity**: AI reads the Global Remaster Script to capture dramatic climax, atmospheric mood, time of day, and visual continuity across shots.
- **Process 100% Target Batch**: AI outputs JSON containing `visual` for **EXACTLY THE SPECIFIED BATCH SHOTS LIST** (e.g., 30 Shots). OMITTING ANY SHOT IN THE BATCH IS STRICTLY FORBIDDEN.
- **Pure JSON Rule**: Return strictly a single valid JSON object containing a `shots` array. Do NOT print conversational lead-in text outside the JSON block.

---

## 2. VISUAL PROMPT DEVELOPMENT STANDARDS

### 2.1 Asset Safety & Placeholders
- **Exact `[Asset_Name]` Anchoring**: Copy the exact character name string from the provided Asset Library. Creating unauthorized variant names is forbidden.
- **Prevent Historical Drift**: All clothing, armor, weaponry, and terrain details in `visual` must remain strictly consistent with Asset descriptions.

### 2.2 Description Quality Standards for Visual Prompt
- **Length**: 120–220 words in English (specifically optimized for image generation models).
- **Physical Static Appearance Description**: Subject, Appearance, Environment, 3-layer composition (Foreground / Middleground / Background), physical camera distance & placement.
- **Forbidden Quality Buzzwords**: Do NOT use `photorealistic`, `cinematic`, `4k`, `8k`, `3D render`, `close-up`, `wide-angle`, `dramatic lighting`. Describe raw physical textures only.

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object:

```json
{
  "shots": [
    {
      "shot": 1,
      "visual": "Detailed physical static scene 120-220 words featuring [Asset_Name]..."
    },
    {
      "shot": 2,
      "visual": "Detailed physical static scene 120-220 words..."
    }
  ]
}
```
