# ✦ NNForge — 神经网络锻造炉

<p align="center">
  <a href="https://github.com/CJX0712/nn-forge/actions/workflows/ci.yml"><img src="https://github.com/CJX0712/nn-forge/actions/workflows/ci.yml/badge.svg" alt="ci"></a>
  <a href="https://github.com/CJX0712/nn-forge/releases"><img src="https://img.shields.io/github/v/release/CJX0712/nn-forge?sort=semver" alt="release"></a>
  <a href="https://github.com/CJX0712/nn-forge/blob/main/LICENSE"><img src="https://img.shields.io/github/license/CJX0712/nn-forge" alt="license"></a>
  <img src="https://img.shields.io/badge/author-%E6%99%A8%E6%98%9F-1f6feb" alt="author">
</p>

单文件离线神经网络：纯 JS 手写 MLP + 反向传播，浏览器内实时训练并在画布上渲染决策边界。零依赖，双击 `index.html` 即用。

## 功能

- **4 个数据集**：XOR / 双月牙 / 环形 / 双螺旋（种子化采样，可复现）
- **可调架构**：隐藏层 [8] / [8,8] / [16,16]，tanh 隐层 + sigmoid 输出 + BCE 损失
- **实时训练**：学习率滑块、暂停/继续、loss 曲线、训练精度
- **决策边界热区**：红→绿 = P(class 1) 0→1，散点着色

## 内置自检（8 项，引擎不变量）

| # | 不变量 |
|---|--------|
| 1 | **梯度检验**：解析反向传播 == 数值中心差分，maxRelErr < 1e-4（实测 ~9e-7） |
| 2 | XOR 收敛：4000 epoch 后 4 点全部分对（p>0.9 / p<0.1） |
| 3 | 种子确定性：同 seed 权重逐位一致，异 seed 不同 |
| 4 | BCE 性质：p==y → 0，−log0.5 = 0.693147 |
| 5 | 双月牙 800 epoch 精度 > 95%（实测 100%） |
| 6 | 双螺旋 1500 epoch 精度 > 90%（实测 99.3%） |
| 7 | 损失下降：600 epoch 后 < 初始 1/5 |
| 8 | 输出有界：200 随机输入 predict ∈ [0,1] |

## 无头验证

```bash
node _smoke.js   # 8/8 ALL GREEN
node _probe.js   # XOR 真值表 + ASCII 决策边界 dump
```

## 数学

- 前向：`z = Wx + b`，隐层 `a = tanh(z)`，输出 `p = sigmoid(z)`
- 反向传播：输出层 delta = `p − y`（BCE + sigmoid 融合），隐层 delta = `(Wᵀδ) ∘ (1 − a²)`（tanh 导数）
- 参数更新：全批量梯度下降，Xavier 缩放初始化（mulberry32 种子化）

MIT License
