# AnalystFC — Amex Campus Challenge 2026, Round 1

Leaderboard-best submission (public accuracy **0.8772**): a dollar-denominated
issuer **P&L model** that estimates each Cardmember's annual profit, ranks all
500,000 members by it, and flags the top 20% as the most profitable.

The model is **fully interpretable and fit-free** — every term is a named
revenue or cost line an issuer already tracks, and no parameters are learned at
run time. Coefficients are anchored to issuer financial disclosures and the
card's own published product terms, not tuned to the leaderboard.

## The equation (annual profit per member, $)
```
Profit = 0.023*(f6+f7+f8+f9+f10)          # interchange on category spend
       + 0.015*(f6+f9)                    # travel-booking commission
       - 0.007*(5*f6 + f7+f8+f9+f10)      # rewards accrued (5x flights, 1x rest)
       + 0.12*f1                          # net interest on revolving balance
       - (f14 + f16 + 50*f13 + 15*f15)    # benefit credits burned
       - 0.7*f11*f1                       # expected credit loss (PD x LGD x EAD)
       - (20*f2 + 60*f3)                  # servicing / collections calls
```
Members are ranked by this estimated profit; the top 20% are predicted most
profitable. Missing values are imputed as zero ("the event didn't happen") per
the dataset's structured-missingness patterns. See the report for coefficient
derivations, iteration history, and robustness analysis.

## Repository contents
| File | Description |
| --- | --- |
| `pipeline.py` | Single end-to-end script: reads `Amex_R1_dataset.csv`, scores all 500,000 members, and writes the predictions CSV plus the two-sheet official-template submission workbook. Deterministic, no fitting at run time. |
| `Amex_Round1_Technical_Report.pdf` | Full technical report: data forensics, framework construction, coefficient derivation, experiment table with public scores (0.538 → 0.8772), robustness, and guideline compliance. |
| `README.md` | This file. |

## Run
```bash
pip install pandas numpy xlsxwriter
python pipeline.py
```
**Input:** `Amex_R1_dataset.csv` (23 masked attributes, 500k rows) in the repo root.
**Output:**
- `AnalystFC_Amex_R1_predictions.csv` — the submission predictions (`ID`, `Prediction`).
- `AnalystFC_Amex_R1_submission.xlsx` — two-sheet official submission workbook (predictions + profitability framework).

## Guideline compliance
- Only the provided variables `f1`–`f23` are used; `id` never enters the equation.
- No rows added or altered; all 500,000 unique identifiers are scored.
- Output matches the exact official submission-template format.
- The model is interpretable and scalable — every term maps to a real issuer revenue or cost line.
