---
name: animate
description: Convert VOX paper collage image prompts into 6-second stop-motion assembly animation prompts with locked camera rules and paper ASMR audio guidance in pure JSON format.
disable-model-invocation: true
---

# VOX PAPER COLLAGE ANIMATION GENERATION SYSTEM

This system receives **VOX Paper Collage Image Prompts** for each Shot and converts them into production-ready 6-second stop-motion paper assembly animation prompts (for Runway Gen-3, Luma Dream Machine, Kling AI, Hunyuan Video).

---

## 1. MANDATORY MOTION & CAMERA RULES

1. **Locked Camera**: The camera must remain completely locked throughout the entire animation. **NO** zooming, panning, tilting, rotation, orbiting, dolly movement, tracking, handheld motion, focus changes, reframing, cuts, or transitions.
2. **0 to 7 Seconds (Build Process)**: Start with only the empty newsprint background plate. The collage assembles itself back-to-front in narrative order:
   - Hero cutout slides into place with subtle paper drag.
   - Supporting cutouts arrive and pin into position with a 2-frame stamped landing.
   - Tape presses onto paper, typewriter labels slide into place, rubber stamps impact.
   - Red string draws naturally between brass pins, marker underlines/arrows appear last.
3. **7 to 10 Seconds (Living Poster)**: Completed collage remains fixed. Only subtle paper corner lifts, halftone texture shimmers, or string vibrations are allowed.
4. **Audio**: No narration, voices, or music. Use paper-based ASMR sounds (paper sliding, cardstock taps, tape pressing, stamp impacts, string movement, pin clicks, room tone).

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object per batch:

```json
{
  "shots": [
    {
      "shot": 1,
      "visual": "Transform the provided image into a 6-second premium editorial documentary paper-collage animation. Preserve the final composition of the provided image exactly. Style: hand-cut documentary paper collage in motion. Aged newsprint surface, halftone photo cutouts, torn edges, tape, stamps, red string. Stop-motion cadence, stepped easing, 2 to 3 frame holds. CAMERA RULES: Locked camera throughout, no zooming, panning, or dolly. 0 to 7 SECONDS: Assembles back-to-front, hero cutout slides into place, supporting cutouts pin into position, tape presses, typewriter labels slide, red string draws between pins. 7 to 10 SECONDS: Living poster, subtle paper corner lift and string vibration. AUDIO: No narration or music, subtle paper ASMR sounds, cardstock taps, pin clicks, room tone."
    }
  ]
}
```
