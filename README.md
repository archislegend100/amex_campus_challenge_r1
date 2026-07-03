# AnalystFC — Amex Campus Challenge 2026, Round 1

Retained leaderboard-best submission (public accuracy **0.8772**): a
dollar-denominated issuer P&L that estimates each Cardmember's annual profit
and ranks all 500,000 members, taking the top 20% as the most profitable.

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
Missing values are imputed as zero ("the event didn't happen") per the
dataset's structured-missingness patterns. Coefficients are anchored to
issuer financial disclosures and the card's own product terms — see the
report for derivations, iteration history, and robustness analysis.

## Files
- `pipeline.py` — single end-to-end script: reads `Amex_R1_dataset.csv`,
  scores all 500,000 members, writes the predictions CSV and the two-sheet
  official-template submission workbook. Deterministic, no fitting at run time.
- `report.tex` / `report.pdf` — full technical report: data forensics,
  framework construction, coefficient derivation, experiment table with
  public scores (0.538 → 0.8772), robustness, guideline compliance.
- `AnalystFC_Amex_R1_predictions.csv` — the submission predictions (ID, Prediction).

## Run
```
python pipeline.py        # requires pandas, numpy, xlsxwriter
```

## Guideline compliance
Only provided variables f1–f23 are used; `id` never enters the equation; no
rows added or altered; all 500,000 unique identifiers scored; exact official
submission template format; equation is interpretable and scalable (every
term is a named revenue or cost line an issuer already tracks).
