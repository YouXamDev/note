
## FIR 数字滤波器的类型

| 线性相位类型  | 严格线性相位 $h(n)=h(N-1-n)$ （偶对称） | 广义线性相位 $h(n)=-h(N-1-n)$ （奇对称）      |
| ------- | ---------------------------- | ---------------------------------- |
| $N$ 为奇数 | I 型，适用于所有类型                  | III 型，不能设计低通，高通和带阻，一定有 +1 和 -1 的零点 |
| $N$ 为偶数 | II 型，不能设计高通和带阻，一定有 -1 零点     | IV 型，不能设计低通和带阻，一定有 +1 的零点          |
若在 +1 或者 -1 一定有零点，说明在 +1 和 -1 的零点数量一定是奇数个，否则一定是偶数个。

若 FIR 存在零点 $a$，那么 $a^*$, $1/a$, $(1/a)^*$ 也一定是零点。
## 窗函数和常用窗函数性能指标

1. **矩形窗**：
	
	$$w_R(n)=R_N(n)$$
2. **汉宁窗**：
	
	$$w(n)=0.5-0.5 \cos \left( \frac{2 \pi n}{N-1} \right)$$
3. **汉明窗**：
	
	$$w(n)=0.54-0.46 \cos \left( \frac{2 \pi n}{N-1} \right)$$
3. **布莱克曼窗**：
	
	$$w(n)=0.42-0.5 \cos \left( \frac{2 \pi n}{N-1} \right)+0.08 \cos \left( \frac{4 \pi n}{N-1} 2n \right)$$
4. **凯瑟窗**：
	
	$$w(n)=\frac{I_0 \left( \beta \sqrt{1-\left(1- \frac{2n}{N-1}\right)^2} \right)}{I_0(\beta)}, \quad 0 \le n \le N - 1$$


|        窗函数         | 最大旁瓣峰值 $\alpha_p / \text{dB}$ |  主瓣宽度（近似过渡带带宽）   | 加窗后滤波器的精确过渡带带宽 $\Delta \omega$ | 加窗后滤波器阻带最小衰减 / dB |
| :----------------: | :----------------------------: | :--------------: | :----------------------------: | :---------------: |
|        矩形窗         |                           -13 | $\frac{4 \pi}N$  |       $\frac{1.8 \pi}N$        |        -21        |
|        汉宁窗         |                           -31 | $\frac{8 \pi}N$  |       $\frac{6.2 \pi}N$        |        -44        |
|        汉明窗         |                           -41 | $\frac{8 \pi}N$  |       $\frac{6.6 \pi}N$        |        -53        |
|       布莱克曼窗        |                           -57 | $\frac{12 \pi}N$ |        $\frac{11 \pi}N$        |        -74        |
| 凯瑟窗($\beta=7.865$) |                           -57 | $\frac{10 \pi}N$ |        $\frac{10 \pi}N$        |        -80        |

## 利用窗函数设计线性相位 FIR 数字滤波器的设计步骤

1. 首先选择窗函数类型，选择最合适的窗函数（在满足阻带衰减的条件下选择主瓣宽度最小的窗函数）。然后求得窗口长度 $N$，注意如果过渡带宽度不相等，使用最小的计算。
2. 采用标准窗函数法构造期望逼近的 FIR 数字滤波器的频率响应函数 $H_d(e^{j \omega})$：
	
	$$H_d(e^{j \omega})=H_{da}(\omega)e^{-j\tau\omega}$$
	
	其中 $\tau=\frac{N-1}2$
3. 利用 ITDFT 计算单位冲激相应 $h_d(n)$：
	
	$$h_d(n)=\frac 1 {2 \pi} \int_{-\pi}^{\pi} H_d(e^{j \omega})e^{j \omega n} \, \mathrm d \omega$$

	$\omega_c$ 应当使用过渡带的中点频率。
4. 加窗得到实际 FIR 系数序列 $h(n)$：
	
	$$h(n)=h_d(n)w(n)$$

可直接查表：

