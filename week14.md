# Week 14 — The Campsite Score: Making the Core Decision Work

This week I spent most of my time studying and refining the campsite scoring code. The interface and controller scripts move the player through the steps of choosing a site. This script does the central job: it turns what the player finds in the landscape into a reasoned decision about where to live. Without it, the player could mark a place, but the game could not explain why it is more or less suitable.

The score connects the exploration, guide and settlement report from [Week 13](week13.md). It takes the conditions the player has learned to notice and checks them against the proposed camp. That makes the score the game's main feedback loop, rather than just a number on a panel.

## From landscape data to a score

The evaluator reads the active Unity Terrain at the chosen point. It samples slope and height, estimates the local hollow or rise, checks the nearby water and reads the terrain's grass detail. In summer it also checks how far the site is from the herd. In autumn and winter it considers shelter from wind and whether the route to water is practical.

Each measurement is converted to a value from zero to one. `Ramp` lets suitability change gradually across a range instead of switching abruptly between good and bad. `Grade` then turns each factor into the three categories shown in the report.

```csharp
static float Ramp(float v, float a, float b)
{
    return Mathf.Clamp01((v - a) / (b - a));
}

static int Grade(float c)
{
    return c >= 0.66f ? 0 : c >= 0.33f ? 1 : 2;
}
```

For example, the grass factor combines the measured density and height. A lush-looking but very short patch, or tall grass with little cover, does not automatically receive full marks.

```csharp
float cGrass = 0.5f * Ramp(grassDensity, 5f, 14f)
             + 0.5f * Ramp(grassHeight, 0.12f, 0.50f);
```

The standard score combines water access, flatness, drainage, wind shelter and grass using the selected yurt type's weights. Summer and autumn then blend that result with a seasonal set of priorities. In summer, the herd and flood risk matter more; in autumn, wind shelter and the route to water matter more.

```csharp
float score = type.wWater * cWater
            + type.wFlat * cFlat
            + type.wDry * cDry
            + type.wWind * cWind
            + type.wGrass * cGrass;

if (summer)
    score = 0.4f * score + 0.6f *
        (0.30f * cWater + 0.35f * cFlood + 0.15f * cFlat
         + 0.05f * cGrass + 0.15f * cHerd);

if (autumn)
    score = 0.4f * score + 0.6f *
        (0.30f * cWind + 0.25f * cWater + 0.15f * cFlood
         + 0.15f * cGrass + 0.15f * cFlat);
```

The weights and cut-offs are design values that I set for this prototype. The literature review in [Week 05](week5.md) supports looking at forage, water, terrain and weather exposure together, but it does not establish a universal 100-point campsite formula. The score is a transparent gameplay model, not a scientific prediction of where a real household should settle.

## Winter needs a different calculation

Winter has its own branch because a summer-style grass and water comparison would miss the main problem: whether the household can reach forage and usable water through snow and exposed terrain. The winter score combines shelter, water access, snow, forage, flatness and cold hollows.

```csharp
float raw = 0.26f * cShelter + 0.22f * cWater
          + 0.20f * cSnow + 0.14f * cForage
          + 0.10f * cFlat + 0.08f * cCold;

float s = 0.90f * Mathf.Pow(
    Mathf.Clamp01(raw / WinterRef), 2.2f);

if (snow > 0.45f) s = Mathf.Min(s, 0.40f);
if (r.exposure > 0.85f) s = Mathf.Min(s, 0.50f);
if (r.walkWater > 90f) s = Mathf.Min(s, 0.45f);
if (r.slope > 8f || r.distWater < 8f)
    s = Mathf.Min(s, 0.30f);
```

These caps mean that a serious problem, such as deep snow, an exposed site or a very steep slope, cannot be cancelled out just because another factor scores well. The water route is estimated on a two-metre terrain grid, with steep cells blocked and snow increasing the cost of crossing a cell. It is an approximate route estimate, not Unity NavMesh pathfinding or a model of real herding behaviour.

