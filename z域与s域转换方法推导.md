# z 域与 s 域转换方法推导

## 1 引言

在控制系统和数字信号处理中，连续时间系统用 **Laplace 变换（s 域）** 描述，离散时间系统用 **Z 变换（z 域）** 描述。将连续系统离散化，或者将离散系统映射回连续域，核心问题就是建立 **s 与 z 之间的映射关系**。

本文系统推导以下五种经典方法：

| 序号 | 方法 | 映射关系 |
|:---:|------|---------|
| 1 | 精确映射（脉冲不变法基础） | $z = e^{sT}$ |
| 2 | 前向差分（前向欧拉法） | $s = \dfrac{z - 1}{T}$ |
| 3 | 后向差分（后向欧拉法） | $s = \dfrac{z - 1}{Tz}$ |
| 4 | 双线性变换（Tustin / 梯形法） | $s = \dfrac{2}{T} \cdot \dfrac{z - 1}{z + 1}$ |
| 5 | 零阶保持器（ZOH）等效 | 直接作用于传递函数 |

其中 $T$ 为采样周期。

---

## 2 精确映射关系的推导

### 2.1 从采样定理出发

设连续信号 $x(t)$ 以周期 $T$ 采样，得到离散序列

$$x[n] = x(nT)$$

对连续信号做 Laplace 变换：

$$X(s) = \int_0^{\infty} x(t)\,e^{-st}\,dt$$

对离散序列做 Z 变换：

$$X(z) = \sum_{n=0}^{\infty} x[n]\,z^{-n}$$

### 2.2 理想采样与映射

理想采样信号可以写成

$$x_s(t) = \sum_{n=0}^{\infty} x(nT)\,\delta(t - nT)$$

对其做 Laplace 变换：

$$X_s(s) = \sum_{n=0}^{\infty} x(nT)\,e^{-snT}$$

令 $z = e^{sT}$，则 $e^{-snT} = z^{-n}$，于是

$$X_s(s)\Big|_{z=e^{sT}} = \sum_{n=0}^{\infty} x(nT)\,z^{-n} = X(z)$$

**因此，s 域与 z 域之间的精确映射关系为：**

$$\boxed{z = e^{sT}}$$

等价地：

$$s = \frac{1}{T}\ln z$$

### 2.3 映射的几何意义

令 $s = \sigma + j\omega$：

$$z = e^{(\sigma + j\omega)T} = e^{\sigma T}\,e^{j\omega T}$$

- $|z| = e^{\sigma T}$：s 平面左半平面（$\sigma < 0$）映射到 z 平面单位圆内（$|z| < 1$）
- $\angle z = \omega T$：s 平面虚轴上的频率 $\omega$ 映射到 z 平面单位圆上的角度
- s 平面虚轴（$\sigma = 0$）映射到 z 平面单位圆（$|z| = 1$）

**稳定性保持：** s 域左半平面 $\leftrightarrow$ z 域单位圆内。

---

## 3 前向差分法（前向欧拉法）

### 3.1 推导

对微分方程中的导数用前向差分近似：

$$\frac{dx(t)}{dt} \approx \frac{x((n+1)T) - x(nT)}{T} = \frac{x[n+1] - x[n]}{T}$$

在 s 域中，微分对应于乘以 $s$；在 z 域中，$x[n+1]$ 对应 $z \cdot X(z)$。因此：

$$s \cdot X(s) \longleftrightarrow \frac{z - 1}{T} \cdot X(z)$$

由此得到映射：

$$\boxed{s = \frac{z - 1}{T}}$$

反解：

$$z = 1 + sT$$

### 3.2 与精确映射的关系

对 $z = e^{sT}$ 做 Taylor 展开（取一阶）：

$$z = e^{sT} \approx 1 + sT$$

这正好就是前向差分法的映射，说明前向差分法是精确映射的**一阶近似**。

### 3.3 稳定性分析

s 域稳定区域为左半平面 $\text{Re}(s) < 0$。代入 $z = 1 + sT$：

令 $s = \sigma + j\omega$，则

$$z = (1 + \sigma T) + j\omega T$$

稳定条件 $|z| < 1$ 要求：

