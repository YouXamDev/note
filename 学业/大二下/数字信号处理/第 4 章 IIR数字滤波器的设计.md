## 模拟滤波器的设计

### 巴特沃思低通滤波器

$$
\vert H(j\Omega) \vert = \frac{1}{\sqrt{1 + \left( \frac{\Omega}{\Omega_c} \right)^{2N}}}
$$

#### 查表法

1. 由给定的通带指标 $\Omega_p, A_p$ 和阻带 $\Omega_s, A_s$，查表求得滤波器的阶数 $N$：

    - 通带频率: $\Omega_p$
    - 通带内最大衰减: $A_p$
    - 阻带下限频率: $\Omega_s$
    - 阻带内最小衰减: $A_s$

    先将频率归一化：

    $$\lambda_p = 1, \lambda_s = \frac{\Omega_s}{\Omega_p}$$
2. 从表中得到 $H(p)$ 的分母多项式；
3. 把 $p$ 替换为 $s / \Omega_p$，得到 $H(s)$。
    $$H(s) = H(p)\vert_{p = \frac{s}{\Omega_p}}$$

### 切比雪夫滤波器

$$
\vert H(j\Omega) \vert^2 = \frac{1}{1 + \varepsilon^2 C_N^2 \left( \frac{\Omega}{\Omega_c} \right)}
$$

其中 $\varepsilon$ 表示通带内波动范围，$C_N(\frac{\Omega}{\Omega_c})$ 是切比雪夫多项式。

$$
C_N(x) = \begin{cases}
    \cos(N \arccos x), & 0 \le x 1 \\
    \cosh(N \text{arccosh} x), & x \ge 1
\end{cases}
$$

#### 查表法

1. 归一化 $\lambda_p = 1, \lambda_s = \frac{\Omega_s}{\Omega_p}$；
2. 查曲线确定滤波器的阶数 $N$；
3. 查表得到 $\varepsilon$；
4. 查表得到 $H(p)$ 的分母多项式的因式形式 $A(p)$，求得 $H(p)$：
    $$H(p)=\frac 1{\varepsilon 2^{N-1}A(p)}$$
5. 求得 $$H(s) = H(p)\vert_{p = \frac{s}{\Omega_p}}$$

### 其他类型滤波器的变换

对于高通、带通和带阻，需要将 $\lambda_s$ 和 $p$ 按下表变换：

| 滤波器类型 | 归一化要求 | $$H_d(s)=H_{LP}(p) \vert_{p=q(s)}$$|
|:---:|:---:|:---:|
|低通|$\lambda_p=1, \lambda_s=\frac{\Omega_s}{\Omega_p}$|$$p=\frac{s}{\Omega_p}$$|
|高通|$\lambda_p=1, \lambda_s=\frac{\Omega_p}{\Omega_s}$|$$p=\frac{\Omega_p}{s}$$|
|带通|$\lambda_p=1, \lambda_s=\frac{\Omega_{s2}-\Omega_{s1}}{\Omega_{p2}-\Omega_{p1}}$|$$p=\frac{s^2+\Omega_{p1}\Omega_{p2}}{(\Omega_{p2}-\Omega_{p1}) s}$$|
|带阻|$\lambda_p=1, \lambda_s=\frac{\Omega_{p2}-\Omega_{p1}}{\Omega_{s2}-\Omega_{s1}}$|$$p=\frac{(\Omega_{p2}-\Omega_{p1})s}{s^2+\Omega_{p1}\Omega_{p2}}$$|

### 带通和带阻的几何对称

当求带通或者带阻滤波器的时候，两个通带截止频率和两个阻带起始频率都关于中心频率 $\Omega_0$ 对称：

$$
\Omega_0 = \sqrt{\Omega_{p1}\Omega_{p2}} = \sqrt{\Omega_{s1}\Omega_{s2}}
$$

若不对称，则需要调整边缘频率使其对称：

