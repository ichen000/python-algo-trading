# 📈 Python Algorithmic Trading Learning

> Learning Python for algorithmic trading, following "Python for Algorithmic Trading" by Yves Hilpisch.

---

## 📚 Learning Progress

| Week | Topic | Status |
|------|-------|--------|
| Week 1 | Python Environment & Basics | ✅ Complete |
| Week 2 | MTPR Indicator Development | 🔄 In Progress |
| Week 3-4 | Financial Data Analysis | ⏳ Planned |

---

## 🔧 MTPR Indicator (Multi-Timeframe Price Range)

A price range analysis indicator based on **Donchian Channel** concepts.

### Theoretical Foundation
- **Creator**: Richard Donchian (1950s)
- **Core Method**: Using N-period highest high and lowest low to establish price channels
- **References**: Corporate Finance Institute, Investopedia

### Implementation
Uses long-term (500 days), medium-term (250 days), and short-term (90 days) price ranges with EMA smoothing to analyze price position relative to historical ranges.

---

## 📁 Project Structure

```
python-algo-trading/
├── README.md           # This file
├── notebooks/          # Jupyter notebooks
│   └── mtpr_analysis.ipynb
└── notes/             # Learning notes
    └── dev_notes.md
```

---

## 🛠️ Tools Used

- Python 3
- Jupyter Notebook
- pandas, numpy
- yfinance
- matplotlib

---

## 📖 Book Reference

**"Python for Algorithmic Trading"** by Yves Hilpisch
- Part I: Python Environment (Ch 1-2)
- Part II: Financial Data Analysis (Ch 3-5)
- Part III: Backtesting (Ch 6-8)
- Part IV: Machine Learning (Ch 9-11)
- Part V: Live Trading (Ch 12-15)

---

*Started: January 2026*
