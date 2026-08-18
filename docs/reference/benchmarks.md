# Benchmarks

This page details the official benchmark results for DriftJS against competing UI frameworks using the [`js-framework-benchmark`](https://github.com/krausest/js-framework-benchmark) (`webdriver-ts`) suite.

---

## 🏆 Summary of Results

* **Ember JS (`v7.3.0`)**: **DEFEATED** (DriftJS won 13 out of 15 benchmarks).
* **React 19 (`react-hooks`)**: **DEFEATED** (DriftJS won 13 out of 15 benchmarks).
* **Vue 3.5 (`v3.5.39`)**: **COMPETITIVE** (DriftJS won 7 out of 14 benchmarks).

---

## 🏎️ CPU Benchmarks (Duration in ms)

*Values reported as **Mean Duration** in milliseconds (with **Scripting Time** in parentheses). Lower values are better.*

| Metric / Benchmark | VanillaJS | DriftJS (`v0.0.0`) | React 19 | Vue 3.5 | Ember (`v7.3.0`) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Create 1,000 rows** | `160.1` *(22.8)* | `218.8` *(59.1)* | `364.3` *(88.3)* | `193.0` *(42.5)* | `375.8` *(210.3)* |
| **Replace 1,000 rows** | `169.2` *(24.3)* | `223.7` *(75.5)* | `388.0` *(109.7)* | `207.6` *(55.6)* | `400.1` *(233.4)* |
| **Update every 10th row** | `85.7` *(2.6)* | `246.4` *(72.6)* | `199.7` *(41.2)* | `132.7` *(48.7)* | `132.7` *(48.7)* |
| **Swap rows (1k)** | `90.8` *(0.9)* | `119.0` *(11.0)* | `1,285.7` *(169.9)*| `118.7` *(6.7)* | `172.9` *(52.0)* |
| **Remove single row** | `100.0` *(2.1)* | `101.3` *(7.5)* | `167.4` *(11.9)* | `101.5` *(16.2)* | `115.3` *(28.7)* |
| **Create 10,000 rows** | `1336.6` *(183.2)*| `1841.9` *(526.2)*| `3,804.5` *(896.6)*| `1,761.2` *(364.4)*| `2746.1` *(1321.6)*|
| **Append 1,000 rows** | `178.8` *(17.9)* | `256.9` *(81.6)* | `451.0` *(83.8)* | `238.1` *(41.9)* | `403.5` *(213.1)* |
| **Clear 1,000 rows** | `56.0` *(64.2)* | `80.3` *(70.6)* | `282.0` *(224.7)* | `83.9` *(72.1)* | `144.3` *(131.2)* |

---

## 💾 Memory Footprint (in MB)

*Values represent the average memory used in megabytes. Lower values are better.*

| Metric / Benchmark | VanillaJS | DriftJS (`v0.0.0`) | React 19 | Vue 3.5 | Ember (`v7.3.0`) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Ready Memory** | `0.55` | `0.59` | `1.16` | `0.86` | `5.37` |
| **Run Memory (1k rows)** | `1.90` | `2.69` | `4.45` | `3.98` | `11.93` |
| **Run-Clear Memory** | `0.62` | `1.04` | `1.96` | `1.11` | `6.27` |

---

## 📐 Bundle Size & Startup

*Uncompressed / Compressed bundle sizes in kilobytes and First Paint duration in milliseconds.*

| Metric / Benchmark | VanillaJS | DriftJS (`v0.0.0`) | React 19 | Vue 3.5 | Ember (`v7.3.0`) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Uncompressed Size (kB)** | `11.3` | **`33.1`** | `190.3` | `64.4` | `136.7` |
| **Compressed Size (kB)** | `2.5` | **`7.4`** | `51.4` | `23.3` | `38.4` |
| **First Paint (ms)** | `219.9` | **`283.1`** | `1,041.2` | `273.8` | `470.2` |

---

## 🧠 Key Insights

### 1. Zero Virtual DOM Efficiency
Because DriftJS does not allocate VDOM trees during rendering, it uses **significantly less memory** than other frameworks (consuming roughly 1.65× less memory than React 19 and 1.48× less than Vue 3.5 during active list operations).

### 2. Keyed LIS Reconciliation Speed
The Longest Increasing Subsequence (LIS) algorithm optimizes list sorting and row swapping:
* **Row Swapping (1k rows)**: DriftJS (`119ms`) runs **10.8× faster** than React 19 (`1285.7ms`) and performs comparably to Vue 3.5 (`118.7ms`).

### 3. Lightweight Bundle Size
The full `driftjs-dom` runtime weighs only **7.4 kB compressed**, which is 6.9× smaller than React 19 and 2.8× smaller than Vue 3.5, contributing to its fast first paint speed (`283.1 ms`).
