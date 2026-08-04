---
name: shot
description: Break down script content into precise Shot segments & Voice-over narration rhythms (8-14 words/shot) based on Global Remaster Context and Target Batch Chunks, guaranteeing 100% verbatim restoration without skipping any word.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SHOT BREAKDOWN SYSTEM (CONTEXT-AWARE BATCHING)

This system serves as the **CORE PRODUCER CONTENT ENGINE**. It receives **Global Remaster Script Context** and the **Target Batch Script Chunk** to split text into concise Shot segments matched to voice-over narration rhythm (`line`), preserving 100% verbatim text accuracy without skipping any words.

---

## 1. 100% VERBATIM CONSERVATION RULE

- **NO SUMMARIZATION - NO OMISSION - NO WORD ALTERATION**: The `line` field for every shot MUST be verbatim original script text. When combining all `line` sentences across batches, the text MUST match the original script **100% WORD-FOR-WORD**.
- **Process 100% Target Batch Chunk**: AI uses Global Remaster Script Context to understand story flow, but MUST break down 100% of the words in the specified Target Batch Chunk.

---

## 2. VOICE-OVER PACING & SHOT SEGMENTATION STANDARDS

- **Ideal Shot Length**: **8 to 14 words** per shot (equivalent to 3–5 seconds of voice-over narration per shot).
- **Semantic Clause Chunking**:
  - Break at commas, periods, semicolons, or natural grammatical clause boundaries.
  - NEVER split proper nouns or fixed phrases mid-way (e.g., Do NOT split between "Napoleon" and "Bonaparte", or "Battle" and "of Normandy").
- **Quote Escaping**: Escape double quotes inside `line` strings with `\"` or replace with single quotes `''` to prevent JSON parsing failures.

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object per batch:

```json
{
  "shots": [
    {
      "shot": 1,
      "line": "Verbatim excerpt from original script for shot 1 (8-14 words)..."
    },
    {
      "shot": 2,
      "line": "Verbatim excerpt from original script for shot 2..."
    }
  ]
}
```
