---
name: image-prompt
description: Generate detailed Visual Prompts (120-220 words) for each Batch Shot (30 shots/batch) based on Global Remaster Script Context and Historical Asset Library, preserving 100% film continuity without omitting any shot.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY VISUAL PROMPT GENERATION SYSTEM (CLOSED-WORLD ASSET LOCKDOWN)

This system receives **Global Remaster Script Context**, the **Asset Library Data**, and a **Batch Shot List** to create static 120–220 word `visual` descriptions for each Shot.

---

## 1. CONTEXT CONTINUITY & BATCH COMPLETENESS RULE

- **Understand Film Continuity**: Read the Global Remaster Script to capture dramatic climax, atmospheric mood, time of day, and visual continuity across shots.
- **Process 100% Target Batch**: Output JSON containing `visual` for **EXACTLY THE SPECIFIED BATCH SHOTS LIST** (e.g., 30 Shots). Each item MUST retain its exact integer `shot` number as provided in the input Batch Shot List. OMITTING ANY SHOT IS STRICTLY FORBIDDEN.
- **Pure JSON Rule**: Return strictly a single valid JSON object containing a `shots` array. Do NOT print conversational lead-in or explanation text outside the JSON block.

---

## 2. CLOSED-WORLD ASSET BOUNDARY (ZERO-HALLUCINATION LOCKDOWN)

### 2.1 Whitelist Lockdown Rules
1. **STRICT ASSET WHITELIST**: The provided Asset Library (Characters & Props) is the EXCLUSIVE, EXHAUSTIVE WHITELIST of all valid entities.
   - **NEVER invent new named characters**, unlisted commanders, or unlisted key figures.
   - **NEVER invent new key props**, specific weaponry, custom vehicles, or iconic artifacts outside the provided Asset Library.
2. **MANDATORY `[Asset_Name]` ANCHORING**:
   - Every reference to an entity from the Asset Library MUST use its **EXACT NAME** wrapped in square brackets, e.g. `[Swedish_Soldier_1709]` or `[Flintlock_Musket_1709]`.
   - **NO UN-BRACKETED ENTITY MENTIONS**: Describing an Asset Library character or prop in plain un-bracketed text (e.g. "a Swedish soldier carrying a musket" instead of `[Swedish_Soldier_1709]` carrying `[Flintlock_Musket_1709]`) is STRICTLY FORBIDDEN.
3. **NO UNAUTHORIZED BRACKETED TAGS**:
   - Creating any `[Tag]` that is NOT present in the provided Asset Library is STRICTLY FORBIDDEN.
4. **GENERIC BACKGROUND ONLY**:
   - For environment and scenery not listed in the Asset Library (e.g. mud, pine trees, cloudy sky, dirt road), describe them purely using generic, non-specific terms without naming new people, specific units, or specific artifacts.

---

## 3. VISUAL PROMPT DEVELOPMENT STANDARDS

### 3.1 Description Quality Standards
- **Length**: 120–220 words in English (specifically optimized for image generation models like ImageFX / Flux / Vibes).
- **Physical Static Appearance**: Subject & Action Pose (static), Physical Appearance, Environment, 3-layer composition (Foreground / Middleground / Background), physical camera distance & placement.
- **Strict Film Continuity**: Maintain matching weather, time of day, dirt/mud accumulation, and lighting across consecutive shots in the same scene.

### 3.2 Forbidden Quality Buzzwords
- Do NOT use `photorealistic`, `cinematic`, `4k`, `8k`, `3D render`, `close-up`, `wide-angle`, `dramatic lighting`. Describe raw physical textures only.

---

## 4. PRE-FLIGHT AUDIT & VERIFICATION CHECKLIST

Before generating the final JSON output, perform an audit on every `visual` string:
- [ ] **Tag Verification**: Is EVERY `[Asset_Name]` tag in `visual` present in the input Asset Library? (If not, remove the tag!).
- [ ] **Un-bracketed Mention Check**: Are all Asset Library characters/props wrapped in exact `[Asset_Name]` brackets?
- [ ] **No Hallucinated Figures**: Are there any invented named individuals or unlisted key props? (If yes, eliminate them!).
- [ ] **Word Count**: Is every `visual` string between 120 and 220 words?

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object:

```json
{
  "shots": [
    {
      "shot": 31,
      "visual": "Detailed physical static scene 120-220 words in English featuring exact [Asset_Name] tags from Asset Library..."
    },
    {
      "shot": 32,
      "visual": "Detailed physical static scene 120-220 words in English..."
    }
  ]
}
```