|  类型  | 频率响应 $H_d(e^{j\omega})(r=\frac{N-1}{2})$                                                                                        | 单位冲激响应 $h_d(n)=\frac{1}{2\pi}\int_{-\pi}^{\pi}H_d(e^{j\omega})e^{j\omega n}\mathbf{d}\omega$                                                    |
| :--: | ------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| 理想低通 | $$\begin{cases}e^{-j\tau\omega}&, \vert \omega \vert \le \omega_c \\ 0&,  \text{其他}\end{cases}$$                                | $$\frac{\sin((n - \tau)\omega_c)}{(n - \tau)\pi}$$                                                                                              |
| 理想高通 | $$\begin{cases}e^{-j\tau\omega}&, \omega_c \le \vert \omega \vert   \\ 0&,  \text{其他}\end{cases}$$                              | $$\frac{\sin((n - \tau)\pi)}{(n - \tau)\pi} - \frac{\sin((n - \tau)\omega_c)}{(n - \tau)\pi}$$                                                  |
| 理想带通 | $$\begin{cases}e^{-j\tau\omega}&, \omega_l \le \vert \omega \vert \le \omega_h \\ 0&,  \text{其他}\end{cases}$$                   | $$\frac{\sin((n - \tau)\omega_h)}{(n - \tau)\pi} - \frac{\sin((n - \tau)\omega_l)}{(n - \tau)\pi}$$                                             |
| 理想带阻 | $$\begin{cases}e^{-j\tau\omega}&,\vert \omega \vert \le \omega_l, \omega_h\le \vert \omega \vert \\ 0&,  \text{其他}\end{cases}$$ | $$\frac{\sin((n - \tau)\omega_l)}{(n - \tau)\pi} + \frac{\sin((n - \tau)\pi)}{(n - \tau)\pi} - \frac{\sin((n - \tau)\omega_h)}{(n - \tau)\pi}$$ |


## 利用频率取样法设计线性相位 FIR 数字滤波器的设计步骤

1. 对理想滤波器频率响应 $H_d(e^{j\omega})$ 在 $\left[0, 2\pi\right)$ 内等间隔采样 $N$ 点，得到 $H_d(k)$:

	$$H_d(k)=H_d(e^{j\omega}) \vert_{\omega=\frac{2\pi k}{N}}, \quad, k=0,\cdots,N-1$$
2. 利用公式求出 $H(z)$ 的表达式：
	
	$$H(z)=\frac 1 N \sum_{k=0}^{N-1}H_d(k)\frac{1-z^{-N}}{1-W_N^{-k}z^{-1}}$$
3. 求出系统的频率响应 $H(e^{j\omega})$：
	
	$$H(e^{j\omega})=\sum_{k=0}^{N-1}H_d(k)\phi(\omega-\frac{2\pi}Nk)$$

	$$\phi(\omega)=\frac 1 N \frac{\sin \frac{N\omega}2}{\sin \frac \omega 2}e^{-j\omega \frac{N-1}2}$$


## FIR 数字滤波器的实现结构

### 直接型

$$H(z)=\sum_{n=0}^{N-1}h(n)z^{-n}$$

![](files/Pasted%20image%2020240621171700.png)

### 级联型

$$H(z)=\sum_{n=0}^{N-1}h(n)z^{-n}=\prod_{i=0}^K\left(\alpha_{0i}+\alpha{1i}z^{-1}+\alpha_{2i}z^{-2}\right)$$

写成按零点表示的二阶因式的形式

![](files/Pasted%20image%2020240621172041.png)

### 线性相位结构


|         | $h(n)$ 偶对称                                     | $h(n)$ 奇对称                                     |
| ------- | ---------------------------------------------- | ---------------------------------------------- |
| $N$ 为偶数 | ![](files/Pasted%20image%2020240621172521.png) | ![](files/Pasted%20image%2020240621172634.png) |
| $N$ 为奇数 | ![](files/Pasted%20image%2020240621172447.png) | ![](files/Pasted%20image%2020240621172543.png) |
### 频率取样型(不考)

把 $H(z)$ 写成 

$$H(z)=\frac 1 N {\color{red}(1-Z^{-N})} { \color{blue} \sum_{k=0}^{N-1}  \frac{H(k)}{1-W_N^{-k}z^{-1}}}$$


![](files/Pasted%20image%2020240621173328.png)

**稳定性问题**

为了保证稳定性，需要把单位圆上的零点和极点移到半径 $r$ 略小于 1 的圆上，用 $rz^{-1}$ 代替 $z^{-1}$

$$H(z)=\frac 1 N (1-r^Nz^{-N}) \sum_{k=0}^{N-1} \frac{H(k)}{1-rW_N^{-k}z^{-1}}$$

