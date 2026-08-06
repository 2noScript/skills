---
name: animate
description: Convert historical documentary image prompts into 6-second video animation motion prompts (cinematic camera moves, particle dust, ember drift, historical motion) in pure JSON format.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY ANIMATION GENERATION SYSTEM

This system receives **Historical Image Prompts** for each Shot and converts them into cinematic 6-second AI video motion prompts (for Runway Gen-3, Luma Dream Machine, Kling AI, Hunyuan Video).

---

## 1. CINEMATIC MOTION & CAMERA RULES

1. **Controlled Camera Motion**: Specify a single smooth camera move per shot (e.g., slow forward push, subtle lateral tracking shot, gentle crane lift, static locked shot).
2. **Atmospheric Environment Motion**: Incorporate era-appropriate ambient particle motion (floating dust motes, drifting battlefield embers, subtle cannon smoke rise, wind blowing through coats/flags).
3. **Character & Asset Motion**: Micro-movements (breathing chest, blinking eyes, hand adjusting musket/sword hilt, horse shifting weight).
4. **Continuity**: Maintain 100% visual consistency with the static scene image prompt and `[Asset_Name]` descriptions.

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object per batch:

```json
{
  "shots": [
    {
      "shot": 1,
      "visual": "A 6-second cinematic historical animation. Slow forward push camera motion across a freezing winter battlefield near Moscow. Subtle smoke rises from burnt out wooden cabins in the background, light snow drifts horizontally across the frame, Swedish infantry soldier breathing cold air, realistic fabric movement on his blue coat, atmospheric cinematic depth."
    }
  ]
}
```
