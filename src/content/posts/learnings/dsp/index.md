---
title: 數位訊號處理筆記
published: 2026-02-24
updated: 2026-03-15
description: 有種死到臨頭開始覺得以前的自己實在混的可以，決定把這學期數位訊號處理上的內容做成筆記，希望能幫到未來的我或是其他需要的人
image: ""
tags: [DSP]
category: Learnings
draft: false
lang: zh_TW
---

# 前言

最近突然覺得自己常常耍廢摸魚得過且過感覺未來會暴死，經過深刻的反省之後決定把這學期上課的內容整理成筆記放到 blog 裡，希望這個系列能夠安穩落地。因為目前才剛開始教所以分類會有些隨便，以後滾動調整吧～

---

# 緒論

## 關於訊號

- 訊號：資訊的流動
- 訊號處理：生成、轉換和解釋資訊的關鍵技術
- 數位訊號處理：使用電腦處理訊號

## 訊號的分類與轉換

訊號主要分為兩大類 ：

1. **連續時間訊號 (Continuous-time, CT)**：標記為 $x(t)$
2. **離散時間訊號 (Discrete-time, DT)**：標記為 $x[n]$，其中 $n$ 為整數 $(0, 1, 2, \ldots)$

![](w1_1.png)

連續時間訊號經過取樣(Sampling)後轉換為離散訊號，取樣率是一個至關重要的參數，它決定了你每秒鐘要抓取多少個點。取樣率越高，還原出來的訊號就越接近原始的連續狀態。

### 類比轉數位 (A/D Conversion)

訊號從連續時間訊號轉為類比訊號需要經過兩個步驟，分別是取樣(Sampling)以及量化(Quantization)：

$$
CT \xrightarrow{Sampling} DT \xrightarrow{Quantization} Digital
$$

- **取樣（時間離散化）**：將 $x(t)$ 轉換為 $x[n]$，決定了訊號的**頻率範圍**
    - 取樣週期($T$)：兩次取樣之間的時間間隔
    - 取樣率/頻率($f_s$)：$f_s = \frac{1}{T}$
    - 關係式：$t = n \cdot T$
- **量化（數值離散化）**：將連續的振幅值轉為有限精度的數位資料，決定了訊號的**動態範圍與細節**

## 基礎離散時間訊號

