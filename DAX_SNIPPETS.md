# Select "Segments_Revenue_By_Year" in the Fields pane
## Basic totals

Total Revenue =
SUM('Segments_Revenue_By_Year'[Revenue_USD])

Total Revenue 2027 =
CALCULATE(
  SUM('Segments_Revenue_By_Year'[Revenue_USD]),
  'Segments_Revenue_By_Year'[Year] = 2027
)

Total Revenue 2025 =
CALCULATE(
  SUM('Segments_Revenue_By_Year'[Revenue_USD]),
  'Segments_Revenue_By_Year'[Year] = 2025
)

---

## Growth measures

CAGR 2021_2027 =
VAR S = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year]=2021)
VAR E = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year]=2027)
VAR n = 6
RETURN IF(S>0 && E>0, POWER(E/S,1/n)-1, BLANK())

YoY % =
VAR Prev = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), PREVIOUSYEAR('Calendar'[Date]))
VAR Curr = SUM('Segments_Revenue_By_Year'[Revenue_USD])
RETURN IF(Prev=0, BLANK(), DIVIDE(Curr-Prev, Prev))

---

## Forecast / projection

Forecasted Revenue =
VAR CurrentYear = SELECTEDVALUE('Calendar'[Year])
VAR StartVal = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year] = 2021)
VAR EndVal = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year] = 2027)
VAR CAGR = IF(StartVal>0, POWER(EndVal / StartVal, 1/6) - 1, BLANK())
RETURN
SWITCH(
  TRUE(),
  CurrentYear <= 2027,
    CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year] = CurrentYear),
  CurrentYear = 2028,
    EndVal * (1 + CAGR),
  BLANK()
)

Projected Revenue 2028 =
VAR EndVal = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year] = 2027)
VAR r = [CAGR 2021_2027]
RETURN IF(NOT(ISBLANK(EndVal)) && NOT(ISBLANK(r)), EndVal * (1 + r), BLANK())

Projected Revenue 2028 (by Segment) =
VAR seg = SELECTEDVALUE('Segments_Revenue_By_Year'[Segment])
VAR EndVal = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year]=2027, 'Segments_Revenue_By_Year'[Segment]=seg)
VAR StartVal = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year]=2021, 'Segments_Revenue_By_Year'[Segment]=seg)
VAR r = IF(StartVal>0, POWER(EndVal / StartVal, 1/6)-1, BLANK())
RETURN IF(NOT(ISBLANK(EndVal)) && NOT(ISBLANK(r)), EndVal * (1+r), BLANK())

---

## Market & Competition KPIs

Top3 Share % =
VAR seg = SELECTEDVALUE('Segments'[Segment])
VAR tbl = FILTER(ALL('Market_Shares_2021'), 'Market_Shares_2021'[Segment] = seg)
VAR top3 = TOPN(3, tbl, 'Market_Shares_2021'[Market_Share_pct], DESC)
VAR sumTop3 = SUMX(top3, 'Market_Shares_2021'[Market_Share_pct])
RETURN IF(sumTop3>0, sumTop3, BLANK())

Competition Index =
1 - DIVIDE([Top3 Share %], 100)   -- returns 0..1

---

## Investment / M&A KPIs

Number of Deals (2021-2023) =
CALCULATE(COUNTROWS('Investments_And_MA'), 'Investments_And_MA'[Year] >= 2021 && 'Investments_And_MA'[Year] <= 2023)

Total Deal Value (2021-2023) =
CALCULATE(SUM('Investments_And_MA'[Amount_USD]), 'Investments_And_MA'[Year] >= 2021 && 'Investments_And_MA'[Year] <= 2023)

Average Deal Size =
DIVIDE([Total Deal Value (2021-2023)], [Number of Deals (2021-2023)])

---

## Business Score (example rule-based InvestmentScore 0-100)

InvestmentScore =
VAR cagr = [CAGR 2021_2027]
VAR normCAGR = IF(ISBLANK(cagr),0, MIN(1, cagr))
VAR seg2025 = CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year]=2025, ALLEXCEPT('Segments_Revenue_By_Year', 'Segments_Revenue_By_Year'[Segment]))
VAR max2025 = CALCULATE(MAXX(VALUES('Segments_Revenue_By_Year'[Segment]), CALCULATE(SUM('Segments_Revenue_By_Year'[Revenue_USD]), 'Segments_Revenue_By_Year'[Year]=2025)), ALL('Segments_Revenue_By_Year'))
VAR normMarket = IF(max2025>0, seg2025 / max2025, 0)
VAR top3 = DIVIDE([Top3 Share %], 100)
VAR competitionIndex = 1 - top3
VAR recentFlag = IF(CALCULATE(COUNTROWS('Investments_And_MA'), 'Investments_And_MA'[Year] >= 2021 && 'Investments_And_MA'[Year] <= 2023) > 0, 1, 0)
VAR score = 100 * (0.40*normCAGR + 0.30*normMarket + 0.20*competitionIndex + 0.10*recentFlag)
RETURN ROUND(score,2)

---

# End of DAX_SNIPPETS.md
