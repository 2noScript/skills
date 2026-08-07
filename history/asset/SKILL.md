---
name: asset
description: Extract raw script drafts into an Asset Library (Characters, Living Entities, Collective Units, Objects, Props, Structures) with static visual descriptions incorporating 100% historical context, uniforms, and era details in pure JSON format.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY ASSET EXTRACTION SYSTEM (MASTERCLASS)

This system serves as the **FOUNDATION ASSET ENGINE**. It scans historical documentary scripts line-by-line to extract **100% of all characters, military units, weapons, vehicles, artifacts, and key historical props** into a structured JSON Asset Library with detailed physical `visual` descriptions.

---

## 1. 100% EXHAUSTIVE EXTRACTION & REPRODUCIBILITY RULES

- **2-PASS ZERO-DROP SCANNING**:
  - **Pass 1 (Explicit Scanning)**: Read the script from start to finish line-by-line. Extract EVERY named person, secondary figure, soldier group, weapon, vehicle, banner, document, or structure mentioned in narrative text. OMITTING ANY ENTITY IS STRICTLY FORBIDDEN.
  - **Pass 2 (Implicit & Gear Extraction)**: For every character or group extracted in Pass 1, extract their signature era weaponry or equipped items into `props` if visually significant (e.g. `Swedish_Soldier_1709` -> `Flintlock_Musket_1709`).
- **DETERMINISTIC CHRONOLOGICAL ORDERING**:
  - Order all extracted items in both `characters` and `props` arrays strictly by the **chronological order of their first appearance in the script**. This guarantees 100% identical, reproducible output across multiple executions on the same input script.
- **ALIAS RESOLUTION & DEDUPLICATION**:
  - Consolidate different names referring to the same entity into a single canonical asset name (e.g., "General Zhukov", "Commander Zhukov", "Zhukov" -> `General_Georgy_Zhukov_1945`). Do NOT create duplicate entries for the same entity.

---

## 2. ASSET CLASSIFICATION & NAMING STANDARDS

### 2.1 Asset Categories
1. **Living Entities (`characters`)**:
   - **Named Historical Figures**: Specific commanders, kings, leaders (e.g., `Charles_XII_1709`, `Napoleon_Bonaparte_1812`).
   - **Generic / Secondary Figures**: Individual soldiers, scouts, peasants, guards (e.g., `Swedish_Infantry_Soldier_1709`, `Ottoman_Janissary_Guard_1683`).
   - **Collective Units & Formations**: Squads, regiments, cavalry lines, war mounts (e.g., `Swedish_Cavalry_Squadron_1709`, `Cossack_Horsemen_1709`, `Warhorse_1709`).

2. **Inanimate Objects & Props (`props`)**:
   - **Personal Gear & Handheld Weapons**: Rifles, muskets, swords, shields, helmets, canteens (e.g., `Flintlock_Musket_1709`, `Steel_Broadsword_1709`).
   - **Heavy Equipment & Vehicles**: Cannons, tanks, warships, supply wagons, siege engines (e.g., `Carronade_Cannon_1709`, `Wooden_Supply_Wagon_1709`).
   - **Artifacts, Banners & Documents**: Maps, royal seals, letters, regimental flags, relics (e.g., `Swedish_Regimental_Banner_1709`, `Sealed_Royal_Decree_1709`).
   - **Key Visual Structures & Forts**: Specific redoubts, trench walls, fortress gates (e.g., `Poltava_Earthwork_Redoubt_1709`).

### 2.2 Deterministic Naming Formula (`name`)
- **DETERMINISTIC FORMULA**: Construct asset names strictly using the formula `[EntityName]_[RoleOrCategory]_[Year]` (e.g. `Swedish_Infantry_Soldier_1709`, `Flintlock_Musket_1709`, `M1_Garand_Rifle_1944`).
- **PASCAL_SNAKE_CASE ONLY**: Capitalize each word and separate with underscores (`_`). Never use spaces, hyphens, or special characters.
- **NO BRACKETS IN `name`**: Do NOT include square brackets inside the `name` field in the JSON output. (Square brackets `[Asset_Name]` will be applied downstream during Visual Prompt generation).

---

## 3. HISTORICAL CONTEXT & `visual` PROMPT STANDARDS

Every asset `visual` MUST be written as a highly detailed, comprehensive physical static description in **English (120–220 words)**, focusing exclusively on static physical attributes covering 5 mandatory historical layers:

1. **Era & Faction Timestamp**: Year, century, conflict, and nation/faction (e.g., *1709 Battle of Poltava, Swedish Army*).
2. **Headwear, Hair & Facial Attributes**: Helmet style, tricorn, topknot, hair color/style, beard/mustache, facial scars, eye expression, weathered skin texture.
3. **Uniforms & Clothing**: Specific coat color, collar, lapels, lining, brass/tarnished buttons, belts, trousers, footwear, stitching, weave, insignia, embroidery.
4. **Weapons & Equipped Items**: Specific era-accurate weaponry, material of stock, iron lock mechanism, blade steel, leather scabbard, ammunition pouch, brass fittings.
5. **Raw Material Textures & Wear**: Coarse hand-woven wool, tarnished brass, weathered cowhide leather, mud/powder stains, hand-forged iron armor with light metallic scratches, fabric fraying.

### STRICTLY FORBIDDEN IN `visual`:
- Do NOT include dynamic actions (e.g., "running", "firing", "screaming").
- Do NOT include atmospheric weather or lighting (e.g., "sunset", "rain", "fog", "dramatic light").
- Do NOT include camera/render buzzwords (e.g., "photorealistic", "4k", "close-up", "wide angle", "3D render").

---

## 4. VERIFICATION CHECKLIST BEFORE PRODUCING OUTPUT

Before outputting JSON, verify:
- [ ] Has EVERY character, soldier group, weapon, vehicle, banner, and key object in the script been extracted?
- [ ] Are aliases deduplicated into a single clear name?
- [ ] Is every `visual` string written in detailed English between **120 and 220 words** with all 5 historical layers included?
- [ ] Are all dynamic actions, camera angles, and render buzzwords completely removed?

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object containing `characters` and `props` arrays:

```json
{
  "characters": [
    {
      "name": "Swedish_Soldier_1709",
      "visual": "Swedish infantry soldier era 1709 during the Great Northern War at the Battle of Poltava, Swedish Forces. Wearing a dark blue felt tricorn hat with white wool trim and a small pewter cockade button, sky blue coarse hand-woven wool coat with signature Swedish yellow leather facings, broad lapels, and tarnished brass buttons. Cross-body brown cowhide leather belt with an iron-buckled ammunition pouch, dark blue trousers tucked into knee-high black leather riding boots with scuffed heels. Clean-shaved stern face with sun-weathered skin, faint gunpowder smudges near cheekbones, and light blue eyes. Holding a 1700s Flintlock musket with a hand-forged iron lock mechanism, metallic scratches on the barrel, and a dark polished oak wood stock."
    }
  ],
  "props": [
    {
      "name": "Flintlock_Musket_1709",
      "visual": "Flintlock musket era 1709 of the Swedish Royal Infantry. Featuring a crude hand-forged iron lock mechanism with subtle metallic scratches and light oxidation along the hammer assembly, a 44-inch dark oak wood stock with visible grain lines and a smooth hand-rubbed oil finish. Equipped with a weathered brown cowhide shoulder sling secured by tarnished brass rivets and iron buckles. The iron barrel exhibits authentic hand-hammered texture, light gunpowder residue near the flash pan, and a small brass front sight blade near the muzzle tip."
    }
  ]
}
```