## Calibrating the number the player sees

The weighted result is not displayed as a literal percentage of scientific suitability. The code compares it with an authored reference site for that scenario, then maps it to a player-facing range. In the standard profile the reference is aimed near 92; summer and autumn use a target near 90. Serious constraints can lower the final result further.

```csharp
float target = summer || autumn ? 0.90f : 0.92f;
float k = summer ? 1.8f : autumn ? 1.9f : 1.2f;
float s = r.score >= rawBest
    ? Mathf.Min(0.94f, target + (r.score - rawBest) * 0.3f)
    : target * Mathf.Pow(r.score / rawBest, k);
```

The report then converts the result to a number out of 100 and uses the same three grades as the factor rows:

```csharp
scoreNumber.text = Mathf.RoundToInt(result.score * 100f).ToString();
scoreGrade.text = g == 0 ? "GOOD" : g == 1 ? "FAIR" : "POOR";
```

This calibration makes the score easier to read in the game, but it also means the number depends on the reference sites and thresholds I authored. It should be checked through playtesting and revised if players find that the report does not match what they observe in the landscape.

## Public code I compared with

I reviewed two public GitHub examples that use suitability scoring. I used them to compare the structure of the problem; I did not copy their source code, data or numeric weights.

| Public example | What I took from the approach | What I changed for this game |
|---|---|---|
| [Esri `VineyardAnalysis.py`](https://github.com/Esri/raster-functions/blob/master/functions/VineyardAnalysis.py) | It assesses terrain suitability by checking landscape criteria such as elevation, slope and aspect. This helped me frame the campsite as several observable conditions rather than one visual impression. | I use continuous 0–1 ramps instead of only threshold classes, and sample the live Unity terrain. I add pasture, water access, seasonal exposure and player-readable feedback. |
| [Solar site suitability `overlay.py`](https://github.com/PranavAchar01/solar-site-suitability/blob/main/src/site_suitability/overlay.py) and its [criteria configuration](https://github.com/PranavAchar01/solar-site-suitability/blob/main/config/criteria.yaml) | It separates criterion scoring, weighted combination and hard exclusions. I followed that general pattern so the player can receive both an overall result and individual reasons. | I adapted the pattern to a real-time Unity scene, yurt types and seasonal rules. The project adds grass detail, herd distance, snow and an estimated walking route, with hard score caps for risks such as steep ground or excessive exposure. |

The main improvement for this prototype is that the criteria feed back into the player's learning task: the same ideas appear in the guide, in the landscape and in the final site report. The weights and thresholds still need testing, and the route grid is deliberately simple enough for the prototype.

## References — Harvard style

Esri (n.d.) *VineyardAnalysis.py*. Available at: https://github.com/Esri/raster-functions/blob/master/functions/VineyardAnalysis.py (Accessed: 30 October 2026).

Jordan, G., Goenster, S., Munkhnasan, T., Shabier, A., Buerkert, A. and Schlecht, E. (2016) ‘Spatio-temporal patterns of herbage availability and livestock movements: A cross-border analysis in the Chinese-Mongolian Altay’, *Pastoralism*, 6, article 12. Available at: https://doi.org/10.1186/s13570-016-0060-2.

Munkhzul, O., Oyundelger, K., Narantuya, N., Tuvshintogtokh, I., Oyuntsetseg, B., Wesche, K. and Jäschke, Y. (2021) ‘Grazing effects on Mongolian steppe vegetation—A systematic review of local literature’, *Frontiers in Ecology and Evolution*, 9, article 703220. Available at: https://doi.org/10.3389/fevo.2021.703220.

PranavAchar01 (n.d.) *Solar site suitability: overlay.py and criteria.yaml*. Available at: https://github.com/PranavAchar01/solar-site-suitability (Accessed: 30 October 2026).
