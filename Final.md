# Following the Grass

<p align="center"><img src="images/1805menufinal.jpg" width="840" alt="Following the Grass title screen"><br><em>A VR game about seasonal camp selection on the Mongolian grassland.</em></p>

**Jiayue Tao** · MSc Advanced Project · Unity / Blender · VR and PC

[The landscape](#reading-the-grassland)　·　[The journey](#leaving-home)　·　[The decision](#choosing-where-to-settle)　·　[Playtesting](#playtesting)

## Reading the grassland

On my visit to Mongolia, I looked at a river and saw a view. The herders I spoke with were looking for water and grazing for their animals. I built *Following the Grass* around that difference. The player has a family ger to move and one question to answer: **where would you settle before the weather changes?**

<p align="center"><img src="images/final_thesis_image27.jpeg" width="840" alt="River channel, camp and herd in spring"><br><em>Spring pasture. The herd draws the eye to water, while the ridge offers a second place to inspect.</em></p>

Research on Mongolian camp selection connected water access with pasture and seasonal movement (Ono and Ishikawa, 2020; Michler et al., 2022). I turned that relationship into a playable choice. Players inspect the land, record a promising site and commit to it before seeing the consequences.

<details>
<summary>Research figures and design references</summary>

| Research | Design choice | Player action |
|---|---|---|
| Ono and Ishikawa (2020), Michler et al. (2022) describe how pasture and water shape camp selection. | Give each site advantages and drawbacks. | Compare a convenient riverbank with drier ground. |
| Kolb (1984) and Johnson-Glenberg (2018) connect action with learning. | Show feedback after the decision. | Observe, mark a site, commit and review. |
| UNESCO (2013) and Tsovoodavaa et al. (2018) document ger construction. | Make the portable home the starting point. | Walk out of the dwelling you will later relocate. |

| Observation and decision cycle | Interior of the portable home |
|---|---|
| ![Observation and decision cycle](images/final_thesis_image2.png) | ![Interior of the portable home](images/final_thesis_image8.png) |

</details>

## Leaving home

The first view is from inside the ger. Furniture follows the curved wall, leaving the doorway clear. Outside, Bataa leads the player to the flock. The river becomes audible on approach. From the ridge, the player can look back and judge how far the animals would have to walk for water.

<p align="center"><img src="images/08yurtentry.png" width="840" alt="Spring ger doorway"><br><em>The home the player leaves in spring is the dwelling they will place at the end of the patrol.</em></p>

On horseback, the player can compare sites farther apart. Map pins preserve observations along the way. Back at camp, the player chooses a marked site and a ger type. The report explains what that combination means for the family and its animals.

<details>
<summary>Building the ger and opening menu</summary>

I modelled the circular wall, roof, central opening and door separately in Blender. I checked the roof poles from above and rotated the wall to align the blue band with the door frame. Four variations make each shelter recognisable during selection.

| Standard ger | Wind ropes | Rain cover | Winter covering |
|---|---|---|---|
| ![Standard ger](images/final_thesis_image3.png) | ![Wind ropes](images/final_thesis_image4.png) | ![Rain cover](images/final_thesis_image5.png) | ![Winter covering](images/final_thesis_image6.png) |

I moved the spring starting point indoors. Furniture follows the curved wall, leaving a clear path to the door. I adjusted the furniture shadows under the light entering through the roof.

| Furniture placement | Roof light |
|---|---|
| ![Furniture placement](images/08yurtfurniture.png) | ![Roof light](images/08rooflight.png) |

I developed the cover through several menu layouts. The chapter selector previews the weather before players enter the grassland.

| Early menu | Revised layout | Season selection |
|---|---|---|
| ![Early menu](images/1802menuv1.jpg) | ![Revised layout](images/1804menuv3.jpg) | ![Season selection](images/1808difficultyseasons.jpg) |

</details>

<details>
<summary>Grass, water and terrain studies</summary>

I used free assets for the environment and livestock alongside my ger, furniture and character models. GrassPhysics mesh patches let me cover the terrain with moving grass. I adjusted height and coverage independently, enlarged the map and shaped the river channel. With the skybox in place, I tuned the URP water material so the river reflected the sky.

| Grass setup | River material | Three candidate sites |
|---|---|---|
| ![Grass setup](images/07grassphysicssetup.png) | ![River material](images/08watermaterialtest.png) | ![Three candidate sites](images/09riverandthreesites.png) |

| Short grass at fixed density | Tall grass at the same density |
|---|---|
| ![Short grass at fixed density](images/final_thesis_image19.png) | ![Tall grass at the same density](images/final_thesis_image20.png) |

</details>

<details>
<summary>Bataa, map design and spatial sound</summary>

I wrote short prompts around what players can see. At the river, Bataa asks them to consider more than the distance to water. I aligned facial topology in Blender with the Unity lip-sync setup and tested the speaking and pointing animations. The closed mouth texture leaves little detail visible when the lips part.

| Character design | Facial topology and lip sync | Pointing animation |
|---|---|---|
| ![Character design](images/final_thesis_image11.png) | ![Facial topology and lip sync](images/final_thesis_image12.jpeg) | ![Pointing animation](images/final_thesis_image16.png) |

### Recording observations

I kept dialogue panels translucent so the landscape stays visible behind the subtitles. Map pins record where an observation was made. The field guide explains what to look for. At settlement, roof outlines help players recognise the four ger types from above.

| Map markers | Field guide | Ger selection icons |
|---|---|---|
| ![Map markers](images/final_thesis_image34.png) | ![Field guide](images/final_thesis_image35.png) | ![Ger selection icons](images/final_thesis_image39.png) |

### Finding places by ear

I built the soundscape around three listening positions. Wind and grass become clearer on leaving the ger. The river grows louder on approach, giving players a sense of distance even while they look towards the herd.

| Doorway | Meadow | Riverbank |
|---|---|---|
| ![Doorway](images/11gerthreshold.png) | ![Meadow](images/11meadow.png) | ![Riverbank](images/11riverbank.png) |

</details>

## Choosing where to settle

A spring riverbank offers water and grazing within a short walk. Summer rain makes the same low ground a flood risk. Autumn puts exposed ridges at a disadvantage. In winter a river can be close enough to see yet frozen at the nearest bank.

<p align="center"><img src="images/final_thesis_image45.jpeg" width="840" alt="Open water in the frozen river"><br><em>Winter changes the question from how close the river is to where the herd can actually drink.</em></p>

The campsite evaluator samples terrain and vegetation at the chosen point. It weighs those observations for the selected ger, then applies the rules for the season. A rain-resistant roof still needs dry ground beneath it. In winter, deep snow or a long walk to open water can limit the final score.

<p align="center"><img src="images/final_thesis_image47.png" width="840" alt="Two summer campsite reports"><br><em>Two summer sites, one ger type. The reports show how the choice of ground changes the result.</em></p>

<details>
<summary>Compare the four seasonal landscapes</summary>

| Spring | Summer rain | Autumn wind | Winter snow |
|---|---|---|---|
| Use the river and herd to learn the route. | Tall grass hides hollows. Inspect the ground for drainage. | Compare an exposed ridge with a sheltered route to water. | Trace the frozen channel to open water and check the remaining grazing. |

| Spring ridge | Summer basin | Autumn pasture | Winter open water |
|---|---|---|---|
| ![Spring ridge](images/09hillview.png) | ![Summer basin](images/1503lakeherd.jpg) | ![Autumn pasture](images/final_thesis_image43.png) | ![Winter open water](images/final_thesis_image45.jpeg) |

</details>

<details>
<summary>Inside the scoring system · weights, code and winter routing</summary>

**Map pin → terrain sample → environmental factors → seasonal rules → campsite report**

The evaluator samples the selected point on Unity Terrain. Height and density each contribute half the grass value. This lets a dense patch of short grass score differently from equally dense tall grass.

```csharp
float cGrass = 0.5f * Ramp(grassDensity, 5f, 14f)
             + 0.5f * Ramp(grassHeight, 0.12f, 0.50f);
```

| Ger type | Water | Flat ground | Dryness | Wind shelter | Grass |
|---|---:|---:|---:|---:|---:|
| Warm / standard | 30% | 25% | 20% | 10% | 15% |
| Wind-resistant | 20% | 20% | 15% | 35% | 10% |
| Rain-resistant | 15% | 25% | 40% | 5% | 15% |
| Cold / insulated | 30% | 15% | 15% | 15% | 25% |

These are my base weights for the game. Summer blends 40% of the house score with 60% seasonal factors, including flood risk and herd distance. Autumn emphasises shelter and the route to water. Winter uses a separate formula for accessible resources. [Scoring code and calibration](week14.md)

| Summer site comparison | Hollow and raised ground |
|---|---|
| ![Summer site comparison](images/final_thesis_image47.png) | ![Hollow and raised ground](images/final_thesis_image48.jpeg) |

### Winter changes the route to water

In winter the evaluator estimates a walking route across a two-metre grid. Steep cells block travel and snow raises the cost of crossing. If the estimated walk to water exceeds 90 metres, the code caps the normalised score at 0.45. A site beside a frozen bend can therefore score poorly even when the map shows water nearby.

</details>

## Playtesting

<p align="center"><img src="images/final_test_projection_01.png" width="840" alt="Player and projected riverside conversation with Bataa"><br><em>A headset session with the riverside conversation projected for observers.</em></p>

Five people tried the prototype. Three understood that they were choosing a new home for the family. One missed the goal and one record was inconclusive. Three reported discomfort, two found choosing a site difficult and two found the repeated journey monotonous. Some participants reported more than one difficulty.

My next revision would shorten the return ride and offer a more comfortable way to move. I would also ask each player to explain their choice before seeing the score, then give them unfamiliar terrain. That would show which clues they could use on their own.

<details>
<summary>View the headset sessions and projected gameplay</summary>

<table>
<tr>
<td><img src="images/final_test_vr_01.jpg" alt="Participant using the VR prototype" width="280"></td>
<td><img src="images/final_test_vr_02.jpg" alt="Participant wearing a headset during testing" width="280"></td>
<td><img src="images/final_test_vr_03.jpg" alt="Participant holding VR controllers" width="280"></td>
</tr>
<tr>
<td>Headset test session</td>
<td>Headset test session</td>
<td>Controller interaction</td>
</tr>
<tr>
<td><img src="images/final_test_vr_04.jpg" alt="Side view of a headset test" width="280"></td>
<td><img src="images/final_test_vr_05.jpg" alt="Participant using controllers in VR" width="280"></td>
<td></td>
</tr>
<tr>
<td>Headset test session</td>
<td>Controller interaction</td>
<td></td>
</tr>
</table>

### Projected gameplay sessions

<table>
<tr>
<td><img src="images/final_test_projection_01.png" alt="Projected game dialogue during a test session" width="480"></td>
<td><img src="images/final_test_projection_02.png" alt="Projected grassland and livestock during a test session" width="480"></td>
</tr>
<tr>
<td>Dialogue and the riverside scene</td>
<td>Grassland and livestock exploration</td>
</tr>
</table>

</details>

## Project materials

<details>
<summary>Production tools and weekly journal</summary>

| Component | What I built with it |
|---|---|
| Blender | Modelled the ger and furniture, checked the decorative bands around the curved wall, and prepared Bataa’s face for speech. |
| Unity Terrain and C# | Built the patrol route and sampled the ground beneath each proposed camp. |
| GrassPhysics and URP water | Set grass height and coverage independently and adjusted the reflection on the river. |
| LipSync Pro | Matched dialogue to mouth movement and checked the result alongside Bataa’s gestures. |
| VR headset and controllers | Let players turn towards sound, approach livestock and mark observations. The project also has a PC version. |

![Speech setup for Bataa](images/10lipsyncprosetup.png)

- [Week 01 — Initial Research and Reflection](week1.md)
- [Week 02 — Choosing a Direction](week2.md)
- [Week 03 — Research and Early Game Direction](week3.md)
- [Week 04 — Researching a Living Pasture](week4.md)
- [Week 05 — Four Seasons, One Decision](week5.md)
- [Week 06 — Modeling](week6.md)
- [Week 07 — Spring Grassland Blockout](week7.md)
- [Week 08 — Landscape Tuning and Ger Interior](week8.md)
- [Week 09 — Establishing the Grassland and Player Route](week9.md)
- [Week 10 — Dialogue UI, Subtitles and Lip Sync](week10.md)
- [Week 11 — Designing the Grassland Soundscape](week11.md)
- [Week 12 — Making the Whole Flow Work](week12.md)
- [Week 13 — UI Design Across the Game](week13.md)
- [Week 14 — The Campsite Score: Making the Core Decision Work](week14.md)
- [Week 15 — The Summer Chapter](week15.md)
- [Week 16 — Wind, Grass and Choosing an Autumn Camp](week16.md)
- [Week 17 — The Winter Chapter](week17.md)
- [Week 18 — Cover and Level Select Design](week18.md)
- [Week 19 — Four Seasons, Four Sets of Rules](week19.md)

</details>

<details>
<summary>References</summary>

Braun, V. and Clarke, V. (2006) ‘Using thematic analysis in psychology’, *Qualitative Research in Psychology*, 3(2), pp. 77–101. https://doi.org/10.1191/1478088706qp063oa

Johnson-Glenberg, M.C. (2018) ‘Immersive VR and education: embodied design principles that include gesture and hand controls’, *Frontiers in Robotics and AI*, 5, article 81. https://doi.org/10.3389/frobt.2018.00081

Jensen, L. and Konradsen, F. (2018) ‘A review of the use of virtual reality head-mounted displays in education and training’, *Education and Information Technologies*, 23, pp. 1515–1529. https://doi.org/10.1007/s10639-017-9676-0

Kolb, D.A. (1984) *Experiential learning: experience as the source of learning and development*. Englewood Cliffs, NJ: Prentice-Hall.

Michler, L.M. et al. (2022) ‘Moving toward the greener side: environmental aspects guiding pastoral mobility and impacting vegetation in the Dzungarian Gobi, Mongolia’, *Rangeland Ecology & Management*, 83, pp. 149–160. https://doi.org/10.1016/j.rama.2022.03.006

Ono, C. and Ishikawa, M. (2020) ‘Pastoralists’ herding strategies and camp selection in the local commons—a case study of pastoral societies in Mongolia’, *Land*, 9(12), article 496. https://doi.org/10.3390/land9120496

Tsovoodavaa, G., Shih, R.R.L., Bonjar, M.R.G. and Kistelegdi, I. (2018) ‘A review and systemization of the traditional Mongolian yurt (GER)’, *Pollack Periodica*, 13(3), pp. 19–30. https://doi.org/10.1556/606.2018.13.3.3

UNESCO (2013) *Traditional craftsmanship of the Mongol Ger and its associated customs*. https://ich.unesco.org/en/RL/00872

</details>

| Game build | Walkthrough | Presentation |
|---|---|---|
| | | |
