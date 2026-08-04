---
name: asset
description: Extract raw script drafts into an Asset Library (Characters, Living Entities, Collective Units, Objects) with static visual descriptions incorporating 100% historical context, uniforms, and era details in pure JSON format.
disable-model-invocation: true
---

# HISTORICAL DOCUMENTARY ASSET EXTRACTION SYSTEM (MASTERCLASS)

This system extracts all visual assets from historical documentary scripts into structured JSON data with a detailed `visual` incorporating **100% historical context, era accuracy, military uniforms, gear, and realistic material textures**.

---

## 1. HISTORICAL CLASSIFICATION & VISUAL STANDARDS

### 1.1 Asset Classification
- **Living Entities (`characters`)**: Individuals, commanders, troop formations, military corps, war horses, livestock...
- **Inanimate Objects (`objects`)**: Weapons, tanks, warships, uniforms, fortifications, relics, flags...
- **Asset Naming Convention (`name`)**: Name assets cleanly without square brackets (e.g., `Swedish_Soldier_1709`, `US_GIs_1944`, `Bach_Dang_Warship_1288`). When embedded in `visual` by the Visual Prompt Skill, the bracketed format `[Asset_Name]` will be applied.

### 1.2 Standard for Integrating Historical Context into `visual`
In each `visual` field, MANDATORY physical static description covering 5 historical layers:
1. **Era & Faction**: Specify year/century timestamp and faction/nation (e.g., *Poltava battle era 1709, Swedish Forces*).
2. **Headwear & Hairstyle**: Describe exact hairstyle, hair knot, or helmet type (e.g., *Black felt Tricorn hat*, *M1 steel helmet*, *Tran Dynasty topknot*).
3. **Uniforms & Clothing**: Colors, lapels, buttons, belts (e.g., *Sky blue wool coat with yellow leather facings*, *Willow-leaf armor plate with dragon embroidery*).
4. **Weapons & Equipped Gear**: Exact era-accurate weaponry (e.g., *Flintlock musket with iron lock*, *Steel broadsword*, *M1 Garand rifle*).
5. **Raw Materials & Surface Textures**: Coarse woven wool, tarnished brass buttons, mud stains on coat hems, hand-forged iron armor with light metallic scratches.
- **STRICTLY FORBIDDEN**: Do NOT describe dynamic actions, emotions, weather, or camera angles.

---

## MANDATORY OUTPUT JSON FORMAT

Output strictly a single valid JSON object:

```json
{
  "characters": [
    {
      "name": "Swedish_Soldier_1709",
      "visual": "Swedish infantry soldier era 1709 (Battle of Poltava). Wearing a black felt tricorn hat, sky blue wool coat with signature Swedish yellow leather facings, brown leather belt with tarnished brass buckle ammunition pouch, black high-top leather riding boots. Holding a 1700s Flintlock musket with iron lock wrapped in oak wood stock."
    }
  ],
  "objects": [
    {
      "name": "Flintlock_Musket_1709",
      "visual": "Flintlock musket era 1709. Crude hand-forged iron lock mechanism with subtle metallic scratches, dark oak wood stock with visible grain, weathered brown cowhide shoulder sling with tarnished brass rivets."
    }
  ]
}
```