$$(1 + \sigma T)^2 + (\omega T)^2 < 1$$

这是 z 平面上以 $(-1, 0)$... 不，展开可得圆心在 $(1, 0)$、半径为 $1/T$ 的区域映射关系。具体地，s 平面的左半平面映射到 z 平面中以 $(1, 0)$ 为圆心、$1$ 为半径（当 $T$ 归一化后）的圆盘。

**关键问题：** s 平面左半平面映射后的稳定区域**不完全在** z 平面单位圆内，因此前向差分法**不保证稳定性映射**，某些 s 域稳定的系统离散化后可能变得不稳定。

---

## 4 后向差分法（后向欧拉法）

### 4.1 推导

用后向差分近似导数：

$$\frac{dx(t)}{dt} \approx \frac{x(nT) - x((n-1)T)}{T} = \frac{x[n] - x[n-1]}{T}$$

在 z 域中，$x[n-1]$ 对应 $z^{-1} X(z)$，因此：

$$s \cdot X(s) \longleftrightarrow \frac{1 - z^{-1}}{T} \cdot X(z) = \frac{z - 1}{Tz} \cdot X(z)$$

映射关系：

$$\boxed{s = \frac{z - 1}{Tz}}$$

反解：

$$z = \frac{1}{1 - sT}$$

### 4.2 与精确映射的关系

对 $z = e^{sT}$ 取倒数的一阶展开：

$$z^{-1} = e^{-sT} \approx 1 - sT$$

$$z \approx \frac{1}{1 - sT}$$

这正是后向差分法，说明它是对 $e^{-sT}$ 做一阶 Taylor 近似后取倒数得到的结果。

### 4.3 稳定性分析

令 $s = \sigma + j\omega$（$\sigma < 0$），则：

$$z = \frac{1}{1 - (\sigma + j\omega)T} = \frac{1}{(1 - \sigma T) - j\omega T}$$

$$|z| = \frac{1}{\sqrt{(1 - \sigma T)^2 + (\omega T)^2}}$$

当 $\sigma < 0$ 时，$1 - \sigma T > 1$，故 $(1 - \sigma T)^2 + (\omega T)^2 > 1$，从而 $|z| < 1$。

**结论：** 后向差分法**保证稳定性映射** —— s 域左半平面完全映射到 z 域单位圆内。

---

## 5 双线性变换（Tustin 变换 / 梯形法）

### 5.1 推导思路一：梯形积分

对微分方程 $\dot{y} = f(t)$ 两端积分，用梯形公式近似：

$$y(nT) - y((n-1)T) = \int_{(n-1)T}^{nT} f(\tau)\,d\tau \approx \frac{T}{2}\big[f(nT) + f((n-1)T)\big]$$

在 z 域中，积分算子 $1/s$ 对应：

$$\frac{1}{s} \longleftrightarrow \frac{T}{2} \cdot \frac{z + 1}{z - 1}$$

$$\therefore\quad \frac{T}{2} \cdot \frac{z + 1}{z - 1} = \frac{1}{s}$$

反解得：

$$\boxed{s = \frac{2}{T} \cdot \frac{z - 1}{z + 1}}$$

### 5.2 推导思路二：Padé 近似

从精确映射 $z = e^{sT}$ 出发：

$$z = e^{sT} = \frac{e^{sT/2}}{e^{-sT/2}}$$

对分子分母分别做一阶 Taylor 展开：

$$e^{sT/2} \approx 1 + \frac{sT}{2}, \qquad e^{-sT/2} \approx 1 - \frac{sT}{2}$$

$$z \approx \frac{1 + sT/2}{1 - sT/2}$$

反解 $s$：

$$z(1 - sT/2) = 1 + sT/2$$

$$z - zsT/2 = 1 + sT/2$$

$$z - 1 = s\left(\frac{T}{2}z + \frac{T}{2}\right) = \frac{sT}{2}(z + 1)$$

$$\boxed{s = \frac{2}{T} \cdot \frac{z - 1}{z + 1}}$$

两种推导殊途同归，得到完全相同的结果。

### 5.3 反映射

$$z = \frac{1 + sT/2}{1 - sT/2}$$

