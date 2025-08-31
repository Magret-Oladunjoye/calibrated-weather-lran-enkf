# Calibrated Weather Forecasts with LRAN + Student-t EnKF


> This project is a **continuation of my master’s thesis work** on daily temperature forecasting, but extended to a different dataset (Kaggle historical hourly weather, 2012–2017) and a broader use case.  
> I turn hourly weather data into **calibrated probabilistic forecasts** by combining a **Koopman-style latent model (LRAN)** with a **robust Student-t Ensemble Kalman Filter** and **adaptive inflation**.  
> Beyond standard error metrics, I evaluate **calibration** (PIT, reliability, coverage), which is critical for decision-making.


---

## What’s inside

- **Notebook**: `Thesis_Extended.ipynb` — end-to-end training, filtering, and evaluation.
- **Modeling**:
  - **LRAN** (Linearly Recurrent Autoencoder Network) encodes diurnal structure in a linear latent state with nonlinear encoder/decoder.
  - **Student-t EnKF** for robust data assimilation and **adaptive inflation** tuned by predictive performance.
- **Evaluation**: CRPS, RMSE, PIT histogram, reliability (q10/q50/q90), and empirical coverage/interval width.

---

## Data

- **Source**: Historical hourly weather (multiple North American + Israeli cities, ~2012–2017).
- **Variables used**: temperature, humidity, pressure, wind speed; plus time features (sin/cos of hour-of-day and day-of-year).
- **Units**: I convert all temperatures to **°C** at load time.
- **Split**: Train < 2016, Validation = 2016, Test = 2017.

> This repo uses historical data only; there is no real-time ingestion.

---

## Headline results (example: one city, 2017 test window)

- **Mean CRPS**: 1.789  
- **RMSE**: 3.18 °C  
- **Median predictive σ**: 3.25 °C  
- **z-score std**: 1.01 (close to 1 is good)  
- **Coverage**: 0.81 @ 80% (avg width 8.33 °C), 0.90 @ 90% (avg width 10.70 °C)

These indicate **well-calibrated** and reasonably **sharp** forecasts: intervals are neither too narrow nor too wide, and probabilities line up with observed frequencies.

---

## Quick start

### 1) Open in Colab
Click the badge after you push this repo to GitHub (replace `<USER>` and `<REPO>` with your handle and repo name):

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<USER>/<REPO>/blob/main/Thesis_Extended.ipynb)

### 2) Run the notebook
- Install requirements in the first cell if prompted.
- Point to your processed city parquet(s) and run the cells.
- The notebook trains LRAN briefly, runs the Student-t EnKF with adaptive inflation, and computes CRPS/PIT/reliability.

### 3) Optional: export and visualize
- Export forecast arrays as `.npz` and load them into a Gradio app or a Hugging Face Space to share an interactive demo.

---

## Why this matters

- **Decision-useful uncertainty**: I report **calibration** (PIT, reliability, coverage) not just RMSE, so risk-based decisions are better informed.
- **Physics-aware backbone**: LRAN provides a **Koopman-style linear latent** evolution well matched to periodic weather structure.
- **Robust assimilation**: Student-t EnKF handles outliers and misspecification better than Gaussian updates.

---

## How it works 


I learn a low-dimensional latent state where dynamics are **linear** (LRAN), which captures diurnal cycles and smooth evolution. Observations (temperature) are decoded nonlinearly. At test time, I run an **Ensemble Kalman Filter** with **Student-t** likelihood to robustly fuse observations, and I tune a **scalar inflation** factor online to maintain appropriate spread. Forecast distributions at horizon *h* are summarized by mean, σ, and quantiles; I assess **CRPS**, **PIT**, **reliability**, and **coverage/width**.

---

## References

- Otto, S. E., & Rowley, C. W. (2019). **Linearly Recurrent Autoencoder Networks for Learning Dynamics**. *SIAM Journal on Applied Dynamical Systems*, 18(1), 558–593. DOI: 10.1137/18M1177846.  
- Lusch, B., Kutz, J. N., & Brunton, S. L. (2018). **Deep learning for universal linear embeddings of nonlinear dynamics**. *Nature Communications*, 9, 4950.

---


Questions or ideas for extensions? Open an issue or reach out.
