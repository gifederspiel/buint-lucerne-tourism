# Business questions — candidates

Pick 3. Each covers two analytics types. All answered on the merged Stage 3 dataset.

| # | Question (decision maker's phrasing) | Analytics |
|---|---|---|
| A | Since 2019 we added 12% beds but only 7% overnight stays, and bed occupancy is still 5.4 pts below the 2019 peak. How much of that shortfall is our own capacity expansion, and how much is a shift in guest mix and external conditions? | descriptive + diagnostic |
| B | The US is 26.9% of our overnight stays and with domestic guests 51.7%. How much of the post-2019 US growth is the CHF/USD rate rather than structural demand, and what do we lose over 12 months if the franc gains 10% against the dollar? | diagnostic + predictive |
| C | Growth since 2019 came almost only from September, October and December while January and February declined. Once weather variation is accounted for, which months and source markets show real underlying growth with unused capacity, and how should we prioritise them? | predictive + prescriptive |
| D | Asian markets are still 45-60% below 2019 while the US is 50% above it. Is this a pricing/FX effect or a lasting structural loss, and should we keep allocating capacity to Asian demand? | diagnostic + prescriptive |
| E | Average length of stay has been flat at 1.7-1.8 nights for 13 years. What keeps Lucerne a one-night stopover, and which guest segments could plausibly be moved to two nights? | descriptive + diagnostic |
| F | How reliably can next season's monthly overnight stays be predicted from weather and exchange rates, and how much capacity should we plan on that basis? | predictive + prescriptive |

**Recommended set: A → B → C.** They chain: A finds the problem, B quantifies the biggest threat, C decides what to do.

**Constraint:** HESTA has no price, revenue or marketing-spend variable, so no question may ask about revenue, profitability or ROI. Occupancy is the only yield proxy.

**Blocker:** B, D and F need the FX dataset (SNB `devkum`), not yet downloaded.
