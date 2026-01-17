# 📓 MTPR Development Notes

> **Date**: 2026/01/17
> **Progress**: MTPR basic calculation complete

---

## ✅ Completed

| Item | Status |
|------|--------|
| Jupyter environment setup | ✅ |
| yfinance stock data retrieval | ✅ |
| MTPR indicator Python implementation | ✅ |
| Single stock analysis | ✅ |

---

## 📍 Next Steps

```
Week 2: MTPR Enhancement
├── Add selling pressure calculation
├── Add volume contraction signal
├── Add horizontal resonance detection
└── Batch screening multiple stocks
```

---

## 🐛 Debugging Log

### Issue 1: `pip` not found
```
zsh: command not found: pip
```
**Solution**: Use `pip3` instead of `pip` on Mac

---

### Issue 2: `name 'tsmc' is not defined`
```
NameError: name 'tsmc' is not defined
```
**Cause**: Kernel reset or didn't run the cell that defines the variable
**Solution**: Re-run the cell with `tsmc = yf.download(...)`

---

### Issue 3: `unsupported format string`
```
TypeError: unsupported format string passed to Series.__format__
```
**Cause**: yfinance returns MultiIndex DataFrame
**Solution**:
```python
# Handle MultiIndex
if isinstance(df.columns, pd.MultiIndex):
    df = df.droplevel(1, axis=1)
# Or
float(value.iloc[-1])  # Convert to single value
```

---

### Issue 4: MTPR value is `nan`
```
MTPR latest value: nan
```
**Cause**: rolling(500) requires at least 500 data points
**Solution**: Download longer history with `period='5y'`

---

## 🔗 Reference: Pine Script → Python

| Pine Script | Python |
|-------------|--------|
| `ta.highest(high, 500)` | `df['High'].rolling(500).max()` |
| `ta.lowest(low, 500)` | `df['Low'].rolling(500).min()` |
| `ta.ema(x, 21)` | `x.ewm(span=21).mean()` |

---

*Created: 2026/01/17*
