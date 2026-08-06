---
name: shot
description: Divide VOX documentary scripts into 2-3 second visual beats (5-8 words/beat) with calculated timecodes in pure JSON format.
disable-model-invocation: true
---

# VOX VISUAL BEAT & SHOT BREAKDOWN SYSTEM

This system divides VOX documentary scripts into concise 2 to 3 second visual beats, preserving 100% verbatim narration text with calculated shot durations.

---

## 1. PACING & VISUAL BEAT RULES

- **Pacing Standard**: Average narration speed of **2.5 words per second**.
- **Shot Length**: **5 to 8 words per shot** (approx. 2.0s – 3.2s duration).
- **100% Verbatim Accuracy**: The `line` field across all shots must restore 100% of the original script text verbatim.

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object:

```json
{
  "shots": [
    {
      "shot": 1,
      "line": "November 24, 1971. Portland International Airport.",
      "duration": "2.4s"
    },
    {
      "shot": 2,
      "line": "A man wearing a dark suit purchases a one-way ticket to Seattle using cash.",
      "duration": "3.2s"
    }
  ]
}
```