1. 对于带通滤波器，$\Omega_{p1}$ 和 $\Omega_{p2}$ 不变，更改 $\Omega_{s1}$ 或 $\Omega_{s2}$ 使其对称，具体更改哪个要看哪个能让起始频率更靠近 $\Omega_0$。若 $\frac{\Omega_0^2}{\Omega_{s2}}>\Omega_{s1}$，则更改 $\Omega_{s1}$ 为 $\frac{\Omega_0^2}{\Omega_{s2}}$，若 $\frac{\Omega_0^2}{\Omega_{s2}}\le\Omega_{s1}$，则更改 $\Omega_{s2}$ 为 $\frac{\Omega_0^2}{\Omega_{s1}}$。同时取阻带最小衰减为两者之间的最大值。
2. 对于带阻滤波器，$\Omega_{s1}$ 和 $\Omega_{s2}$ 不变，更改 $\Omega_{p1}$ 或 $\Omega_{p2}$ 使其对称，具体更改哪个要看哪个能让截止频率更靠近 $\Omega_0$。若 $\frac{\Omega_0^2}{\Omega_{p2}}>\Omega_{p1}$，则更改 $\Omega_{p1}$ 为 $\frac{\Omega_0^2}{\Omega_{p2}}$，若 $\frac{\Omega_0^2}{\Omega_{p2}}\le\Omega_{p1}$，则更改 $\Omega_{p2}$ 为 $\frac{\Omega_0^2}{\Omega_{p1}}$。同时取通带最大衰减为两者之间的最小值。

总而言之，如果不对称，对于 $\Omega_{p1}$、$\Omega_{p2}$、$\Omega_{s1}$ 和 $\Omega_{s2}$ 四个值，中间的两个值不动，更改两个边缘的值，对于两个边缘的值，哪个修改之后比之前更接近中心频率，就修改哪个。如果靠近边缘的（对于带通滤波器看阻带，对于带阻滤波器看通带）最大/小衰减（$A_p/A_s$），应当使其更接近中间的值（若两边为阻带，则取两者最大值，若两边为通带，则取两者最小值）。

## 模拟滤波器的数字化

### 冲激响应不变法

1. 对已知的 $H_a(s)$ 进行拉氏反变换，求得 $h_a(t)$，
2. 根据冲激相应不变准则，令 $h(n)=Th_a(nT)$，求得 $h(n)$。
3. 对 $h(n)$ 进行 $z$ 变换，得到 $H(z)$。

**约束条件**：
1. $h_a(t)$ 是限带信号，当 $\vert \Omega \vert > \Omega_m$ 时，$H_a(j\Omega) = 0$；
2. 取样频率 $\Omega_s \ge 2 \Omega_m$，即 $f_s \ge 2 f_m$。

本质上只需要将 $H_a(s)$ 分解成部分分式之和的形式，就可以得到 $H(z)$

$$
H_a(s) = \sum_{i=1}^N \frac{\color{red}A_i}{s - {\color{blue}s_i}} 
\Rightarrow
H(z) = {\color{red}T}\sum_{i=1}^N \frac{\color{red}A_i}{1 - e^{ {\color{blue}s_i} T} z^{-1}}
$$

ROC 为 $|z| > |e^{s_iT}|$

### 双线性变换法

$$
H(z) = H_a(s) \vert _{s = \frac{2}{T} \frac{1 - z^{-1}}{1 + z^{-1}}}
$$

#### 频率预畸变

1. DF 的性能指标 $\omega_s, \omega_p, A_p, A_s$；
2. 根据 $\Omega=\frac 2 T \tan\frac{\omega}2=2 f_s \tan\frac{\omega}2$ 转换 DF 性能指标，得到 AF 性能指标 $\Omega_s, \Omega_p$；
3. 根据 $\Omega_s, \Omega_p, A_p, A_s$ 设计模拟低通原型滤波器，得到 AF 的系统函数 $H_a(s)$；
4. 根据双线性变换法，得到 DF 的系统函数 $H(z)$。
