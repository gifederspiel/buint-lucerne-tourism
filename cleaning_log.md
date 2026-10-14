# Cleaning log

Every cleaning decision goes here. This log feeds the paper's **Data Quality** section.

One row per decision. `rows` = number of rows affected.

| Date | Dataset | Issue | Rows | Action | Reason |
|---|---|---|---|---|---|
| | | | | | |

## Raw file provenance
| File in `data/raw/` | Source | Cube | Exported | Original filename |
|---|---|---|---|---|
| `hesta_supply.csv` | BFS STAT-TAB (HESTA) | `px-x-1003020000_201` | 2026-10-14 | `px-x-1003020000_201_20261014-101604.csv` |
| `hesta_country.csv` | BFS STAT-TAB (HESTA) | `px-x-1003020000_101` | 2026-10-14 | `px-x-1003020000_101_20261014-101844.csv` |

Both exports: English labels, UTF-8, comma-separated, quoted text fields, 182 data rows,
2013–2026, commune `Luzern` only.

## Findings from first inspection (not yet acted on)
| Dataset | Issue | Rows | Planned action |
|---|---|---|---|
| both | `Month == "Total of the year"` annual totals mixed into monthly rows | 14 each | Drop before building `date` |
| both | Month names mostly English but **`Juli`** is German | 14 each | Month map must cover both spellings |
| both | Empty future months 2026-09 … 2026-12 filled with `...` | 4 each | Trim window to last complete month = **2026-08** |
| both | Missing-value placeholder is `...` | — | `na_values=["..."]` |
| `hesta_supply` | Annual-total rows carry decimals (`50.00`), monthly rows integers | 14 | Irrelevant once totals are dropped |
| `hesta_supply` | Extra column `Room nights` not in our agreed target schema | all | Keep or drop — group decision |
| `hesta_country` | Wide: 73 country labels × 2 indicators = 146 columns | all | `melt` → `date, commune, country, arrivals, overnight_stays` |
| `hesta_country` | Country spellings `Irland`, `Serbien` are German; `New Zealand, Oceania`, `Taiwan (Chinese Taipei)` contain commas/parens | all | Standardise to English before `country_iso2` mapping |
| `hesta_country` | 7 aggregate buckets (`Other European countries`, `Other African countries`, …) are not countries | all | No ISO2; treat as `Other` groups, do not double-count |
| `hesta_country` | `Visitors' country of residence - total` is a total column, not a country | all | Use as cross-check against `hesta_supply`, then exclude |

## Scope & limitations (must reach the paper — do not drop)

**HESTA covers hotel and spa establishments only.** No Airbnb / short-term rentals, no holiday
apartments, no campsites, no hostels (BFS records those separately, in PASTA). Everything we report
is *paid hotel demand* in the commune of Lucerne, not total visitor volume.

Goes in **two** places:

- **Section 3, Data source** — so the scope is set before any number is read:
  > HESTA is a mandatory monthly survey of hotel and spa establishments. It does not cover
  > short-term rentals (Airbnb), holiday apartments, campsites or hostels, which BFS records in a
  > separate statistic. All figures in this paper therefore describe paid hotel demand in the
  > commune of Lucerne.

- **Section 7, Conclusion** — as a limitation on the recommendations:
  > Because HESTA excludes short-term rentals, a shift of guests from hotels into that segment would
  > appear in our data as falling demand rather than as a change in channel. The occupancy decline
  > we report should be read as hotel occupancy, not as total visitor volume.

Why it matters: "beds grew faster than demand" and "guests moved to Airbnb" produce the **same curve**
in this dataset, and we cannot tell them apart. The occupancy finding has to carry this caveat.

Other scope facts worth one line each in Section 3:
- Guest origin is **country of residence**, not nationality.
- HESTA is a mandatory full survey, not a sample — no sampling error to report.
- The official series starts 2005; our window starts 2013 by group choice.

## Open group decisions
- Dataset owners — still TBD (one per dataset: `hesta_supply`, `hesta_country`, `weather`, `fx`).
- Comparison communes besides `Luzern` for `hesta_supply`.
- Time window end: latest month available in *all four* datasets.
- Threshold for grouping sparse countries into `Other` (`hesta_country`).
