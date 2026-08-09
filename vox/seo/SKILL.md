---
name: seo
description: Generate YouTube SEO metadata package and 5 high-CTR thumbnail banner prompts in pure JSON format.
disable-model-invocation: true
---

# VOX DOCUMENTARY SEO & THUMBNAIL BANNER GENERATION SYSTEM

This system synthesizes and generates an optimized YouTube SEO Metadata package along with 5 high-impact Thumbnail Banner Prompts across 5 distinct visual styles & layout structures based on complete documentary/story scripts.

---

## 1. METADATA GENERATION STANDARDS

### 1.1 Curiosity Question Titles (Multi-Title Question Hooks)
MANDATORY generation of **5 to 8 different title options** for user selection:
* Each title **MUST start with a Question Word** (e.g., `Why`, `How`, `What`, `Who`, `When`, `Tại sao`, `Làm thế nào`, `Ai`...) or documentary formula (`How [Event] Unfolded`, `What Really Happened to [Subject]`).
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
* `style`: The visual aesthetic style (e.g. `"VOX Editorial Paper Collage"`, `"Cinematic Realism"`, `"Classical Oil Painting"`, `"Vintage Archival Map"`, `"Modern Minimalist Vector"`).
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
    "How Did Dan Cooper Vanish Into Thin Air?",
    "Why Did the FBI Never Find Flight 305?",
    "What Really Happened to the $200,000 Ransom?",
    "Who Was the Man Behind the Alias Dan Cooper?",
    "When Will the Mystery of Flight 305 Finally Be Solved?"
  ],
  "description": "On November 24, 1971, a man claiming to be Dan Cooper hijacked Flight 305, extorted $200,000 in cash, and parachuted into the night. He was never seen again.",
  "keyword": "DB Cooper mystery, Flight 305 hijacking, FBI cold case, unsolved crime documentary",
  "hashtags": [
    "#TrueCrime",
    "#Documentary",
    "#DBCooper",
    "#ColdCase",
    "#History",
    "#Mysteries"
  ],
  "banners": [
    {
      "style": "VOX Editorial Paper Collage",
      "structure": "Split-screen composition with B&W halftone hero cutout on left and dark aged newsprint map on right",
      "prompt": "An intense mid-shot of a mystery man wearing a dark trenchcoat and dark sunglasses leaning over a wooden table spread with stacks of $20 bills and a black briefcase under dim warm desk lamp light."
    },
    {
      "style": "Cinematic Realism",
      "structure": "Close-up portrait with cinematic shallow depth of field and dramatic side lighting",
      "prompt": "A tense close-up of an FBI lead investigator staring at a weathered flight route map, rain streaming down the glass window behind him in a dimly lit headquarters room late at night."
    },
    {
      "style": "Classical Oil Painting",
      "structure": "Centrally balanced triangular composition with dramatic chiaroscuro contrast",
      "prompt": "A solitary airplane flying into dark stormy lightning clouds at dusk over dense pine forests, illuminated by dramatic golden light breaking through grey clouds with floating embers."
    },
    {
      "style": "Vintage Archival Map",
      "structure": "Asymmetrical top-down flat lay with red compass circles and sepia photo overlays",
      "prompt": "An ancient yellowed map of the Pacific Northwest terrain surrounded by antique brass navigation dividers, a vintage pocket watch showing midnight, charred ransom note receipts, and leather journals."
    },
    {
      "style": "Modern Minimalist Vector",
      "structure": "High-impact silhouette graphic with bold diagonal color split and high contrast focus",
      "prompt": "A stark silhouette of a parachutist falling through a giant glowing red moon circle against a pitch-black night sky and dark mountain ridge horizon."
    }
  ]
}
```