这就是一阶 **Padé 近似**（[1/1] Padé approximant of $e^{sT}$）。

### 5.4 频率畸变（Frequency Warping）

令 $s = j\omega_a$（模拟频率），$z = e^{j\omega_d T}$（数字频率），代入双线性变换：

$$j\omega_a = \frac{2}{T} \cdot \frac{e^{j\omega_d T} - 1}{e^{j\omega_d T} + 1}$$

$$= \frac{2}{T} \cdot \frac{e^{j\omega_d T/2}(e^{j\omega_d T/2} - e^{-j\omega_d T/2})}{e^{j\omega_d T/2}(e^{j\omega_d T/2} + e^{-j\omega_d T/2})}$$

$$= \frac{2}{T} \cdot \frac{2j\sin(\omega_d T/2)}{2\cos(\omega_d T/2)}$$

$$= j\frac{2}{T}\tan\left(\frac{\omega_d T}{2}\right)$$

因此模拟频率与数字频率之间的关系为：

$$\boxed{\omega_a = \frac{2}{T}\tan\left(\frac{\omega_d T}{2}\right)}$$

**频率畸变**：模拟频率与数字频率之间存在非线性的正切关系。当 $\omega_d T \ll 1$ 时，$\tan(\omega_d T/2) \approx \omega_d T/2$，故 $\omega_a \approx \omega_d$，低频段近似线性。随着频率升高畸变加大。

**预畸变补偿**：在设计数字滤波器时，先将关键频率点 $\omega_d$ 反算为 $\omega_a$，再在 s 域设计，最后用双线性变换离散化，即可保证关键频率点精确对应。

### 5.5 稳定性分析

令 $s = \sigma + j\omega$，代入 $z = \dfrac{1 + sT/2}{1 - sT/2}$：

$$|z|^2 = \frac{|1 + sT/2|^2}{|1 - sT/2|^2} = \frac{(1 + \sigma T/2)^2 + (\omega T/2)^2}{(1 - \sigma T/2)^2 + (\omega T/2)^2}$$

当 $\sigma < 0$ 时，$1 + \sigma T/2 < 1 - \sigma T/2$（因 $\sigma T/2 < 0$），故分子 < 分母，$|z| < 1$。

当 $\sigma = 0$ 时，分子 = 分母，$|z| = 1$。

当 $\sigma > 0$ 时，$|z| > 1$。

**结论：** 双线性变换**严格保持稳定性映射**：
- s 左半平面 $\leftrightarrow$ z 单位圆内
- s 虚轴 $\leftrightarrow$ z 单位圆上
- s 右半平面 $\leftrightarrow$ z 单位圆外

---

## 6 零阶保持器（ZOH）等效法

### 6.1 原理

零阶保持器在每个采样时刻将信号值保持一个采样周期：

$$h_0(t) = \begin{cases} 1, & 0 \le t < T \\ 0, & \text{otherwise} \end{cases}$$

其 Laplace 变换为：

$$H_0(s) = \frac{1 - e^{-sT}}{s}$$

### 6.2 推导

设连续系统传递函数为 $G(s)$，经 ZOH 后的等效离散传递函数为 $G(z)$。

DAC（ZOH）+ 连续系统 + 采样的级联：

$$G(z) = \mathcal{Z}\left\{\frac{1 - e^{-sT}}{s} \cdot G(s)\right\}$$

由于 $1 - e^{-sT}$ 在 Z 变换中对应 $1 - z^{-1}$，所以：

$$\boxed{G(z) = (1 - z^{-1})\,\mathcal{Z}\left\{\frac{G(s)}{s}\right\}}$$

### 6.3 计算步骤

1. 计算 $\dfrac{G(s)}{s}$
2. 对其做部分分式展开
3. 查 Z 变换表，得到 $\mathcal{Z}\left\{\dfrac{G(s)}{s}\right\}$
4. 乘以 $(1 - z^{-1})$

### 6.4 示例

设 $G(s) = \dfrac{a}{s + a}$，求 ZOH 等效 $G(z)$。

**步骤 1：**

$$\frac{G(s)}{s} = \frac{a}{s(s + a)} = \frac{1}{s} - \frac{1}{s + a}$$

