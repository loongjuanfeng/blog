---
title: "Softmax"
date: 2026-07-27T10:27:37+08:00
draft: true
math: true
---

Softmax 的初级笔记。

# 初衷

经常能看到

$$
\begin{gathered}
Q = X W_Q, K = X W_K, V = X W_V \\\\
\mathrm{Attention}(Q, K, V) = \mathrm{softmax} \left(\frac{QK^T}{\sqrt{d_k}}\right)V
\end{gathered}
$$
