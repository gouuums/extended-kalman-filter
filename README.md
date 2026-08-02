# Extended Kalman Filter (EKF) project report: Bearings-only tracking

**Authors:** Thomas GOUMONT — Léo GRUCHOCIAK
**Date:** January 26, 2026
**Institution:** ENSAI (Filtrage linéaire et non-linéaire - Projet - Sujet n°1)

## 📋 Overview

This repository contains the final project report and code for a Filtering methods course, focusing on Target Motion Analysis (TMA), specifically addressing the problem of Bearings-Only Tracking (BOT) using state-space model inference techniques.

Using Python/R workflows and the Extended Kalman Filter (EKF), the project evaluates how noisy angular measurements from one or multiple sensors can be used to reconstruct a moving target's kinematics under a Nearly Constant Velocity (NCV) model.

## 🗂️ Project Structure & Key Sections

1. **Introduction and Problem Description**
   - Formulation of the target tracking problem over a discrete time horizon $k \in \{1, \dots, 500\}$.
   - **State Vector** ($\mathbf{x}_k$): Four-dimensional kinematics describing 2D position $(x_k, y_k)$ and velocity $(\dot x_k, \dot y_k)$.
   - **Dynamic Model (NCV):** Linear state equation driven by a Gaussian white noise process $\mathbf{q}_k \sim \mathcal{N}(0, Q)$.
   - **Measurement Model:** Non-linear angular (azimuth/bearing) observations $\theta_k^i = \arctan\left(\frac{y_k - s_y^i}{x_k - s_x^i}\right) + \mathbf{r}_k$ with Gaussian noise $\mathbf{r}_k \sim \mathcal{N}(0, \sigma^2)$ where $\sigma = 0.05$.

2. **Single-Sensor Target Trajectory Estimation**
   - **Data Simulation:** Simulated $n=500$ observations with Sensor 1 located at $(-1.5, 0.5)$ and initial state $\mathbf{x}_1 = (0, 0, 1, 0)^T$.
   - **Filter Selection:** Explaining why a standard linear Kalman Filter fails due to the non-linear arctangent measurement function, motivating the use of the Extended Kalman Filter (EKF) via first-order Taylor series linearization (Jacobian $C_\theta$).
   - **EKF Implementation:** Initialized with a perturbed estimate and covariance matrix $P_1 = I_4$. Implemented prediction and update steps, including angular normalization via $\text{atan2}(\sin(\cdot), \cos(\cdot))$ to handle phase discontinuities.
   - **Results & Limitations:** Highlighted the loss of range/distance observability with a single static sensor, resulting in stretched trajectory estimates along the sensor-target line-of-sight and increasing RMSE over time.

3. **Multi-Sensor Extension: The Two-Sensor Case**
   - Expanded the observation model to incorporate a second angular sensor at $(1, 1)$.
   - Re-derived the vector Jacobian $H_k \in \mathbb{R}^{2 \times 4}$ by stacking individual sensor gradients.
   - **Performance Comparison:** Demonstrated that adding a second sensor successfully resolves the geometric ambiguity, constrains range uncertainty, stabilizes error bounds within $\pm 3\sigma$, and achieves a stable, non-diverging RMSE profile.

## 📦 Requirements & Libraries

The project environment requires the following packages (primarily utilized in R Markdown format within the source report):

- `MASS` (for multivariate normal simulations)
- `ggplot2`
- `dplyr`
- `gridExtra`

## 📊 Key Findings & Conclusion

- **Linearity vs. Non-linearity:** While the NCV process model is linear-Gaussian, the bearings-only measurement equation introduces severe non-linearities that make standard Kalman filtering impossible.
- **Observability Issues (Single Sensor):** A single bearing-only sensor cannot render target range fully observable without sensor maneuver, leading to progressive error divergence over long horizons.
- **Multi-Sensor Benefits:** Integrating a second sensor effectively restores full observability, yielding stable variance estimates, well-behaved residuals, and robust long-term tracking performance.

## 📄 References

1. S. El Kolei, *Filtering methods course*, Third year, ENSAI, 2025.
2. N. Merlinge, J. Marzat, L. Reboul, *Optimal guidance and observer design for target tracking using bearing-only measurements*, ONERA, 2016.
3. T. Kailath, A.H. Sayed, B. Hassibi, *Linear estimation*, Chapitre 9, section 9.7.1, p.338-342, 2000.
4. Y. Bar-Shalom, X. Li, T. Kirubarajan, *Estimation with Applications to Tracking and Navigation: Theory Algorithm and Software*, John Wiley & Sons, 2004.
