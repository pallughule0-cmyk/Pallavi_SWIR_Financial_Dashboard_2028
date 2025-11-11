# SWIR Imaging — Market Analysis & Power BI Dashboard (2021–2028)

**Author:** <Pallavi>
**Role:** Business Analyst — Market & Financial Insights  
**Last updated:** 2025-12-11

---

## Project summary
This repository contains the data and Power BI dashboard used to analyze the Short-Wave Infrared (SWIR) imaging market. The analysis covers historical data (2021) through 2027 endpoints from an industry report, interpolates intermediate years, and projects 2028 using per-segment CAGR.

The dashboard combines:
- Financial time series and forecast (2021–2028)  
- Technology maturity & product specifications  
- Investment & M&A timeline  
- Segment and company market-share analysis

# Purpose: help product, strategy and investment teams decide where to allocate R&D and capital in the SWIR ecosystem.

---

# How to Build

1. Create relationships:
- `Segments_Revenue_By_Year[Year]` → `Calendar[Year]` (use Calendar table in model)  
- `Segments[Segment]` → `Segments_Revenue_By_Year[Segment]` (if you have a Segments lookup)
2. Paste DAX measures from `DAX_SNIPPETS.md` (if measures are not included in PBIX).

---

## Key insights (Executive summary)
- The SWIR market shows strong projected growth between 2021 and 2028. Using the report endpoints and CAGR interpolation, the market is forecasted to exceed **$1B+** by 2028 (segment-weighted).  
- Area Cameras dominate current revenue; 3D Sensing Modules and Modules/Assemblies show the fastest CAGR.  
- Technology readiness for Extended-SWIR advances quickly — productization is likely in 2025–2027.  
- Investment recommendation: prioritize pilot manufacturing/partnerships in Area Cameras, and R&D/productization pilots for Extended-SWIR sensors.


