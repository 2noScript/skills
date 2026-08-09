---
name: seo
description: Generate YouTube SEO metadata package and 5 high-CTR thumbnail banner prompts in pure JSON format.
disable-model-invocation: true
---

# YOUTUBE SEO METADATA & THUMBNAIL BANNER GENERATION SYSTEM

This system synthesizes and generates an optimized YouTube SEO Metadata package along with 5 high-impact Thumbnail Banner Prompts across 5 distinct visual styles & layout structures based on complete documentary/story scripts.

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

### 1.5 Thumbnail Banner Prompts (5 High-CTR Visual Styles & Deep Scene Prompts)
Generate **5 Thumbnail Banner Image Prompts** based directly on the script's peak story climax, central characters, or key historical conflict. Each banner specifies:
* `style`: The visual aesthetic style (e.g. `"Paper Collage (VOX Editorial)"`, `"Cinematic Realism"`, `"Classical Oil Painting"`, `"Vintage Archival Map"`, `"Modern Minimalist Vector"`).
* `structure`: Framing & layout rules (e.g., `"Split-screen composition with character cutout on left and dark archival map on right with red focal highlight"`).
* `prompt`: **HIGH-CTR DEEP SCENE DESCRIPTION ONLY**.
  - **MANDATORY PROMPT LENGTH**: Must strictly be between **40 and 70 words** (approx. 250–450 characters) to ensure rich detail without missing key narrative elements.
  - Must explicitly capture 4 essential visual layers directly from the script input:
    1. **Primary Subject & Action**: Historical figure/commander, facial emotion, intense posture, outfit details.
    2. **Key Objects & Artifacts**: Weathered maps, red wax seals, secret telegrams, antique compass, dim lantern.
    3. **Atmospheric Environment**: Dark room background, rain on windows, candle smoke, dust particles, war bunker.
    4. **Color Palette & Lighting**: Charcoal shadows vs. glowing amber warm light and striking red accents.
  - **DO NOT include style words like 'paper collage', 'oil painting', or 'photorealistic' inside the `prompt` string itself** (keep visual styles exclusively in `style` and `structure`).

---

## MANDATORY OUTPUT JSON FORMAT

Return strictly a single valid JSON object:

```json
{
  "titles": [
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
  ],
  "banners": [
    {
      "style": "Paper Collage (VOX Editorial)",
      "structure": "Split-screen composition with character cutout on left and dark archival map on right",
      "prompt": "An intense mid-shot of a military commander leaning over a battle map filled with red arrows and classified documents, illuminated by a single overhead desk lamp in a dark wartime bunker with smoke rising."
    },
    {
      "style": "Cinematic Realism",
      "structure": "Close-up portrait with cinematic shallow depth of field and dramatic side lighting",
      "prompt": "A tense close-up portrait of a secret intelligence officer holding a sealed leather envelope stamped with a red wax seal, rain streaming down the glass window behind him in a dimly lit train cabin."
    },
    {
      "style": "Classical Oil Painting",
      "structure": "Centrally balanced triangular composition with dramatic chiaroscuro contrast",
      "prompt": "A regal monarch in golden armor standing before a burning city skyline at dusk, gripping an ancient sword with smoke rising into dark stormy clouds and glowing amber embers floating in the air."
    },
    {
      "style": "Vintage Archival Map",
      "structure": "Asymmetrical top-down flat lay with red compass circles and sepia photo overlays",
      "prompt": "An ancient yellowed parchment map of the Pacific Ocean surrounded by antique brass navigation dividers, a vintage pocket watch showing midnight, charred telegram receipts, and weathered leather journals."
    },
    {
      "style": "Modern Minimalist Vector",
      "structure": "High-impact silhouette graphic with bold diagonal color split and high contrast focus",
      "prompt": "A stark silhouette of a lonely soldier standing at the edge of a cliff pointing toward a giant glowing red circle and dark stormy sky horizon with intense high contrast lighting."
    }
  ]
}
```