1. **單位樣本序列 (Unit Sample Sequence / Impulse)**

    標記為 $δ[n]$，其定義如下 ：

    $$
    δ[n] = 
    \left\{ 
    \begin{array}{ll}
        1, & n = 0 \\
        0, & n \neq 0
    \end{array}
    \right.
    $$

    ![](w1_2.svg)

2. **單位步階序列 (Unit Step Sequence)**

    標記為 $u[n]$，其定義如下 ：

    $$
    u[n] = 
    \left\{ 
    \begin{array}{ll}
        1, & n \ge 0 \\
        0, & n < 0
    \end{array}
    \right.
    $$

    ![](w1_3.svg)

3. **指數序列 (Exponential Sequence)**

    形式為 $x[n] = A \cdot \alpha^n$，$A$ 與 $\alpha$ 可為實數或複數

## 重要數學性質與表示法

### 任意序列的表示 (Representation of Arbitrary Sequence)

任何離散序列 $x[n]$ 都可以表示為一組加權且延遲的單位脈衝之和 ：

![](w1_4.png)

寫成一般式（$a_k$ 即為 $x[k]$ 的值）：

$$
x[n]=\sum_{k=-\infty}^\infty {{\color{#3071c4} a_k} \cdot \delta[n-k]}=\sum_{k=-\infty}^\infty {{\color{#3071c4} x[k]} \cdot \delta[n-k]}
$$
![](w1_5.svg)

**$u[n]$ 與 $\delta[n]$ 的關係**

- **累加關係：** $u[n]=\sum_{k=0}^\infty \delta[n-k]$ 或 $u[n]=\sum_{k=-\infty}^n \delta[k]$
- **差分關係：** $\delta[n]=u[n]-u[n-1]$

### 複數平面與歐拉公式 (Euler's Formula)

在處理複數指數訊號時常用到：

- 歐拉公式：$e^{j \phi}=\cos \phi+j\sin \phi$
- 極座標表示：$A=|A| e^{j \phi_1}$，$\alpha= |\alpha| e^{j \phi_2}$
- 因此 $x[n]=A \alpha^n= |A| |\alpha|^n e^{j(n \phi_2 + \phi_1)}$

_✍️TODO 以後想到再補圖_

---

# 訊號取樣的影響

## 取樣與混疊 (Sampling and Aliasing)

### 基本定義

假設一個連續時間訊號 $x(t) = \cos(\omega t)$
- $\omega$：角頻率 (Radian Frequency)，單位：rad/s（徑度/秒）
- $t$：連續時間，單位：sec（秒）

:::note
複習一下高中內容：在圓周運動或簡諧運動的數學推導中，如果使用「圈數」，公式裡會不斷出現 $2 \pi$ 這個係數，顯得累贅。為了讓方程式更簡潔，我們直接把 $2 \pi$ 乘進去，定義出角頻率（以 $\omega$ 表示）
:::

### 取樣過程（Sampling Process）

1. 每隔 $T$ 秒取樣一次，$T$: 取樣週期 (Sampling Period)
    $$
    \begin{aligned}
    {\text{時間 } t\text{：}} & 0, T, 2T, 3T, \ldots \\
    t &= nT \\
    n &= 0, 1, 2, 3, \ldots
    \end{aligned}
    $$

2. 離散化
    $$
    \begin{aligned}
    x(t) &= \cos(\omega {\color{#3071c4}t}) \\
    &\downarrow {\color{#3071c4}t = nT} \\
    x({\color{#3071c4}nT}) &= \cos(\omega {\color{#3071c4}nT}) \\
    &\Downarrow \\
    x[n] &= \cos(\omega nT)
    \end{aligned}
    $$

3. 取樣頻率轉換
    取樣頻率：$f_s = \frac{1}{T}$ Hz，取樣角頻率為 $\omega_s = \frac{2 \pi}{T}$ rad/s
    :::tip
    $\omega = 2 \pi f \xrightarrow{f = \frac{1}{T}} \omega = \frac{2 \pi}{T}$
    :::

### 離散時間的週期性 (Periodicity in Discrete Time)

假設這裡有兩個訊號：$x$ 和 $x_1$

$$
\left\{ 
\begin{array}{ll}
    x &= \cos(\omega t) \\
    x_1 &= \cos((\omega + {\color{#3071c4}\omega_s})t)
\end{array}
\right.
$$

很明顯這是兩個不一樣頻率的訊號，但如果我們對它們進行採樣，會發現一個很有趣的現象：

$$
\begin{aligned}
x_1(t) &= \cos((\omega+\omega_s)t) \\
&\downarrow t = {\color{#3071c4}nT} \\
x_1[n] &= \cos((\omega+\omega_s){\color{#3071c4}nT}) \\
&\because \omega_s = \frac{2\pi}{T} \\
\therefore x_1[n] &= \cos((\omega+\frac{2\pi}{T})\cdot nT) \\
&= cos(\omega nT+\frac{2\pi}{\cancel{T}}\cdot n\cancel{T}) \\
&= cos(\omega nT+{\underbrace{2\pi n}_{\color{#e53935}{2\pi\text{ 的整數倍，不影響值}}}}) \\
&= cos(\omega nT)
\end{aligned}
$$

可以觀察到雖然 $x$ 跟 $x_1$ 不同，但取樣後都是 $cos(\omega nT)$，$\therefore x[n] = x_1[n]$。

:::note
這裡再假設一個 $x_2(t) = \cos((-\omega+{\color{#3071c4}\omega_s})t)$，取樣後會得到 $x_2[n] = \cos(-\omega nT)$，但因為 cosine 是偶函數，所以 $x_2[n] = \cos(-\omega nT) = \cos(\omega nT) = x[n]$
:::

我們可以得到一個結論，在離散的世界裡，很多不同頻率的連續波，採樣後居然會看起來一模一樣，這個就是所謂的 **「混疊現象」(Aliasing Phenomenon)**。

![](w2_1.png)

<small>🔗圖片來源：https://www.ni.com/docs/zh-TW/bundle/labwindows-cvi/page/advancedanalysisconcepts/aliasing.html</small>

## 奈奎斯特-香農取樣定理 (Nyquist-Shannon Sampling Theorem)

我們現在知道如果取樣頻率不夠高，就會在這種週期性的訊號上產生混疊現象，那如果不想要產生混疊現象的話，具體來說需要多高的取樣頻率呢？**奈奎斯特-香農取樣定理**給出了答案，並定義了所謂的**奈奎斯特頻率 (Nyquist Frequency)**，又或者稱作**奈奎斯特極限 (Nyquist Limit)**。

- **奈奎斯特極限：** 能被系統唯一表示的最高頻率為 $\frac{\omega_s}{2}$
- **發生混疊的條件：** 如果輸入訊號的最高頻率 $\omega_N > \frac{\omega_s}{2}$，就會發生混疊
- **取樣定理：** 假設一個連續時間訊號 $x(t)$，它是一個**頻帶受限訊號 (band-limited signal)**，最高頻率為 $\omega_N$，寫成數學式：$X(j\omega) = 0 \text{ for } |\omega| \ge \omega_N$（所有比 $\omega_N$ 大的頻率都不存在 $\rightarrow X(j\omega) = 0$）。如果我們以 $\omega_s \ge 2\omega_N$ 的頻率對其進行取樣，那麼這個連續訊號 $x(t)$ 就可以 **唯一地 (uniquely)** 被它的離散取樣點 $x[n]$ 所決定。換句話說，只要取樣正確，就能拿這些離散的點拼湊回原本的 $x(t)$

:::tip[頻帶受限訊號 (Band-Limited Signal)]
指其頻譜能量集中在有限的頻率範圍內的訊號，簡單來說，它的頻率是有上限的 ($\omega_{max} < \infty$)。
:::

#### 完美還原訊號的數學條件

$$
\begin{aligned}
&\text{if } \overbrace{\omega_s}^{\color{#3071c4}\text{取樣頻率}} \ge \overbrace{2\omega_N}^{\color{#e53935}\text{最高訊號頻率}} \\
&\text{i.e. , Nyquist Limit } \frac{\omega_s}{2} \\
&\omega_N \le \frac{\omega_s}{2} \color{#3071c4}{\Rightarrow \omega_s \ge 2\omega_N} \\
&\omega_s = \frac{2\pi}{T} \Rightarrow T = \frac{2\pi}{\omega_s}
\end{aligned}
$$

## 反混疊濾波器（Anti-aliasing Filter）

_✍️TODO 以後補_

## 範例與練習

### Ex.1

Determine which input signals to a digital filter or DSP system will be aliased by the given period $\text{Т}$?

1. $x(t) = 2\cos(10t)\text{ , T = 0.1s}$
2. $x(t) = 8\cos(15t)\text{ , T = 0.2s}$

:::note[ANS]
1. $\omega_s = \frac{2\pi}{T} = \frac{2\pi}{0.1} = 20\pi$ <br>
    Nyquist Frequency: $\frac{\omega_s}{2} = 10\pi\approx 31.4$ <br>
    $\therefore \omega_N = 10 < \frac{\omega_s}{2}$，沒有混疊
2. 同理，$\frac{\omega_s}{2} = \frac{\frac{2\pi}{0.2}}{2} = 5\pi\approx 15.7$ <br>
    $\therefore \omega_N = 15 < \frac{\omega_s}{2}$，沒有混疊
:::

### Ex.2

Determine if the following signals will be aliased. If the signal is aliased into having the same sample value as a lower frequency sinusoidal signal, determine the lower sinusoidal signal.

1. $x(t) = 7\cos(25t)\text{ , T = 0.1s}$
2. $x(t) = 3\cos(160t)\text{ , T = 0.02s}$

:::note[ANS]
1. $\frac{\omega_s}{2} = \frac{\frac{2\pi}{0.1}}{2} = 10\pi\approx 31.4$ <br>
    $\therefore \omega_N = 25 < \frac{\omega_s}{2}$，沒有混疊
2. $\frac{\omega_s}{2} = \frac{\frac{2\pi}{0.02}}{2} = 50\pi\approx 157$ <br>
    $\therefore \omega_N = 160 > \frac{\omega_s}{2}$，有混疊 <br>
    由於[混疊高頻摺疊至低頻的現象](#離散時間的週期性-periodicity-in-discrete-time)，$-\omega_1 + \omega_s = 160$ <br>
    又 $\omega_s = 100\pi \Rightarrow \omega_1 = 100\pi - 160$ <br>
    $\therefore x_1(t) = 3\cos((100\pi - 160)t) \approx 3\cos(154.2t)$
:::

---

# 數位濾波器規格

## 基礎概念與公式

在介紹數位濾波器之前，要先了解一下下面這三個東西，它們是衡量一個濾波器好不好用、有沒有效的核心概念：

- **增益 (Gain)**
    - $Gain = \frac{\text{輸入訊號的振幅}}{\text{輸出訊號的增幅}}$
    - 如果 $Gain = 1$，代表訊號無損通過。在濾波器中，這就是我們希望保留訊號的區域，稱為 **通帶 (Pass Band)**
    - 如果 $Gain$ 趨近於 $0$，代表訊號被擋下或大幅削弱。這就是我們希望濾除雜訊的區域，稱為 **阻帶 (Stop Band)**
- **損失 (Loss)**
    - 增益的倒數，即 $Loss = {(Gain)}^{-1}$，本質上是一樣的東西，只是換個角度描述
- **分貝 (dB)：** 現實世界的訊號強弱差異極大，如果我們在定義濾波器規格的時候只使用倍數來表示，數值會變得極端並且難以繪圖，比如 $\text{通帶}:\text{阻帶} = 1 : 10^{-6}$。為了閱讀和計算的方便性，分貝這個單位就出現了，它利用對數的性質將巨大的倍數差異壓縮成好處理的數字
    - 分貝轉換公式：$Gain_{\color{#3071c4}{dB}} = 20\cdot\log_{\color{#e53935}{10}}(Gain)$
    - 無變化時（$1$ 倍）：$Gain = 1$，$20\cdot\log_{10}(1) = 0\text{ dB}$，因此 $0\text{ dB}$ 在圖表中常常被用來代表訊號的基準線
    - 訊號放大時（假設 $2$ 倍）：$20\cdot\log_{10}(2) \approx 6\text{ dB}$
    - 訊號衰減時（假設 $10^{-4}$ 倍）：$20\cdot\log_{10}(10^{-4}) = -80\text{ dB}$

:::tip[REMARK]
以分貝作為單位的時候，增益和損失的關係變得更加簡單：
$$
\begin{aligned}
& \because\cancel{20}\cdot\log_{10}(Loss) = \cancel{20}\cdot\log_{10}((gain)^{-1}) \\
& \therefore Loss = -Gain
\end{aligned}
$$
:::

#### 奈奎斯特頻率 / 摺疊頻率（Nyquist / Folding frequency）

$$
\begin{aligned}
\frac{\omega_s}{2} &= \frac{\cancel{2}\pi \cdot f_s}{\cancel{2}} \\
\because T &= \frac{1}{f_s}, \quad T: \text{Sampling Period} \\
\therefore \frac{\omega_s}{2} &= \pi \cdot \frac{1}{T} = \frac{\pi}{T}
\end{aligned}
$$

## 濾波器與規格參數

頻率響應的圖表主要由通帶 (Pass Band)、阻帶 (Stop Band) 與 過渡帶 (Transition Band) 組成。

- **增益規格（Gain spec.）：**
    - $g_{pmax}$​：通帶內允許的最大增益
    - $g_{pmin}$​：通帶內允許的最小增益
    - $g_{smax}$：阻帶內允許的最大增益
- **頻率規格（Frequency spec.）：**
    - $\omega_{p}$​：通帶內的最高頻率
    - $\omega_{s}$​：阻帶內的最低頻率
    - $\omega_{f}$​：摺疊頻率，也就是奈奎斯特頻率，取樣頻率的一半 $\frac{\omega_{sampling}}{2}$

### 低通濾波器規格 (Lowpass Digital Filter Specification)

- 概念圖
    ![](w3_1_1.svg)
- 規格定義圖
    ![](w3_1_2.svg)
    :::note
    在工程設計中，只要增益曲線（綠線）不要碰到「容差邊限」或稱「禁制區」（黑色的斜線區域）就是合格的濾波器了
    :::

### 高通濾波器規格 (Highpass Digital Filter Specification)

- 概念圖
    ![](w3_2_1.svg)
- 規格定義圖
    ![](w3_2_2.svg)

### 帶通濾波器規格 (Bandpass Digital Filter Specification)

- 概念圖
    ![](w3_3_1.svg)
- 規格定義圖
    ![](w3_3_2.svg)

### 帶阻濾波器規格 (Bandstop Digital Filter Specification)

- 概念圖
    ![](w3_4_1.svg)
- 規格定義圖
    ![](w3_4_2.svg)
    :::tip
    某些帶阻濾波器又被稱為**帶陷濾波器 (Notch Filter)**，Notch 是凹口或是刻痕的意思，一般來說，Notch Filter 專指頻帶非常窄的帶阻濾波器。
    :::

## 濾波器的實際應用

- 低通濾波器
    1. 降噪，如 ECG 或 EEG 等生醫訊號及音訊處理
    2. 抗混疊濾波器
    3. 影像處理
- 高通濾波器
    1. 去除直流成分
    2. 影像邊緣偵測
- 帶通濾波器
    1. 無線通訊
    2. 生醫訊號處理
    3. 語音訊號處理
- 帶阻濾波器
    1. 消除電源線干擾
    2. 語音通訊
- 全通濾波器（增益維持1）
    1. 相位補償
    2. 多通道通訊系統中的訊號對齊

## 範例與練習

### Ex.1

Draw the graphical specification for a digital highpass filter out to the folding frequency in rad/s that will not change the gain above 500 rad/s by more than ±3 dB, while reducing the gain below 200 rad/s by more than 40 dB.
The sampling time T = 0.001 s.

:::note[ANS]
1. 先確定 x 軸的盡頭，也就是 $\omega_f$ 的部分，由於取樣時間 $T = 0.001\text{s}$ </br>
    $\because\text{Nyquist Frequency} = \frac{\pi}{T}$ [💡為什麼？](#奈奎斯特頻率--摺疊頻率nyquist--folding-frequency)</br>
    $\therefore \omega_f = \frac{\pi}{0.001} = 1000\pi$
2. 畫出阻帶，題目說 200 rad/s 以下增益要衰減 40 dB，因此阻帶容許最大增益為 -40 dB，即 $g_{smax} = -40\text{ db}$
3. 畫出通帶，題目說 500 rad/s 以上增益改變不能超過 ±3 dB，以無損增益 0 dB 為基準，可得知允許的範圍最高 $g_{pmax} = 3\text{ dB}$，最低 $g_{pmin} = -3\text{ dB}$

規格圖如下所示：
![](w3_5_1.svg)
:::

### Ex.2

Draw the graphical specification for a bandstop digital filter out to its folding frequency that will reduce the gain between 1,000 rad/s and 5,000 rad/s by more than 60 dB, but will not reduce the gain above 10,000 rad/s or below 150 rad/s by more than 3 dB. The sampling rate is 10,000 Hz.

:::note[ANS]
$\text{Nyquist Frequency} = \frac{\omega_s}{2} = \frac{\cancel{2}\pi\cdot f_s}{\cancel{2}} = 10000\pi$ </br>
後面跟上一題類似故省略 </br>
規格圖如下所示：
![](w3_5_2.svg)
:::

---

# Z 轉換

取樣並獲得了離散的數據後，我們通常想要對它進行一些處理，例如我們上一個主題講到的濾波器，用來去除雜訊或增強特定頻率。但在時域進行分析非常困難，因此我們會希望對訊號進行轉換，並在「頻域」來分析離散系統。

在連續系統中，我們使用拉普拉斯轉換 (Laplace Transform)；而在離散系統中，對應的工具就是 **Z 轉換 (Z-Transform)**。

![](w4_1.png)

## 為什麼要轉到頻域？

工程師偏好在頻域處理問題，最主要的原因是運算邏輯的簡化：在時域極為複雜的「卷積」運算，轉到頻域後會變成簡單的「乘法」。

![](w4_2.svg)

| 特性 | 時域 (Time Domain) | 頻域 (Frequency Domain) |
| :--- | :--- | :--- |
| **運算邏輯** | 卷積 (Convolution) $*$ | 乘法 (Multiplication) $\cdot$ |
| **連續訊號** | $y(t) = x(t) * h(t)$ | $Y(s) = X(s) \cdot H(s)$ |
| **離散訊號** | $y[n] = x[n] * h[n]$ | $Y(z) = X(z) \cdot H(z)$ |

### 正規化角頻率 (Normalized Angular Frequency)

在處理離散訊號時，為了避免公式裡一直帶著取樣週期 $T$ 而顯得臃腫，我們通常會定義一個新的變數：

$$
\begin{aligned}
f(t) &= \cos(\omega{\color{#3071c4}\cdot} t) \\
&\quad {\color{#3071c4}let}\downarrow{\color{#3071c4}t=n\cdot t}{\color{#e53935}\text{ <Sampling>}} \\
f[n] &= \cos(\omega{\color{#3071c4}\cdot n\cdot t}) = \cos(\omega T\cdot n) \\
&\quad {\color{#3071c4}let}\downarrow{\color{#3071c4}\Omega=\omega T} \\
&= \cos({\color{#3071c4}\Omega}n)
\end{aligned}
$$

這裡的 $\Omega = \omega T$ 稱為 **離散角頻率** 或 **正規化角頻率**，單位為 rad。

## Z 轉換定義

將離散訊號 $f[n]$ 進行 Z 轉換得到 $F(z)$ 的一般式如下：

$$
F(z) = \sum\limits_{n=-\infty}^{\infty}f[n]\cdot z^{-n}
$$

若訊號具備因果性（即從 $n = 0$ 開始），則公式縮減為：
$$F(z) = \sum\limits_{n=0}^{\infty}f[n]\cdot z^{-n}$$

## 基礎訊號轉換範例

### 1. 單位樣本/脈衝訊號 (Unit Impulse)

根據定義，$\delta[n]$ 只在 $n=0$ 時有值，其餘皆為 0。

$$
\begin{aligned}
z(\delta[n]) &= \sum\limits_{n=-\infty}^{\infty}\delta[n]\cdot z^{{\color{#e53935}-n}} \\
&= \delta[0]\cdot z^{{\color{#e53935}-}0} = 1\cdot 1 = 1
\end{aligned}
$$

#### 💡 Example: 組合脈衝訊號
Use the unit-sample signal to describe a sampled data where the initial sample $x[0]=2$, $x[3]=-2$ and the rest of the samples are zero.

**Step 1. 寫出離散表示式：**
![](w4_3.svg)
$$x[n] = 2\cdot \delta[n] - 2\cdot \delta[n-3]$$

**Step 2. 進行 Z 轉換：**
$$
\begin{aligned}
X(z) &= \sum\limits_{n=-\infty}^{\infty}x[n]\cdot z^{-n} \\
&= x[0]\cdot z^{-0} + x[3]\cdot z^{-3} \\
&= 2 - 2z^{-3}
\end{aligned}
$$

### 2. 單位步階訊號 (Unit-step Signal)

對於 $u[n]$，訊號在 $n \ge 0$ 時恆為 1。

:::tip[REMARK]
當 $|r| < 1$ 時，無窮等比級數之和為：
$1 + r + r^2 + r^3 + \dots = \frac{1}{1-r}$
:::

將 $u[n]$ 代入 Z 轉換公式，令 $r = z^{-1}$：
$$
\begin{aligned}
Z(u[n]) &= 1 + z^{-1} + z^{-2} + z^{-3} + \dots \\
&= \frac{1}{1-z^{-1}} = \frac{z}{z-1}
\end{aligned}
$$

## Z 轉換常用對照表

| Analog Signal | Sampled Signal | Z-transformed Signal |
| :--- | :--- | :--- |
| | $A\delta[n]$ | $A$ |
| $Au(t)$ | $Au[n]$ | $\frac{Az}{z - 1}$ |
| $Ae^{-at}u(t)$ | $Ae^{-aTn}u[n]$ | $\frac{Az}{z - e^{-aT}}$ |
| | $Ac^n u[n]$ | $\frac{Az}{z - c}, c = e^{-aT}$ |
| $Atu(t)$ | $AnTu[n]$ | $\frac{ATz}{(z - 1)^2}$ |
| $A\cos(\omega t)u(t)$ | $A\cos(\omega Tn)u[n]$ | $\frac{Az[z - \cos(\omega T)]}{z^2 - 2z\cos(\omega T) + 1}$ |
| $A\sin(\omega t)u(t)$ | $A\sin(\omega Tn)u[n]$ | $\frac{Az\sin(\omega T)}{z^2 - 2z\sin(\omega T) + 1}$ |
| $Ae^{-at}\cos(\omega t + \alpha)u(t)$ | $Ac^n \cos(\omega Tn + \alpha)$ | $\frac{Az[z\cos(\alpha) - c \cos(\alpha - \omega T)]}{z^2 - 2cz\cos(\omega T) + c^2}$ |

## 差分方程式與轉移函數

在連續時間系統中，我們使用微分方程式 (Differential Equation) 來描述系統，並利用拉普拉斯轉換求解；而在離散時間系統中，我們則使用 **差分方程式 (Difference Equation)** 來描述，並透過 Z 轉換來處理。

### Z 轉換的平移特性

這裡先提一下 Z 轉換的平移特性，在時域延遲 $k$ 個單位的 $x[n-k]$，經過 Z 轉換後會乘上 $z^{-k}$，即為 $z^{-k}\cdot X(z)$

#### 證明：
$$
\begin{aligned}
\mathcal{Z}\{x[n-k]\} &= \sum_{n=-\infty}^{\infty} x[n-k] \cdot z^{-n} \\
&\text{let } u = n-k \Rightarrow {\color{#3071c4}n = u+k} \\
\therefore \mathcal{Z}\{x[n-k]\} &= \sum_{{\color{#3071c4}n}=-\infty}^{\infty} x[u] \cdot z^{-{\color{#3071c4}(u+k)}} \\
&= \sum_{{\color{#3071c4}u}=-\infty}^{\infty} x[u] \cdot z^{-u} \cdot {\color{#e53935}z^{-k}} \\
&= z^{-k} \cdot \underbrace{\sum_{u=-\infty}^{\infty} x[u] \cdot z^{-u}}_{{\color{#3071c4}=X(z)}} \\
&= z^{-k} \cdot X(z)
\end{aligned}
$$

#### 簡單總結：

| 原始時域訊號 | Z 轉換後頻域訊號 |
| :--- | :--- |
| $x[n]$ | $X(z)$ |
| $x[n-k]$ | $z^{-k}\cdot X(z)$ |
| $f[n]$ | $F(z)$ |
| $f[n-k]$ | $z^{-k}\cdot F(z)$ |
| $f[n+k]$ | $z^{k}\cdot F(z)$ |

### 舉個栗子|⩊･)ﾉ🌰

有個 DSP 系統，它的差分方程式為：$y[n] -2\cdot y[n-1] = 0.5\cdot x[n-1]$，求它的 Block Diagram 。</br>
畫出來要像下面這樣：
![](w5_1.svg)

我們對差分方程式進行整理：

$$
\begin{aligned}
y[n] -2\cdot y[n-1] = 0.5\cdot x[n-1] \\
\therefore y[n] = 0.5\cdot x[n-1] + 2\cdot y[n-1]
\end{aligned}
$$

於是我們就能畫出：
![](w5_2.svg)

### 轉移函數

系統的轉移函數定義為輸出 $Y(z)$ 與輸入 $X(z)$ 的比值。承接上面的例子，首先對差分方程式進行 Z 轉換，根據上面證明的平移特性，我們可以很快的寫出：

$$
\begin{aligned}
y[n] -2\cdot y[n-1] &= 0.5\cdot x[n-1] \\
& \downarrow \text{ Z-Transform} \\
Y(z) -2\cdot z^{-1}\cdot Y(z) &= 0.5\cdot z^{-1}\cdot X(z) \\
\therefore Y(z) (1-2z^{-1}) &= 0.5\cdot z^{-1}\cdot X(z)
\end{aligned}
$$

這個轉移函數 $T(z)$ 是描述系統特性的核心，它決定了系統面對任何輸入時的反應：
![](w5_3.svg)

$$
{\color{#e53935}T(z)}\equiv\frac{Y(z)}{X(z)} = \frac{0.5\cdot z^{-1}}{1-2\cdot z^{-1}}
$$

只要有了 $T(z)$，我們就能輕鬆算出任何輸入對應的結果：$Y(z) = T(z)\cdot X(z)$

_未完待續..._

:::note
部份圖片來源自：陳榮銘 教授 數位訊號處理課程投影片
:::
