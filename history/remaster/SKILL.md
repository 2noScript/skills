---
name: remaster
description: Transform raw script drafts into professional, high-retention YouTube documentary scripts and output 100% clean voice-over narration in pure JSON format.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY SCRIPT REMASTER SYSTEM

This system transforms raw script drafts into dramatic, engaging historical documentary scripts styled for high YouTube audience retention (such as *Kings and Generals*, *Epic History TV*), returning pure JSON containing 100% clean voice-over narration text.

---

## 1. MANDATORY RULE: 100% PURE NARRATION VOICE-OVER ONLY

The `"script"` field in the returned JSON **MUST ONLY CONTAIN PURE VOICE-OVER NARRATION TEXT READY FOR DIRECT TEXT-TO-SPEECH (TTS) / RECORDING**.

### ⛔ STRICTLY FORBIDDEN LIST (8 Types of Formatting Noise):

1. ❌ **FORBIDDEN: Cinematic / Scene / Hook tags in brackets**:
   - Examples FORBIDDEN: `[CINEMATIC OPENING - HIGH TENSION HOOK]`, `[SCENE 1]`, `[HOOK]`, `[FLYCAM SHOT]`, `[PART 1]`, `[INTRO]`, `[OUTRO]`.
2. ❌ **FORBIDDEN: Visual / Footage / B-roll instructions in parentheses or brackets**:
   - Examples FORBIDDEN: `(Visual: Map of Europe)`, `[B-Roll: Tank movement]`, `(Visual: Map animation)`, `(Black and white archival footage)`.
3. ❌ **FORBIDDEN: Sound effect / Music cues in parentheses or brackets**:
   - Examples FORBIDDEN: `(SOUND EFFECT: Low drone...)`, `(SFX: Explosion...)`, `(INTENSE BACKGROUND MUSIC)`, `(Music fades)`.
4. ❌ **FORBIDDEN: Speaker prefixes or narrator labels**:
   - Examples FORBIDDEN: `NARRATOR (VOICEOVER):`, `NARRATOR:`, `VOICEOVER:`, `SPEAKER:`, `HOST:`.
5. ❌ **FORBIDDEN: Character speech labels or quote prefixes**:
   - Examples FORBIDDEN: `General A (says):`, `Napoleon:`, `[Character Voice]:`. (If citing character quotes, write as continuous narration: *Napoleon once declared that...*).
6. ❌ **FORBIDDEN: Timestamps**:
   - Examples FORBIDDEN: `[00:00 - 02:30]`, `(01:15)`, `Timestamp: 05:00`.
7. ❌ **FORBIDDEN: Markdown headers or text styling formatting**:
   - Examples FORBIDDEN: `# Title`, `## Part 1`, `**Phase 1:**`, `> Narration`, `---`.
8. ❌ **FORBIDDEN: Editorial notes, AI greetings, or conversational remarks**:
   - Examples FORBIDDEN: `(Note: this part can be trimmed)`, `Hope this script fits your request!`, `(Continued in Part 2)`.

---

### ✅ STANDARD CLEAN NARRATION EXAMPLE:

```text
On a freezing winter night in 1941, heavy snow fell across the dense forests on the outskirts of Moscow. The formidable army that had once swept across Europe now faced a far more dangerous adversary: forty-below temperatures and the resolute resistance of the Red Army.
```

> **GOLDEN GOAL:** Text inside the `"script"` field will be fed directly into Text-to-Speech (TTS) engines. Any bracketed characters or speaker labels will ruin the audio output.

---

## 2. FACT PRESERVATION RULE

- **PRESERVE HISTORICAL FACTS**: Do not invent false events, dates, or non-existent historical figures. Stick strictly to verified historical facts from the source draft.

---

## 3. CHUNKING MECHANISM (Max-Length Rolling Chunks)

- **Part 1 (`[PART 1]`):** Immediately generate JSON for Part 1 with maximum script volume (800–1000 words).
- **Subsequent Parts (`[PART N]`):** Upon receiving the `[OK]` trigger, generate JSON for Part N at maximum volume.
- **Final Part (`[FINAL PART]`):** Generate JSON for the final chunk (`currentPart: "FINAL"`).
- **Pure JSON Rule**: Output strictly 1 valid JSON object with no external conversational text.

---

## MANDATORY OUTPUT FORMAT

Output strictly a single valid JSON object:

```json
{
  "targetDurationMinutes": "Requested target duration in minutes",
  "currentPart": "Example: 1/3, 2/3, or FINAL",
  "totalParts": 3,
  "estimatedWordCountInThisPart": 0,
  "script": "Continuous 100% pure voice-over narration text. Absolutely zero [CINEMATIC...], (SOUND EFFECT...), NARRATOR: or bracketed notes."
}
```
