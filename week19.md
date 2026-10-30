# Week 19 — Four Seasons, Four Sets of Rules

This week I wrote down, in one place, how the four chapters differ and how the settle tool scores a camp in each of them. Each chapter is one season: Tutorial is spring, Easy is the rainy summer, Normal is the windy autumn and Hard is deep winter. All four use the same steppe layout, the same family, the same herd and the same tools, so the differences come only from the land and from the rules that judge it. Below, I compare the four chapters factor by factor, looking at grass, slope, animals, season and water, and show the code that turns each factor into a score.

## How a score is made

Every site the player chooses is turned into a number between 0 and 1, which the report shows out of 100. The settle tool first measures the site: the distance to water, the slope, how high it sits above the water, whether it is in a hollow, and how much grass grows around it. Each measurement is then turned into a value between 0 and 1 with a simple ramp function, and each value is graded as a circle, a triangle or a cross for the report.

```csharp
static float Ramp(float v, float a, float b) { return Mathf.Clamp01((v - a) / (b - a)); }
static int Grade(float c) { return c >= 0.66f ? 0 : c >= 0.33f ? 1 : 2; }
```

In spring, these values are combined using the weights of the chosen yurt type. Each yurt cares about different things. The warm yurt is the all-rounder, the windy yurt puts most of its weight on wind, the rainy yurt on dry ground, and the cold yurt on water and grass.

```csharp
float score = type.wWater * cWater + type.wFlat * cFlat + type.wDry * cDry + type.wWind * cWind + type.wGrass * cGrass;
```

| Yurt | Water | Flat | Dry | Wind | Grass |
|---|---|---|---|---|---|
| Warm | 0.30 | 0.25 | 0.20 | 0.10 | 0.15 |
| Windy | 0.20 | 0.20 | 0.15 | 0.35 | 0.10 |
| Rainy | 0.15 | 0.25 | 0.40 | 0.05 | 0.15 |
| Cold | 0.30 | 0.15 | 0.15 | 0.15 | 0.25 |

Summer and autumn keep this yurt score for 40 percent of the result and add 60 percent from their own seasonal rule, so the season matters more than the yurt. Winter replaces the formula completely with its own rules. The scene name decides which rules are used:

```csharp
profile = nl.Contains("rain") || n.Contains("夏季") || n.Contains("干旱") || nl.Contains("summer") ? Profile.Summer
        : nl.Contains("wind") || n.Contains("秋") || nl.Contains("autumn") ? Profile.Autumn : Profile.Tutorial;
```

```csharp
if (WinterSnowRules.Active) return EvaluateWinterSite(pos, type);
```

Finally, the raw score is compared with the best known site in that chapter, so that the best answer always reaches about 90 and everything else is scaled down from it. The power in this curve controls how quickly the score falls away from the best site. It is gentle in spring and steep in the later seasons, which is one of the main reasons the later chapters feel stricter.

```csharp
float target = summer || autumn ? 0.90f : 0.92f;
float k = summer ? 1.8f : autumn ? 1.9f : 1.2f;
float s = r.score >= rawBest
    ? Mathf.Min(0.94f, target + (r.score - rawBest) * 0.3f)
    : target * Mathf.Pow(r.score / rawBest, k);
```

## Grass

In spring, the grass is a mix of five layers, from very short ground cover of six to fourteen centimetres up to clumps of about eighty centimetres. The short grass near the river makes the ground easy to read, and the taller clumps show where the pasture is rich. In summer, all five layers are between half a metre and a metre tall and of the same green, so the meadow looks even and hides small dips in the ground. In autumn, the grass turns yellow-green, straw and tawny, and the tallest clumps reach about one point four metres, which makes the ripe pasture stand out as golden patches. In winter, the grass becomes short, dry and brown, between about ten and fifty centimetres, and much of it is under snow. Only the tufts that stick out above the snow can be grazed.

The settle tool reads the grass directly from the terrain. It looks at all grass within twelve metres of the site, and measures both how dense it is and how tall it is on average.

```csharp
int radius = Mathf.RoundToInt(12f * cellsPerMeterX);
float layerH = (protos[li].minHeight + protos[li].maxHeight) * 0.5f;
```

In spring, summer and autumn, density and height count equally. In winter, height no longer matters, because tall grass under snow is useless. Only the density of grass that is still visible counts, and the threshold is much lower.

