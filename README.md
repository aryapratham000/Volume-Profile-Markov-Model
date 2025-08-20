# ES Volume Profile Markov Model

## Overview
This project develops a framework to **classify, analyze, and forecast ES (E-mini S&P 500) session structures** using daily **volume profiles**. It combines profile-based regime labeling, transition statistics, and machine learning benchmarks to evaluate predictability in short-memory market dynamics

## Goal
Predict the **next session type** from ES daily **volume-profile structure**

## Methodology
### 1. Data & Sessionization
- Source: **1-minute ES futures bars** (2020–2025).  
- Sessions defined as **09:30–09:29 ET** to align with RTH structure.  
- Each session tagged with a `SessionDate`.

### 2. Volume Profile Construction
- **55-bin profile** per day.  
- Compute **POC (Point of Control)**, **VAH (Value Area High)**, and **VAL (Value Area Low)** using 70% value area.  
- Typical price = (High + Low + Close) / 3 used for volume assignment.

### 3. Session Classification
Daily sessions are labeled relative to the prior day:
- **Bullish Gap**  
- **Bearish Gap**  
- **Expansion**  
- **Contraction**  
- **Bullish Overlap**  
- **Bearish Overlap**

### 4. Statistical Analysis
- **Frequency & Cluster Stats:** Base rates, streaks, max cluster lengths.  
- **Markov Chains:** Transition probabilities at 1st, 2nd, and 3rd order.  
- **Visualization:** Heatmaps for transition counts and probabilities.

### 5. Rule Extraction
- Extract **high-confidence 3-step rules** where  
  - `Max_Prob ≥ 0.60`  
  - `Count ≥ 5`  
- Example: *Expansion → Bearish Gap → Bearish Overlap → Bearish Overlap* with 86% confidence.

### 6. Predictive Modeling
- Encode session types with `LabelEncoder`.  
- Train **RandomForestClassifier** using lag features (lags 1–20).  
- Evaluate with **TimeSeriesSplit cross-validation** across multiple seeds.  
- Benchmarks: random baseline and majority-class baseline.

## Key Insights

- **Dominant Structures:** Overlaps (43%) and Gaps (39%) dominate; Expansion/Contraction are rare (~17%).  
- **Short Streaks:** Clusters average ~2 days; maximum streaks capped at 4.  
- **Magnet State:** Bearish Overlap frequently emerges as the predicted regime across high-confidence rules.  
- **Predictive Power:**  
  - Shallow lags (1–2) underperform random.  
  - Accuracy improves with deeper lags (up to 0.44 at lag 16–19).  
  - Still trails majority-class baseline (0.456), highlighting imbalance and regime complexity.

## Outputs
- Daily volume profile plots with **VAH/VAL/POC**.  
- Frequency and cluster summary tables.  
- Transition count/probability matrices (heatmaps).  
- High-confidence 3-step session rules.  
- Accuracy vs lag depth plots for predictive benchmarks.  

## Usage
This framework is designed for:
- **Market regime classification**  
- **Signal gating** (act only in high-confidence contexts)  
- **Risk sizing** conditioned on predicted session type  
- **Research tool** for evaluating short-memory structures in futures markets  


## Limitations & Next Steps
- Class imbalance favors overlaps/gaps; models underperform majority baseline.  
- Support for high-confidence rules is thin; requires more data for robustness.  
- Future work:  
  - Test alternative models (HMM, LSTMs).  
  - Incorporate **regime classification** (trend, rotation, expansion) as features.  

