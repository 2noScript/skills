---
name: seo
description: Generate YouTube-ready SEO Metadata (5-8 question hook titles starting with Why/How/What/Who/When..., Video description, SEO keywords, 8-15 Hashtags) based on completed script content in pure JSON format.
disable-model-invocation: true
---

# YOUTUBE SEO METADATA GENERATION SYSTEM

This system synthesizes and generates an optimized YouTube SEO Metadata package based on complete documentary/story scripts.

---

## 1. METADATA GENERATION STANDARDS

### 1.1 Curiosity Question Titles (Multi-Title Question Hooks)
MANDATORY generation of **5 to 8 different title options** for user selection:
* Each title **MUST start with a Question Word** (e.g., `Why`, `How`, `What`, `Who`, `When`, `Tại sao`, `Làm thế nào`, `Ai`...).
* Each title explores a different angle, key character, or central conflict from the script.
* Strictly adhere to factual events in the script without clickbait lies.
* Optimal length for YouTube titles (50–70 characters).

### 1.2 Video Description
* Write a compelling summary capturing context and story climax from the script.

### 1.3 SEO Keywords
* Comma-separated list of SEO keywords.

### 1.4 Hashtags
* Generate **8 to 15 hashtags** (starting with `#`, single words without spaces).

---

## MANDATORY OUTPUT JSON FORMAT

Return strictly a single valid JSON object:

```json
{
  "titleOptions": [
    "Why ...?",
    "How ...?",
    "What ...?",
    "Who ...?",
    "When ...?"
  ],
  "description": "YouTube SEO video description...",
  "keyword": "keyword 1, keyword 2, keyword 3...",
  "hashtags": [
    "#History",
    "#Documentary",
    "#WorldWarII"
  ]
}
```
