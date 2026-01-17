# 📊 Python for Finance - 第一章筆記 (2026 最新版)

> **更新日期**: 2026/01/18  
> **Python 版本**: 3.12+  
> **主要變更**: yfinance MultiIndex 處理、Matplotlib 樣式更新

---

## 1️⃣ 蒙特卡洛模擬 - 純 Python 版本

```python
import time
import random
from math import exp, sqrt

start = time.time()
S0 = 100          # 初始股價
r = 0.05          # 無風險利率 (5%)
T = 1.0           # 時間週期 (1年)
sigma = 0.2       # 波動率 (20%)
values = []

for _ in range(1_000_000):  # 模擬100萬次 (使用底線增加可讀性)
    # 幾何布朗運動公式 (Black-Scholes 模型)
    # ST = S0 * e^((r - 0.5*σ²)*T + σ*Z*√T)
    ST = S0 * exp((r - 0.5 * sigma ** 2) * T +
                  sigma * random.gauss(0, 1) * sqrt(T))
    values.append(ST)

print(f"純 Python 耗時: {time.time() - start:.2f}s")
```

---

## 2️⃣ NumPy 向量化版本（快 7 倍以上）

```python
import time
import numpy as np

start = time.time()
S0 = 100
r = 0.05
T = 1.0
sigma = 0.2

# ⚡ 2026 最佳實踐：使用 np.random.default_rng()
rng = np.random.default_rng(seed=42)  # 可重現的隨機數生成器

ST = S0 * np.exp((r - 0.5 * sigma ** 2) * T +
                 sigma * rng.standard_normal(1_000_000) * np.sqrt(T))

print(f"NumPy 向量化耗時: {time.time() - start:.2f}s")
print(f"期望終值: {ST.mean():.2f}")  # 應接近 S0 * e^(rT) ≈ 105.13
```

> [!TIP]
> **為什麼 NumPy 更快？**
> - 避免 Python for 循環的開銷
> - 底層用 C/Fortran 實現向量化運算
> - 記憶體連續存取，CPU 快取利用率高

---

## 3️⃣ Matplotlib 設定（2026 版）

```python
%matplotlib inline

import matplotlib.pyplot as plt
from matplotlib import font_manager

# ⚠️ 2026 更新：seaborn 樣式名稱變更
# 舊版：plt.style.use('seaborn')
# 新版：plt.style.use('seaborn-v0_8') 或以下替代方案

plt.style.use('seaborn-v0_8-whitegrid')  # 推薦：帶白色網格

# 或使用純 matplotlib 現代樣式
# plt.style.use('bmh')  # 替代方案

# 圖片設定
plt.rcParams.update({
    'figure.dpi': 150,
    'savefig.dpi': 300,
    'font.family': 'sans-serif',
    'font.size': 11,
    'axes.titlesize': 14,
    'axes.labelsize': 12,
})

# 中文字體支援（macOS）
# plt.rcParams['font.sans-serif'] = ['PingFang TC', 'Heiti TC']
# plt.rcParams['axes.unicode_minus'] = False
```

> [!NOTE]
> **可用樣式列表**: `print(plt.style.available)`

---

## 4️⃣ yfinance 獲取 BTC 數據（2026 修正版）

```python
import yfinance as yf
import pandas as pd

# 下載 BTC/USD 歷史數據
df = yf.download('BTC-USD', start='2020-01-01', auto_adjust=True)

# ⚠️ 2026 關鍵修正：處理 MultiIndex 問題
if isinstance(df.columns, pd.MultiIndex):
    df = df.droplevel(1, axis=1)  # 移除第二層索引

# 計算 100 日簡單移動平均線 (SMA)
df['SMA_100'] = df['Close'].rolling(100).mean()

# 繪圖
fig, ax = plt.subplots(figsize=(12, 6))
df[['Close', 'SMA_100']].plot(ax=ax, linewidth=1.5)
ax.set_title('BTC/USD with 100-Day SMA', fontsize=14, fontweight='bold')
ax.set_xlabel('')
ax.set_ylabel('Price (USD)')
ax.legend(['Close', 'SMA 100'])
plt.tight_layout()
plt.show()
```

> [!IMPORTANT]
> **yfinance 2024+ 版本回傳 MultiIndex DataFrame**  
> 單一股票: `df.columns = MultiIndex([('Close', 'BTC-USD'), ...])`  
> 解決方案: `df.droplevel(1, axis=1)`

---

## 5️⃣ 完整工作範例：蒙特卡洛期權定價

```python
import numpy as np
import matplotlib.pyplot as plt

def monte_carlo_call_price(
    S0: float,      # 現價
    K: float,       # 履約價
    r: float,       # 無風險利率
    sigma: float,   # 波動率
    T: float,       # 到期時間（年）
    n_simulations: int = 100_000
) -> tuple[float, float]:
    """蒙特卡洛模擬計算歐式看漲期權價格"""
    
    rng = np.random.default_rng()
    
    # 模擬到期日股價
    ST = S0 * np.exp(
        (r - 0.5 * sigma ** 2) * T +
        sigma * np.sqrt(T) * rng.standard_normal(n_simulations)
    )
    
    # 期權收益
    payoffs = np.maximum(ST - K, 0)
    
    # 折現到現值
    call_price = np.exp(-r * T) * payoffs.mean()
    std_error = payoffs.std() / np.sqrt(n_simulations)
    
    return call_price, std_error

# 使用範例
price, se = monte_carlo_call_price(S0=100, K=105, r=0.05, sigma=0.2, T=1.0)
print(f"歐式看漲期權價格: ${price:.2f} ± ${se:.4f}")
```

---

## 📐 公式總結

### 幾何布朗運動 (GBM)

$$S_T = S_0 \cdot e^{\left(r - \frac{1}{2}\sigma^2\right)T + \sigma \sqrt{T} Z}$$

| 符號 | 含義 | 範例值 |
|:----:|------|--------|
| $S_0$ | 初始價格 | 100 |
| $S_T$ | 到期價格 | 模擬結果 |
| $r$ | 無風險利率 | 0.05 (5%) |
| $\sigma$ | 年化波動率 | 0.2 (20%) |
| $T$ | 時間（年） | 1.0 |
| $Z$ | 標準常態隨機數 | N(0,1) |

---

## 🆚 純 Python vs NumPy 效能對比

| 方式 | 100萬次模擬時間 | 相對速度 |
|------|----------------|---------|
| 純 Python | ~12-15 秒 | 1x |
| NumPy 向量化 | ~0.1-0.3 秒 | **50-100x** |

---

## 📦 2026 依賴版本

```bash
pip install numpy>=2.0 pandas>=2.2 matplotlib>=3.9 yfinance>=0.2.40
```

---

*Created: 2026/01/18 | Based on "Python for Finance" Ch.1*
