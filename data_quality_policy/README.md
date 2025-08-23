# 📊 Data Quality Policy (M1→M5, Intraday EMM 5-Minute Strategy)   

This policy **places primary focus on 5–10-minute gaps** in the M1 feed. While other types of gaps and aggregate continuity heuristics may also affect results, for inclusion of a year in the core (“headline”) metrics we **deliberately select the frequency of 5–10-minute gaps as the single key quality factor**.  
All other signals are **published for transparency**, but they are **not gating criteria**.  

---

## 📈 Data Quality — 5–10-Minute Gaps by Year (M5 Backtest)  

**Rule:** 2001–2008 → ❌ **DROP**. For 2009+ → ✅ **OK** if `Gaps 5–10 min < 120`, else ❌ **DROP**.  
⚠️ **BORDERLINE** if `|cnt_5_10 − 120| ≤ 12`.  

| 📆 Year | ⏱️ Gaps 5–10 min | ⚡ All anomalies (any length) | 🏷️ Label | 📝 Note |
| --- | --- | --- | --- | --- |
| 2001 | 1350 | 1734 | ❌ DROP |  |
| 2002 | 1125 | 1608 | ❌ DROP |  |
| 2003 | 553 | 825 | ❌ DROP |  |
| 2004 | 336 | 567 | ❌ DROP |  |
| 2005 | 354 | 572 | ❌ DROP |  |
| 2006 | 600 | 714 | ❌ DROP |  |
| 2007 | 737 | 880 | ❌ DROP |  |
| 2008 | 164 | 180 | ❌ DROP |  |
| 2009 | 58 | 67 | ✅ OK |  |
| 2010 | 107 | 116 | ✅ OK |  |
| 2011 | 51 | 52 | ✅ OK |  |
| 2012 | 1 | 1 | ✅ OK |  |
| 2013 | 2 | 4 | ✅ OK |  |
| 2014 | 7 | 8 | ✅ OK |  |
| 2015 | 0 | 1 | ✅ OK |  |
| 2016 | 0 | 1 | ✅ OK |  |
| 2017 | 0 | 0 | ✅ OK |  |
| 2018 | 6 | 6 | ✅ OK |  |
| 2019 | 0 | 3 | ✅ OK |  |
| 2020 | 8 | 12 | ✅ OK |  |
| 2021 | 11 | 15 | ✅ OK |  |
| 2022 | 5 | 7 | ✅ OK |  |
| 2023 | 18 | 663 | ✅ OK |  |
| 2024 | 8 | 14 | ✅ OK |  |
| 2025 (Jan–end of July) | 1 | 32 | ✅ OK |  |

---

## 💡 1) Rationale (Why 5–10-Minute Gaps Are Prioritized)  
For the 5-minute indicator stack, **frequent 5–10-minute gaps** disrupt the construction of M5 bars and break entry/exit triggers.  
Because of this direct mechanical effect, the **annual number of 5–10-minute gaps** is the main determinant of whether a year is suitable for inclusion in headline metrics. Other anomalies are documented, but they **do not determine inclusion**.  

---

## 📜 2) Decision Rule  
- **2001–2008:** ❌ **DROP** unconditionally (systemic feed quality problems in early years; used only for stress tests).  
- **2009+:** a year is considered ✅ **OK** for headline if and only if the annual **number of anomalous 5–10-minute gaps** (`cnt_5_10`) is **< 120**. Otherwise → ❌ **DROP**.  
- **Borderline threshold:** ⚠️ a year is marked **BORDERLINE** if `|cnt_5_10 − 120| ≤ 12` (±10% tolerance). Borderline years still follow the main rule (`< 120` → OK; otherwise DROP).  

> ℹ️ Note: other gap categories and continuity metrics within this policy are **non-gating**; they are published solely for completeness and interpretability.  

---

## 📊 3) Current Classification  
- **HEADLINE (✅ OK):** 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018, 2019, 2020, 2021, 2022, 2023, 2024, 2025 (Jan–end of July)  
- **❌ DROP:** 2001, 2002, 2003, 2004, 2005, 2006, 2007, 2008  

📂 Detailed table:  
- [`data_quality_table.csv`](https://github.com/rleydev/euro-macromechanica-results/tree/main/data_quality_policy/data_quality_table.csv)

---

## 🧪 4) Stress Tests  
Years **2001–2008** remain **outside** headline metrics and are included **only** in stress tests to illustrate strategy behavior under degraded feed quality.  

---

## 🔎 See Detailed Data Analysis  
Repo: **euro-macromechanica-backtest-data**[`analysis/...`](https://github.com/rleydev/euro-macromechanica-backtest-data/tree/main/analysis)


Provenance: see data_quality_policy/README.sha256