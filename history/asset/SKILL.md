---
name: asset
description: Extract raw script drafts into an Asset Library (Characters, Living Entities, Collective Units, Objects, Props, Structures) with static visual descriptions incorporating 100% historical context, uniforms, and era details in pure JSON format.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY ASSET EXTRACTION SYSTEM (MASTERCLASS)

This system serves as the **FOUNDATION ASSET ENGINE**. It scans historical documentary scripts line-by-line to extract **100% of all characters, military units, weapons, vehicles, artifacts, and key historical props** into a structured JSON Asset Library with detailed physical `visual` descriptions.

---

## 1. 100% EXHAUSTIVE EXTRACTION RULE (ZERO-DROP POLICY)

- **EXHAUSTIVE SCRIPT SCANNING**: You MUST scan the ENTIRE script from start to finish. DO NOT skip any character, historical figure, soldier, military formation, vehicle, weapon, flag, document, or key setting prop mentioned or implied in the narrative.
- **IMPLICIT ENTITY EXTRACTION**: If the script mentions "the Roman legions advanced across the mud", you MUST extract both `Roman_Legionary_Soldier` (character unit) and `Roman_Gladius_Sword` or `Roman_Scutum_Shield` (props) if visually significant.
- **ALIAS RESOLUTION & DEDUPLICATION**: Consolidate different names referring to the same entity into a single canonical asset name (e.g., "General Zhukov", "Commander Zhukov", "Zhukov" -> `General_Georgy_Zhukov_1945`). Do NOT create duplicate assets for the same person/object.

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

### 2.2 Naming Convention (`name`)
- Use clean Snake_Case with Era/Year suffix: `[Name]_[Role/Faction]_[Year]` (e.g., `Swedish_Soldier_1709`, `M1_Garand_Rifle_1944`).
- Do NOT include square brackets inside `name` in JSON output. (Square brackets `[Asset_Name]` will be applied downstream by the Visual Prompt Skill).

---

## 3. HISTORICAL CONTEXT & `visual` PROMPT STANDARDS

Every asset `visual` MUST be written in **English (50–90 words)**, focusing exclusively on static physical attributes covering 5 mandatory historical layers:

1. **Era & Faction Timestamp**: Year, century, conflict, and nation/faction (e.g., *1709 Battle of Poltava, Swedish Army*).
2. **Headwear, Hair & Facial Attributes**: Helmet style, tricorn, topknot, beard, facial scars, eye color/expression.
3. **Uniforms & Clothing**: Specific coat color, lapels, buttons, belts, trousers, boots, weave, embroidery.
4. **Weapons & Equipped Items**: Material of stock, iron lock, blade steel, leather scabbard, brass fittings.
5. **Raw Material Textures & Wear**: Coarse wool, tarnished brass, weathered cowhide leather, mud/powder stains, hand-forged iron with light metallic scratches.

### STRICTLY FORBIDDEN IN `visual`:
- Do NOT include dynamic actions (e.g., "running", "firing", "screaming").
- Do NOT include atmospheric weather or lighting (e.g., "sunset", "rain", "fog", "dramatic light").
- Do NOT include camera/render buzzwords (e.g., "photorealistic", "4k", "close-up", "wide angle", "3D render").

---

## 4. VERIFICATION CHECKLIST BEFORE PRODUCING OUTPUT

Before outputting JSON, verify:
- [ ] Has EVERY character, soldier group, weapon, vehicle, banner, and key object in the script been extracted?
- [ ] Are aliases deduplicated into a single clear name?
- [ ] Is every `visual` written in English with all 5 historical layers included?
- [ ] Are all dynamic actions, camera angles, and render buzzwords completely removed?

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object containing `characters` and `props` arrays:

```json
{
  "characters": [
    {
      "name": "Swedish_Soldier_1709",
      "visual": "Swedish infantry soldier era 1709 (Battle of Poltava, Swedish Forces). Wearing a dark blue felt tricorn hat with white trim, sky blue coarse wool coat with signature yellow leather facings and brass buttons, cross-body brown leather belt with ammunition pouch, dark trousers tucked into black leather riding boots. Clean-shaved face with weathered skin. Holding a 1700s Flintlock musket with hand-forged iron lock and oak wood stock."
    }
  ],
  "props": [
    {
      "name": "Flintlock_Musket_1709",
      "visual": "Flintlock musket era 1709. Crude hand-forged iron lock mechanism with subtle metallic scratches, dark oak wood stock with visible grain, weathered brown cowhide shoulder sling with tarnished brass rivets and iron buckles."
    }
  ]
}
```
