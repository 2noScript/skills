---
name: remaster
description: Transform raw drafts into high-retention VOX-style documentary narration scripts with historical cold open, 2.5 wps pacing, factual tone, and cliffhanger ending in pure JSON format.
disable-model-invocation: true
---

# VOX DOCUMENTARY SCRIPT REMASTER SYSTEM

This system transforms raw script drafts or transcripts into continuous, factual VOX-style documentary narration scripts optimized for high audience retention.

---

## 1. MANDATORY RULE: 100% PURE NARRATION VOICE-OVER ONLY

The `"script"` field in the returned JSON **MUST ONLY CONTAIN PURE VOICE-OVER NARRATION TEXT READY FOR DIRECT TEXT-TO-SPEECH (TTS) / RECORDING**.

### ⛔ STRICTLY FORBIDDEN:
1. ❌ NO section titles, chapters, headers (`[SCENE 1]`, `[HOOK]`, `[INTRO]`).
2. ❌ NO camera instructions, B-roll notes, or visual cues (`(Visual: Map)`, `[B-Roll: Tank]`).
3. ❌ NO sound effects or music cues (`(SFX: Drone)`, `(Music fades)`).
4. ❌ NO speaker prefixes (`NARRATOR:`, `VOICEOVER:`).
5. ❌ NO timestamps (`[00:00]`, `(01:15)`).
6. ❌ NO em dashes (`—`). Use commas, parentheses, colons, or standard hyphens.

---

## 2. NARRATION WRITING DNA & DISCIPLINE

### 2.1 Cold Open Requirement
The opening 3 to 4 sentences (approx. 30–40 words) **must** immediately establish:
- An **exact date**
- A **named location**
- One **specific physical action**
- *Example*: `"November 24, 1971. Portland International Airport. A man wearing a dark suit purchases a one-way ticket to Seattle using cash. He gives the name Dan Cooper."`

### 2.2 Voice & Sentence Pacing
- Target speaking pace: **2.5 words per second**.
- Maintain a calm, objective, factual documentary tone throughout.
- Use mostly short declarative sentences.
- Connect events using chronological transitions: `then`, `by morning`, `three days later`, `within the hour`, `because of this`, `which meant`, `what nobody knew was`.
- Every sentence must communicate **only one idea** and be **visually imaginable**.

### 2.3 Fact Integrity & Respectful Tone
- Attribute disputed facts: `"The FBI believed..."`, `"Witnesses reported..."`, `"Investigators concluded..."`.
- Respectful handling of tragedies: create tension through maps, documents, physical evidence, timelines, and weather rather than graphic sensationalism.

### 2.4 Mandatory 12-Word Cliffhanger Ending
The final sentence **must contain 12 words or fewer** and end on an object, person's name, date, or short factual statement.

---

## 3. CHUNKING MECHANISM

- **Part 1 (`[PART 1]`):** Immediately generate JSON for Part 1.
- **Subsequent Parts (`[PART N]`):** Upon receiving `[OK]`, generate JSON for Part N.
- **Final Part (`[FINAL PART]`):** Generate JSON for the final chunk (`currentPart: "FINAL"`).

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object:

```json
{
  "targetDurationMinutes": "2",
  "currentPart": "FINAL",
  "totalParts": 1,
  "estimatedWordCountInThisPart": 300,
  "script": "November 24, 1971. Portland International Airport. A man wearing a dark suit purchases a one-way ticket to Seattle using cash. He gives the name Dan Cooper. Ten minutes later, flight 305 takes off into a cold rainstorm. What nobody on board knew was that Cooper was carrying four parachutes and twenty thousand dollars in cash."
}
```