```csharp
float cGrass = 0.5f * Ramp(grassDensity, 5f, 14f) + 0.5f * Ramp(grassHeight, 0.12f, 0.50f);
```

```csharp
float cForage = Ramp(r.grassDensity, 1.0f, 5.0f);
```

Grass has the smallest weight in summer, only 5 percent of the seasonal part, because summer grass is plentiful everywhere. In autumn, it rises to 15 percent, because the herd needs to fatten before winter. In winter, it is 14 percent, but the report changes its wording to "accessible grazing", with messages such as "dry grass showing above the snow" or "forage buried under snow".

## Slope and the shape of the ground

The four terrains have different shapes. Spring has the widest range, from just below the water up to hills of about sixteen metres, with a clear lookout crest where the player can see the whole valley. Summer is a lower, softer basin with a lake, where the important differences are small hollows of less than a metre. Autumn is the flattest of the four, with only about five metres between its lowest and highest points near the camp, so shelter comes from small rises and banks rather than big hills. Winter uses a similar layout to autumn, but the snow adds drifts and a steep snowy bank along one side of the river.

Flat ground is rewarded in every season. Slopes under two degrees score fully, and slopes over six degrees score nothing. Anything steeper than eight degrees is capped at 30, because a yurt cannot be pitched there.

```csharp
float cFlat = 1f - Ramp(slope, 2f, 6f);
if (r.slope > 8f) s = Mathf.Min(s, 0.3f);
```

What changes between seasons is how the shape of the ground is read. In spring, the tool checks whether the site is high enough above the water and not in a hollow, comparing it with the ground within ten metres. In summer, the comparison area is widened to fifteen metres, and being in a hollow becomes a flood risk with the largest weight of all. In winter, a hollow means cold air settling at night instead.

```csharp
float regional = RegionalHeight(pos, terrain, summer ? 15f : 10f);
float hollow = height - regional;
float cFlood = Ramp(hollow, -0.6f, 0.5f) * (0.7f + 0.3f * Ramp(elev, 1.4f, 2.4f));
```

```csharp
float cCold = 1f - Ramp(-r.hollow, 0.2f, 0.8f); if (cold) cCold = Mathf.Lerp(cCold, 1f, 0.3f);
```

In autumn and winter, the shape of the ground also decides shelter from the wind. The wind blows from the west, and the tool looks up to eighty metres upwind to see whether any higher ground blocks it. It also checks whether the site stands higher than its surroundings, which makes it more exposed.

```csharp
for (float d = 4f; d <= 80f; d += 4f)
{
    float hu = H(t, pos - WindDir * d);
    block = Mathf.Max(block, (hu - h0 - 0.5f) / d);
}
float shelter = Mathf.Clamp01((block - 0.01f) / 0.07f);
```

## Animals

All four chapters have the same herd of twelve sheep, six cattle, three horses and a goat, so the player always looks after the same family's animals. What changes is where they are and what they tell the player. In spring, the animals graze around the camp and the river and mainly bring life to the scene. In summer, they gather around the lake, where the water and the best grass are. In autumn, they spread across the golden pasture. In winter, the sheep stay on the windward slopes where the snow is thin, the cattle keep to the flatter ground, and every animal leaves tracks in the snow.

Summer is the only season where the herd is part of the score. The tool finds the nearest animal and gives full marks within twenty metres, falling to nothing at fifty-five metres.

```csharp
float distHerd = summer ? DistanceToHerd(pos) : 0f;
float cHerd = summer ? 1f - Ramp(distHerd, 20f, 55f) : 1f;
```

In winter, the herd is not scored directly, but it still acts as a clue. The places where sheep graze have visible grass, but they are also exposed to the wind, so the player has to read the animals together with the land rather than simply follow them.

## Season and weather

Each season has its own sky and weather, and each one introduces a new danger. Spring has a clear sky and no weather effects, so the player can learn the basics without distraction. Summer has an overcast sky, fog and constant rain that follows the player and stops inside the yurts. Its danger is flooding. Autumn has a sunset sky and a gust system that moves the grass from the west. Its danger is wind. Winter has falling snow and footprints, and the ground is covered by shallow snow and deep drifts. Its danger is snow depth, together with wind and frozen water.

These dangers are built into the score as caps, which are limits that no amount of good water or grass can overcome. Summer caps a site in a flooding hollow at 55. Autumn caps a fully exposed site at 55. Winter has the strictest set:

