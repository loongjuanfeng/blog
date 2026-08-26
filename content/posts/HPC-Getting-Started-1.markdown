---
title: "HPC-Getting-Started #1 - GEMM"
date: 2026-07-27T14:13:15+08:00
draft: true
math: true
---

关于 GEMM (mat_mul) 部分。以下全部假设 $A, B, C \in \mathbb{R}^{n \times n}$

# 朴素的 GEMM 实现

例如一个 $\mathcal{O}(n^3)$ 的朴素实现：

```cpp
for (int i = 0; i < n; i ++) {
for (int j = 0; j < n; j ++) {
for (int k = 0; k < n; k ++) {
  C[i][j] += A[i][k] * B[k][j];
} // k
} // j
} // i
```

```text
n = 1024, time = 1.3437s
```

然而在真实算法中，矩阵（或者张量）的底层都是一维数据（甚至是 `const void*`）。这个时候需要考虑访存的连续性。

使用一维的数据形式重写上面的代码（`A, B, C` $\in \mathbb{R}^{n^2}$）：

```cpp {hl_lines=[4]}
for (int i = 0; i < n; i ++) {
for (int j = 0; j < n; j ++) {
for (int k = 0; k < n; k ++) {
  C[i*n + j] += A[i*n + k] * B[k*n + j];
} // k
} // j
} // i
```

会发现最内层 `for(k)` 的 `B[k*n + j]` 访存连续性很差，这个时候就可以交换循环顺序。

```cpp {hl_lines=[2,3,5,6]}
for (int i = 0; i < n; i ++) {
for (int k = 0; k < n; k ++) {
for (int j = 0; j < n; j ++) {
  C[i*n + j] += A[i*n + k] * B[k*n + j];
} // j
} // k
} // i
```

没有任何的代价，仅仅是交换了循环顺序，就让访存变为连续的了。然后发现 `A[i*n + k]` 在内层循环内没有改变，每次都读·内存很不划算，于是取出。（现代编译器一般能自动进行这个优化）

```cpp {hl_lines=[3,5]}
for (int i = 0; i < n; i ++) {
for (int k = 0; k < n; k ++) {
  auto A_ik = A[i*n + k];
for (int j = 0; j < n; j ++) {
  C[i*n + j] += A_ik * B[k*n + j];
} // j
} // k
} // i
```

```text
n = 1024, time =  0.0928s, bandwidth = 0.1418 GB/s, time improvement: 14.5x
n = 2048, time =  1.0045s, bandwidth = 0.0501 GB/s, bandwidth drop: 64%
n = 4096, time = 13.7337s, bandwidth = 0.0147 GB/s, bandwidth: 9.6x slower than n = 1024
```

然而，在面对 $n \ge 1024$ 这种量级的数据后，L3 cache 已经无法装下这些数据了（本机 L3 cache 24MiB），此时就需要分块。

```cpp {hl_lines=[1,2,3,4,6,7,8,19,20,21]}
const int tile_size = 128; // can be { 32, 64, 128, 256, 512 }
for (int ii = 0; ii < n; ii += tile_size) {
for (int jj = 0; jj < n; jj += tile_size) {
for (int kk = 0; kk < n; kk += tile_size) {

  const int i_end = min(n, ii + tile_size),
            j_end = min(n, jj + tile_size),
            k_end = min(n, kk + tile_size);

  for (int i = ii; i < i_end; i ++) {
  for (int k = kk; k < k_end; k ++) {
    const auto A_ik = A[i*n + k];
  for (int j = jj; j < j_end; j ++) {
    C[i*n + j] += A_ik * B[k*n + j];
  } // j
  } // k
  } // i

} // kk
} // jj
} // ii
```

这个时候就可能涉及到设备微调的问题了，需要进行 config sweep，经过实验，`tile_size = 128` 就是最优的。

顺手进行一个向量化，可能带来额外性能的提升。

```cpp {hl_lines=[4]}
  for (int i = ii; i < i_end; i ++) {
  for (int k = kk; k < k_end; k ++) {
    const auto A_ik = A[i*n + k];
#pragma omp simd
  for (int j = jj; j < j_end; j ++) {
    C[i*n + j] += A_ik * B[k*n + j];
  } // j
  } // k
  } // i
```




