**步骤 2：** 查表

$$\mathcal{Z}\left\{\frac{1}{s}\right\} = \frac{z}{z - 1} = \frac{1}{1 - z^{-1}}$$

$$\mathcal{Z}\left\{\frac{1}{s + a}\right\} = \frac{z}{z - e^{-aT}} = \frac{1}{1 - e^{-aT}z^{-1}}$$

**步骤 3：**

$$\mathcal{Z}\left\{\frac{G(s)}{s}\right\} = \frac{1}{1 - z^{-1}} - \frac{1}{1 - e^{-aT}z^{-1}}$$

**步骤 4：**

$$G(z) = (1 - z^{-1})\left(\frac{1}{1 - z^{-1}} - \frac{1}{1 - e^{-aT}z^{-1}}\right)$$

$$= 1 - \frac{1 - z^{-1}}{1 - e^{-aT}z^{-1}}$$

$$= \frac{(1 - e^{-aT}z^{-1}) - (1 - z^{-1})}{1 - e^{-aT}z^{-1}}$$

$$= \frac{(1 - e^{-aT})z^{-1}}{1 - e^{-aT}z^{-1}}$$

$$\boxed{G(z) = \frac{1 - e^{-aT}}{z - e^{-aT}}}$$

### 6.5 特点

- ZOH 法精确地反映了实际 D/A + 连续系统 + A/D 的物理过程
- 对阶跃响应的离散化是**精确**的（采样点处值完全一致）
- 不存在像双线性变换那样的全局代数 $s \to z$ 映射公式，必须针对具体 $G(s)$ 逐一计算

---

## 7 五种方法对比总结

| 方法 | s → z 映射 | 稳定性保持 | 精度 | 频率特性 |
|------|-----------|:----------:|------|---------|
| 精确映射 | $z = e^{sT}$ | 严格保持 | 精确 | 精确 |
| 前向差分 | $z = 1 + sT$ | **不保证** | 一阶 | 低频近似 |
| 后向差分 | $z = \frac{1}{1 - sT}$ | 保持 | 一阶 | 低频近似 |
| 双线性变换 | $z = \frac{1 + sT/2}{1 - sT/2}$ | 严格保持 | 二阶 | 有频率畸变，可预畸变补偿 |
| ZOH 等效 | 无封闭映射 | 保持 | 阶跃精确 | 引入半个采样周期延迟 |

### 精度阶数说明

三种代数映射法都是对 $z = e^{sT}$ 的不同阶近似：

$$e^{sT} = 1 + sT + \frac{(sT)^2}{2!} + \cdots$$

- **前向差分**（一阶）：$z \approx 1 + sT$
- **后向差分**（一阶）：$z \approx \frac{1}{1 - sT}$（$e^{-sT}$ 的一阶取倒数）
- **双线性变换**（二阶）：$z \approx \frac{1 + sT/2}{1 - sT/2}$（[1/1] Padé 近似）

---

## 8 应用场景建议

1. **数字滤波器设计**：优先选择**双线性变换**，配合预畸变可精确映射关键频率。

2. **数字控制器实现**：
   - 对精度要求不高的场合，**后向差分法**简单且保证稳定性。
   - 需要精确匹配阶跃响应时，选择 **ZOH 等效法**。
   - **双线性变换**在 PID 控制器离散化中最为常用。

3. **数字仿真**：根据仿真步长和精度需求选择合适的方法。步长较大时优先双线性变换或 ZOH。

4. **电机驱动控制（如 PMSM 参数辨识）**：通常采用**后向差分**或**双线性变换**对电流环 PI 控制器进行离散化，兼顾实现简便与系统稳定性。

---

## 参考文献

1. Oppenheim A V, Schafer R W. *Discrete-Time Signal Processing*. 3rd ed. Pearson, 2010.
2. Ogata K. *Discrete-Time Control Systems*. 2nd ed. Prentice Hall, 1995.
3. Franklin G F, Powell J D, Emami-Naeini A. *Feedback Control of Dynamic Systems*. 8th ed. Pearson, 2019.
4. 胡寿松. *自动控制原理*. 第7版. 科学出版社, 2019.
