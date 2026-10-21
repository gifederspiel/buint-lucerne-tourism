# Business questions (draft for group review)

Three questions, each using **two** of the four analytics types, deliberately **chained**:

> **Q1 finds the problem → Q2 quantifies the biggest threat to it → Q3 decides what to do about it.**

All three are answered on the merged Stage 3 dataset (`data/clean/stage3_lucerne_tourism.csv`).
Merged window: **2013-01 to 2026-08, 164 months** (HESTA ends 2026-08, weather 2026-09).

---

## Q1 — Why has occupancy not recovered although demand has?

> *"Since 2019 we have expanded capacity by 12% while overnight stays grew by only 7%, leaving bed
> occupancy 5.4 percentage points below the 2019 peak. What share of this occupancy shortfall is
> attributable to our own capacity expansion, and what share to shifts in guest-origin mix and in
> external demand conditions?"*

**Analytics: descriptive + diagnostic**

**Why it is not trivial.** Occupancy is an identity:
`bed_occupancy = overnight_stays / (beds x days_in_month)`.
So the change can be decomposed into a **capacity effect**, a **volume effect** and a **mix effect**
(shift-share across the 73 origin markets, which differ in length of stay and seasonal placement).
The residual is then regressed on weather anomalies, FX and `covid`.

**Variables.** `beds`, `overnight_stays`, `bed_occupancy_pct`, `room_occupancy_pct`,
`avg_length_of_stay` (supply); origin shares and `is_domestic` (country); `temp_anomaly_c`,
sunshine, `fx_yoy_pct`, `covid` as controls.

**Needs the merge:** the mix effect cannot be computed from either HESTA cube alone.

**Supporting evidence already in the raw data**

| | 2013 | 2019 | 2025 |
|---|---|---|---|
| Establishments | 50 | 53 | 57 |
| Beds | 5,610 | 5,864 | 6,584 |
| Overnight stays | 1.18M | 1.38M | 1.48M |
| Room occupancy | 67.6% | **75.2%** | 69.8% |
| Bed occupancy | 57.7% | 65.1% | 62.3% |

2019 is still the efficiency peak of the whole series. Bed occupancy in 2022-2025 is below the
2013-2019 average in **every month except December**. Average length of stay is flat at 1.7-1.8
nights across all 13 years - Lucerne is structurally a one-night stopover.

---

## Q2 — How exposed is our demand to the franc?

> *"The United States now accounts for 26.9% of overnight stays and, together with the domestic
> market, for 51.7%. How much of the post-2019 US growth is explained by the CHF/USD exchange rate
> rather than by structural demand, and what reduction in overnight stays should we expect over the
> following twelve months if the franc appreciates 10% against the US dollar?"*

**Analytics: diagnostic + predictive**

**Method.** Panel regression of log overnights per market on the log CHF rate of that market's
currency, with calendar-month fixed effects, `covid`, and weather anomalies as controls, giving a
**per-market FX elasticity**. Validate against the January 2015 shock (below), then run the 10%
scenario forward 12 months.

**Variables.** Full merge chain: `hesta_country` -> `country_currency_map` -> `fx`
(`chf_per_unit`, `fx_yoy_pct`).

**Natural experiment: the SNB floor removal, 15 January 2015** (CHF appreciated ~15% vs EUR overnight)

| Year | EUR-zone markets | YoY | US | YoY | All markets | YoY |
|---|---|---|---|---|---|---|
| 2014 | 179,803 | -3.4% | 176,776 | +6.1% | 1,218,169 | +3.6% |
| **2015** | **156,055** | **-13.2%** | 195,475 | +10.6% | 1,279,825 | **+5.1%** |
| 2016 | 174,682 | +11.9% | 203,082 | +3.9% | 1,269,774 | -0.8% |

EUR-zone overnights fell 13.2% while total demand still grew 5.1%. An exogenous, market-specific
FX shock inside our window - this is what makes the question diagnostic rather than correlational.

**Market concentration, 2025 overnight stays**

| Market | 2013 | 2019 | 2025 | share | vs 2019 |
|---|---|---|---|---|---|
| United States | 167k | 266k | **399k** | 26.9% | +50% |
| Switzerland | 313k | 317k | 368k | 24.8% | +16% |
| Germany | 99k | 84k | 81k | 5.5% | -3% |
| China | 90k | 125k | 64k | 4.3% | **-48%** |
| India | 39k | 60k | 50k | 3.4% | -17% |