```csharp
if (snow > 0.45f) s = Mathf.Min(s, 0.40f);
if (r.exposure > 0.85f) s = Mathf.Min(s, 0.50f);
if (r.walkWater > 90f) s = Mathf.Min(s, 0.45f);
if (r.slope > 8f || r.distWater < 8f) s = Mathf.Min(s, 0.30f);
```

Snow depth itself is read from a snow depth map made for the winter terrain. Shallow, firm snow of about four to twenty-five centimetres scores best. Snow that has been scoured almost to the ground by the wind scores a little lower, and snow deeper than forty-five centimetres buries the yurt floor.

```csharp
float cSnow = (1f - Ramp(snow, 0.25f, 0.45f)) * (0.75f + 0.25f * Ramp(snow, 0.04f, 0.10f));
```

The seasons also decide which yurt type helps. In summer, the rainy yurt reduces the flood penalty. In autumn and winter, the windy yurt reduces the wind penalty. In winter, the cold yurt reduces the penalties for snow and cold hollows.

```csharp
if (summer && type.displayName == "Rainy") cFlood = Mathf.Lerp(cFlood, 1f, 0.3f);
if (type.displayName == "Windy") cShelter = Mathf.Lerp(cShelter, 1f, 0.3f);
if (cold) cSnow = Mathf.Lerp(cSnow, 1f, 0.25f);
```

## Water

Water is the factor that changes the most. In spring, there is one river close to the camp. A site within eight metres of the water is capped at 30, because it is too close to the bank. Between fifteen and sixty metres, the score is full, and it falls slowly after that.

```csharp
float cWater = distWater < 15f ? Ramp(distWater, 8f, 15f) : (distWater <= 60f ? 1f : 1f - 0.7f * Ramp(distWater, 60f, 120f));
if (r.distWater < 8f) s = Mathf.Min(s, 0.3f);
```

In summer, there is a river and a wider lake, and the best range narrows to twelve to thirty-five metres. Beyond forty-five metres, the score is capped more and more, down to 50.

```csharp
if (summer) cWater = distWater < 12f ? Ramp(distWater, 7f, 12f) : 1f - 0.8f * Ramp(distWater, 35f, 75f);
```

In autumn, the tool stops measuring a straight line and calculates the actual walking route to the water around banks and steep ground. If the walk is much longer than the straight distance, the score is reduced, and a walk over seventy metres starts to cap the result.

```csharp
walkWater = WalkToWater(pos, waterY, terrain, distWater);
cWater = walkWater < 12f ? Ramp(distWater, 7f, 12f) : 1f - 0.8f * Ramp(walkWater, 40f, 90f);
cWater *= 1f - 0.4f * Ramp(walkWater - distWater, 8f, 30f);
```

In winter, most of the river is frozen and only three short reaches stay open. The tool measures the distance to open water only, and the walking route cannot cross ice. Deep snow also makes each step more expensive, so a route through drifts counts as longer.

```csharp
if (WinterSnowRules.Active) inWater = inWater && WinterSnowRules.OpenWaterDistance(p) < WG;
winterBlocked[i,j] = WinterSnowRules.Active && h < waterY && !inWater;
winterCost[i,j] = WinterSnowRules.Active ? 1f + 2.5f * WinterSnowRules.Depth(p) : 1f;
```

## Summary

Read together, the four chapters form a sequence. Spring teaches the basic factors with gentle scoring and a clear landscape. Summer adds the herd and makes flooding the main concern. Autumn adds wind and real walking routes. Winter combines wind, walking routes and snow, hides water under ice and grass under snow, and uses the strictest caps. The table below sums up the seasonal part of each score.

| | Spring (Tutorial) | Summer (Easy) | Autumn (Normal) | Winter (Hard) |
|---|---|---|---|---|
| Grass | density + height | density + height, 5% | density + height, 15% | visible forage only, 14% |
| Slope and ground | flat, dry, not a hollow | hollows flood, 35% | flat, sheltered by rises | flat, cold hollows, 8% |
| Animals | scenery | distance to herd, 15% | scenery | clue only |
| Season | clear sky | rain, flood cap 55 | west wind 30%, cap 55 | wind 26%, snow 20%, several caps |
| Water | 15–60 m best | 12–35 m best, 30% | walking route, 25% | walk to open water, 22% |
| Score curve | gentle (1.2) | steep (1.8) | steep (1.9) | own curve (2.2) |
