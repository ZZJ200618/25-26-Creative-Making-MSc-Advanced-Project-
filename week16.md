# Week 16 — Wind, Grass and Choosing an Autumn Camp

After the rain and flood-balancing problem in [Week 15](week15.md), I focused on the autumn chapter, `Windynormal`, and how its weather changes the way the player reads the grassland. I wanted autumn to be more than a warmer colour filter. Wind now links three parts of the experience: it moves the grass, changes where the sheep gather, and affects the player's choice of a campsite.

## Wind that changes the landscape

The scene's `Autumn Wind (gusts)` component gives the wind a steady background strength with stronger squalls layered over it. In this scene the base is 0.45, a gust can add 0.5, and a squall lasts about nine seconds. A new squall is scheduled around every 25 seconds, with some variation, so the wind does not repeat at an exact rhythm. The direction is fixed along the positive X axis for this prototype.

The same changing wind value drives the Unity `WindZone` and the terrain grass animation. I tuned the autumn scene to use a restrained range of grass speed, strength and movement amount. The aim is for the field to respond visibly to a gust without making every blade move so strongly that the scene becomes distracting.

```csharp
if (td)
{
    td.wavingGrassSpeed = Mathf.Lerp(grassSpeed.x, grassSpeed.y, Strength);
    td.wavingGrassStrength = Mathf.Lerp(grassStrength.x, grassStrength.y, Strength);
    td.wavingGrassAmount = Mathf.Lerp(grassAmount.x, grassAmount.y, Strength);
}

if (zone)
{
    zone.windMain = Mathf.Lerp(0.3f, 1.4f, Strength);
    zone.windTurbulence = Mathf.Lerp(0.3f, 1.2f, Strength);
}
```

This makes the wind part of what the player can observe, rather than a background setting they cannot connect to the ground.

## Grass as autumn forage

I prepared a mixed autumn palette of straw, tawny and yellow-green grass textures, with a warm tint in the autumn grass profile. Keeping several tones helps the field feel like pasture at different stages, rather than one flat yellow surface. The wind changes how the grass moves, while its height and density still matter for the campsite report.

The score reads grass density and height; it does not treat colour as proof that a patch is good forage. That distinction is important because colour is a visual cue for the player, while the score uses the measurable terrain detail values already available in the scene. This connects the autumn appearance to the existing grass criterion without turning a seasonal tint into a false ecological measurement.

## The herd responds to stronger gusts

The autumn scene contains sheep, cattle and horses, but the wind response is currently attached to the sheep. When the wind stays above 0.78 for more than three seconds, each sheep is sent towards a nearby sheltered point. When it remains below 0.5 for eight seconds, the sheep return to their grazing positions.

The shelter search checks nearby terrain within a 70-metre radius. It favours lower wind exposure and rejects slopes above 12 degrees. The sheep then walk to their new home through the existing wandering behaviour; they do not teleport. This gives the player a visible clue that some parts of the field are more protected than others.

```csharp
if (!sheltering && strongFor > shelterAfter)
{
    sheltering = true;
    foreach (var w in sheep)
        if (w) w.SetHome(shelterHome[w], 2.2f);
}
else if (sheltering && calmFor > returnAfter)
{
    sheltering = false;
    foreach (var w in sheep)
        if (w) w.SetHome(grazeHome[w], 1f);
}
```

This is a designed signal, not a complete account of how all livestock respond to weather. The current script moves sheep only; cattle and horses remain part of the wider scene and route.

## Choosing a place to settle

The name `Windynormal` is detected by the settlement system and selects the autumn scoring profile. A campsite is judged through a combination of wind exposure, water access, drainage, grass and slope. The seasonal part of the score gives the greatest share to wind and water, while keeping grass, ground and drainage in the decision.

```csharp
score = 0.4f * score + 0.6f *
    (0.30f * cWind + 0.25f * cWater + 0.15f * cFlood
     + 0.15f * cGrass + 0.15f * cFlat);
```

The first 40% comes from the selected yurt type's general weights. The remaining 60% uses these autumn priorities: wind shelter 30%, water 25%, drainage 15%, grass 15% and flat ground 15%. The code also reduces the score for a long or indirect walk to water, caps a highly exposed site, and gives a very steep or river-close site a low ceiling. A sheltered place can still be a poor choice if it has little grass or water is difficult to reach.

At report level, wind exposure of 0.45 or less is marked good, above 0.45 up to 0.75 is fair, and above 0.75 is poor. If exposure rises above 0.85, the total site score is capped as well. This reflects the main design question for the player: where can the household balance remaining pasture, access to water and protection as the weather becomes less settled? Michler et al. (2023) found, in their study of herders in the Dzungarian Gobi, that season and available plant biomass helped explain how long camps were used. That supports keeping forage and seasonal change central to this chapter, while the exact percentages and wind thresholds remain my game-design choices. Zámolyi's comparative overview of camps across Inner Asia also describes water, pasture and protection from wind as parts of site selection, but does not provide a universal scoring formula (Zámolyi, 2025).

## Reflection

The main change this week is that the same autumn condition now appears in the scenery, animal movement and settlement decision. Gusts move the grass; sustained wind gathers sheep near sheltered ground; and the settle report rewards lower exposure while still asking the player to consider pasture, water and terrain together. The fixed wind direction and scripted sheep response keep the system readable, but they are simplifications. I will need player feedback to check whether these cues help someone understand the choice without making the animals look like a guaranteed answer. The detailed scoring logic is recorded in [Week 14](week14.md).

## References

Michler, L.M., Kaczensky, P., Oyunsaikhan, G., Bartzke, G.S., Devineau, O. and Treydte, A.C. (2023) ‘To move or not to move—factors influencing small-scale herder and livestock movements in the Dzungarian Gobi, Mongolia’, *Regional Environmental Change*, 23, article 131. Available at: https://doi.org/10.1007/s10113-023-02126-y.

Zámolyi, F. (2025) ‘Spatial concepts and architecture of nomadic camps among the steppe peoples of Inner Asia’, *Built Heritage*, 9, article 25. Available at: https://doi.org/10.1186/s43238-025-00191-2.