US + Switzerland were 41% of overnights in 2013 and are 51.7% in 2025. Asia has not returned:
China -48%, Thailand -61%, Hong Kong -45%, Indonesia -49%, Russia -72% (all vs 2019).

---

## Q3 — Where should we direct capacity and market focus?

> *"Growth since 2019 has come almost entirely from September, October and December, while January
> and February have declined. Once month-to-month weather variation is accounted for, which calendar
> months and source markets show genuine underlying growth with unused capacity, and how should we
> prioritise them?"*

**Analytics: predictive + prescriptive**

**Method, two stages.**
1. *Predictive:* model monthly demand on weather anomalies to separate **weather luck** from
   **underlying trend**. A warm October is not a strategy.
2. *Prescriptive:* rank months by weather-adjusted trend x **capacity headroom** (gap between
   current bed occupancy and target), then match weather-robust counter-seasonal markets to the
   months that have headroom.

**Headroom is the binding constraint:** July/August run at 76-78% bed occupancy and have almost
nothing left to sell; January sits at ~30%.

**Seasonal shift, 2022-2025 vs 2013-2019 monthly averages**

| Up | | Flat | | Down | |
|---|---|---|---|---|---|
| December | +13% | July | +2% | August | -1% |
| September | +7% | June | +3% | March | -2% |
| October | +7% | May | +3% | January | -5% |
| November | +4% | April | +3% | February | -8% |

**Market seasonality (share of annual overnights in Jun-Aug, 2025):** US 40%, China 41%,
Taiwan 42%, UK 40% vs Switzerland 27%, Italy 26%, **Brazil 20%**. The shift toward the US
mechanically sharpens the summer peak - the markets worth courting for the empty months are the
counter-seasonal ones.

---

## Analytics coverage

| | Descriptive | Diagnostic | Predictive | Prescriptive |
|---|---|---|---|---|
| Q1 | X | X | | |
| Q2 | | X | X | |
| Q3 | | | X | X |

Two methods per question, all four types covered, each question consuming the previous one's output.

---

## Constraints to respect in the paper

**No price or revenue variable exists in HESTA** - no ADR, no RevPAR, no marketing spend. No
question may ask about revenue, profitability or marketing ROI. Q3 is therefore framed as
*prioritisation under capacity constraints*, not "return per marketing franc". Occupancy is the
only yield proxy available.

**Scope:** HESTA covers hotels and spas only - no Airbnb, holiday apartments, campsites or hostels.
See the scope section in `cleaning_log.md`; it must appear in Section 3 and Section 7.

## Variables to exclude (the brief requires justifying this)

Of 33 populated weather columns, keep **5**:

| Keep | Code | Why |
|---|---|---|
| Mean temperature | `tre200m0` | Core weather channel |
| Precipitation | `rre150m0` | Rain suppresses day visits |
| Sunshine duration | `sre000m0` | **In minutes - divide by 60** |
| Relative sunshine | `sremaxmv` | % of possible; controls for day length, proxies winter fog |
| Summer days | `tnd25xm0` | Threshold effect, not just the mean |

Drop: pressure (`pp0qffm0`, `pp0qnhm0`, `prestam0`), humidity (`ure200m0`), global radiation
(`gre000m0`, collinear with sunshine), evaporation (`erefaom0`), wind (`fkl010m0`, `fu3010m0`),
degree-days (`xno000m0`, `xcd000m0`, collinear with temperature), soil temperatures (empty since
2013). No plausible channel to hotel demand, and they would inflate the correlation matrix with noise.

From `hesta_country`, exclude the 7 `Other ... countries` aggregate buckets from per-market models
(they are not countries and have no ISO2 / currency) and the `Visitors' country of residence - total`
column (a total, used only as a cross-check).

## Open items

- [ ] **FX dataset not yet downloaded** (SNB `devkum`). Q2 is load-bearing and impossible without it.
- [ ] `weather.csv` is **LUZ only**; the project spec lists LUZ + PIL. None of these three questions
      needs Pilatus - propose dropping PIL and updating the dataset table rather than downloading it.
- [ ] Comparison communes: both HESTA exports contain `Luzern` only. Decide whether we need them.
- [ ] Assign one owner per question.
