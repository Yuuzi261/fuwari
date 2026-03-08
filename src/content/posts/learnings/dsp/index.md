---
title: 數位訊號處理筆記
published: 2026-02-24
description: 有種死到臨頭開始覺得以前的自己實在混的可以，決定把這學期數位訊號處理上的內容做成筆記，希望能幫到未來的我或是其他需要的人
image: ""
tags: [DSP]
category: Learnings
draft: false
lang: zh_TW
---

# 前言

最近突然覺得自己常常耍廢摸魚得過且過感覺未來會暴死，經過深刻的反省之後決定把這學期上課的內容整理成筆記放到 blog 裡，希望這個系列能夠安穩落地。因為目前才剛開始教所以分類會有些隨便，以後滾動調整吧～

# Week1

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

> _以後想到再補圖_

# Week2

## 訊號取樣的影響 (Effect of Signal Sampling)

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

> _施工中..._

:::note
部份圖片來源自：陳榮銘 教授 數位訊號處理課程投影片
:::