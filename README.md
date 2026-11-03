# ALeRCE-ZTFLSST-HubbleConstant: Cosmological $H_0$ Inference from ZTF Type Ia Supernovae & Early LSST Alert Vetting

[![Event: ALeRCE Hackathon 2026](https://img.shields.io/badge/ALeRCE%20Hackathon-March%202026-2EA44F)](https://science.alerce.online/)
[![ALeRCE Science Portal](https://img.shields.io/badge/Portal-science.alerce.online-00599C)](https://science.alerce.online/)
[![Surveys: ZTF & LSST](https://img.shields.io/badge/Surveys-ZTF%20%26%20Rubin%20LSST-6B46C1)](https://alerce.online/)
[![Cosmology: Astropy & sncosmo](https://img.shields.io/badge/Cosmology-Astropy%20%7C%20sncosmo-F37626)](https://sncosmo.readthedocs.io/)
[![Catalog: IAU TNS](https://img.shields.io/badge/Cross--Match-IAU%20TNS-1D76DB)](https://www.wis-tns.org/)

**Authors:** [Steve Jurado](https://github.com/jurados) ([@jurados](https://github.com/jurados)) & [Xaviera Julio](https://github.com/Xavissss) ([@Xavissss](https://github.com/Xavissss))  
**Event & Context:** Developed during the **[ALeRCE Hackathon 2026](https://science.alerce.online/)** (*Automatic Learning for the Rapid Classification of Events* — [https://science.alerce.online/](https://science.alerce.online/))  
**Date / Last Updated:** March 2026

---

## Overview

**`ALeRCE-ZTFLSST-HubbleConstant`** is an end-to-end observational cosmology and time-domain astrophysics project created during the **March 2026 ALeRCE Hackathon** ([science.alerce.online](https://science.alerce.online/)). The primary objective of this work is to **measure the Hubble Constant ($H_0$)** directly from wide-field astronomical broker streams by combining **Zwicky Transient Facility (ZTF)** Type Ia Supernova (`SNIa`) light curves classified by the **ALeRCE** broker with spectroscopic redshifts from the **IAU Transient Name Server (TNS)**.

By standardizing **$N = 1{,}442$ Type Ia Supernovae** at peak brightness via the Phillips decline-rate relation ($\Delta m_{15}$), **SFD98** Galactic dust extinction corrections, and **`sncosmo`** cross-filter K-corrections, we construct a Hubble–Lemaître diagram yielding **$H_0 = 68.16 \pm 0.16\,\mathrm{km\,s^{-1}\,Mpc^{-1}}$ (stat)** in flat $\Lambda\mathrm{CDM}$. Complementing our ZTF cosmological analysis, we benchmark the **ALeRCE Stamp Classifier** on **$1{,}694$ visually inspected Vera C. Rubin Observatory (LSST)** alerts and $200$ early LSST supernova candidates to define high-purity selection cuts for next-generation Rubin cosmology.

---

## 1. Scientific Motivation & The $H_0$ Tension

Type Ia supernovae (SNe Ia)—thermonuclear disruptions of carbon–oxygen white dwarfs approaching the Chandrasekhar mass limit—serve as precision **standardizable candles** across cosmological distances. Yet, modern observational cosmology faces a persistent $\sim 5\sigma$ discrepancy known as the **Hubble Tension** between:
- **Early-Universe / CMB + BAO Constraints:** Inferred assuming flat $\Lambda\mathrm{CDM}$ from the Cosmic Microwave Background (*Planck* 2018: $H_0 = 67.4 \pm 0.5\,\mathrm{km\,s^{-1}\,Mpc^{-1}}$; ACT-DR6 2025: $67.0 \pm 0.7$; BBN + DESI BAO 2024: $68.5 \pm 0.6$).
- **Late-Universe Direct Distance Ladder:** Calibrated locally via Cepheids and Tip of the Red Giant Branch (TRGB) anchors (HST SH0ES 2024: $H_0 = 73.0 \pm 1.0\,\mathrm{km\,s^{-1}\,Mpc^{-1}}$; JWST SH0ES 2024: $73.4 \pm 1.2$).

As sky surveys transition from ZTF ($\sim 10^6$ alerts/night) to the **Vera C. Rubin Observatory Legacy Survey of Space and Time (LSST; $\sim 10^7$ alerts/night)**, cosmological SN Ia samples are increasingly discovered, classified, and prioritized in real time by community brokers such as **[ALeRCE](https://science.alerce.online/)**. This work demonstrates how to bridge **automated broker classification (ZTF + LSST)** with **cosmological parameter inference ($H_0$)**.

---

## 2. End-to-End Cosmological & Broker Pipeline

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│                 STAGE 1: BROKER QUERY & TNS CROSS-MATCHING                   │
│  ALeRCE ZTF lc_classifier (class='SNIa', p >= 0.3)                           │
│  ──► Multi-band PSF Photometry (fid=1: g, fid=2: r)                          │
│  ──► Spherical KD-Tree Cross-Match vs. IAU TNS Catalog (sep <= 2.0 arcsec)   │
│  ──► 1,466 spectroscopically anchored SNe Ia (df_snia_with_tns_match*.pkl)   │
└──────────────────────────────────────┬───────────────────────────────────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│              STAGE 2: PARAMETRIC LIGHT-CURVE FITTING (Δm15 & Tmax)           │
│  Piecewise / Bazin-like Rise & Decline Model + 6th-Degree Polynomial Fit     │
│  ──► Rest-frame Peak Epoch (Tmax ± e_Tmax) & 15-day Decline Rate (Δm15)      │
└──────────────────────────────────────┬───────────────────────────────────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│           STAGE 3: PHILLIPS STANDARDIZATION TO REST-FRAME B BAND             │
│  • Stretch Parameterization:  s = 1.24 - 0.18 * Δm15  (α = 0.60)             │
│  • Galactic Dust Extinction:  Schlegel, Finkbeiner & Davis (SFD98; R_g=3.2)  │
│  • Spectral K-Correction:     sncosmo Hsiao SN Ia Template (sdssg -> bessellb)│
│  ──► Standardized Peak Magnitude mB_eff ± e_mB_eff for N = 1,442 SNe Ia      │
└──────────────────────────────────────┬───────────────────────────────────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│           STAGE 4: HUBBLE DIAGRAM & FLAT ΛCDM χ² MINIMIZATION (H0)           │
│  μ_model(z; H0, Ωm=0.30) + M0 (-19.3)  ──►  H0 = 68.16 ± 0.16 km s⁻¹ Mpc⁻¹   │
│  ──► Direct Comparison with Early-Universe (Planck/DESI) vs. Late (SH0ES)    │
└──────────────────────────────────────┬───────────────────────────────────────┘
                                       ▼
┌──────────────────────────────────────────────────────────────────────────────┐
│             STAGE 5: NEXT-GEN LSST STAMP CLASSIFIER BENCHMARKING             │
│  1,694 Visually Labeled LSST Alerts + 200 Top LSST SN Candidates             │
│  ──► Confusion Matrix & Astrometric/Photometric Quality Cuts (σ_sky <= 0.15")│
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Stage I — ALeRCE ZTF Query & TNS Spherical Cross-Matching

Candidate Type Ia supernovae are queried from the **ALeRCE ZTF Client** (`alerce.core.AlErce`) filtering by the Balanced Random Forest light-curve classifier (`lc_classifier`, taxonomy class `SNIa`, probability $P(\mathrm{SNIa}) \ge 0.3$). For every candidate `oid`, the pipeline downloads the multi-epoch PSF photometry (`magpsf`, `sigmapsf`, `mjd`, filter ID `fid`) and computes inverse-variance weighted astrometric centroids $(\bar{\alpha}, \bar{\delta})$.

To anchor each photometric light curve with an unambiguous spectroscopic redshift $z_{\mathrm{TNS}}$ and classification, the ZTF centroids are cross-matched against the **IAU Transient Name Server (`tns_public_objects_260311.csv.zip`)** on the unit sphere $\mathbb{S}^2$ via `astropy.coordinates.SkyCoord.match_to_catalog_sky`:

$$\Delta\theta = \arccos\!\left[\sin\bar{\delta}\sin\delta_{\mathrm{TNS}} + \cos\bar{\delta}\cos\delta_{\mathrm{TNS}}\cos(\bar{\alpha} - \alpha_{\mathrm{TNS}})\right] \le 2.0''$$

This produces **$50{,}065$ photometric detections across $1{,}466$ unique ZTF SNe Ia** matched to $1{,}465$ TNS designations (`df_snia_with_tns_match_plus_dm15.pkl`), alongside a clean benchmark subset of $130$ local Universe SNe Ia (`df_snia_with_tns_match.csv`, $0.015 \le z \le 0.125$ with sub-arcsecond median separation $\langle\Delta\theta\rangle = 0.24''$).

---

## 4. Stage II — Parametric Light-Curve Modeling ($\Delta m_{15}$ & $T_{\max}$)

Because astronomical survey cadence is irregular and interrupted by weather or lunar phase, the true peak epoch $T_{\max}$ and post-maximum decline rate $\Delta m_{15}$ (the magnitude drop between $T_{\max}$ and $T_{\max} + 15\,\mathrm{d}$ in the rest frame) must be extracted from continuous parametric fits:

1. **Piecewise Exponential Rise & Linear Late-Time Decline Model:**
   Converts observed AB magnitudes $m_{\mathrm{PSF}}$ into flux densities $F_{\nu} = 3631 \times 10^{-0.4\,m_{\mathrm{PSF}}}\,\mathrm{Jy}$ and fits an analytic Bazin-like rise with an exponential/linear cobalt-decay tail:

   $$m_{\mathrm{model}}(t) = \begin{cases} A - \beta(t - t_0) - 2.5\log_{10}\!\left(\dfrac{e^{-(t - t_0)/\tau_{\mathrm{fall}}}}{1 + e^{-(t - t_0)/\tau_{\mathrm{rise}}}}\right) + c, & t < t_1 \\[10pt] A - \gamma(t - t_0) - 2.5\log_{10}\!\left(\dfrac{e^{-(t_1 - t_0)/\tau_{\mathrm{fall}}}}{1 + e^{-(t_1 - t_0)/\tau_{\mathrm{rise}}}}\right) + c, & t \ge t_1 \end{cases}$$

2. **Inverse-Variance Weighted Polynomial Interpolation (`ZTF25abqamzb_lightcurve.csv`):**
   Fits a continuous degree-$K=6$ polynomial across a 500-point MJD grid around peak brightness by minimizing:

   $$\chi^2(\mathbf{c}) = \sum_{i=1}^{N_b} \left(\frac{m_{b,i} - \sum_{k=0}^{6} c_{b,k}\,t_i^k}{\sigma_{m,b,i}}\right)^2$$

<p align="center">
  <img src="assets/figs/ztf25abqamzb_poly_fit.png" width="88%" alt="ZTF25abqamzb Multi-Band Light Curve Fit"/>
</p>

| ZTF Object ID | Filter (`fid`) | Photometric Epochs ($N_{\mathrm{det}}$) | Peak Epoch $\widehat{t}_{\mathrm{peak}}$ [MJD] | Peak Apparent Magnitude $\widehat{m}_{\mathrm{peak}}$ [AB mag] | Observed Magnitude Range [AB mag] |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **`ZTF25abqamzb`** | $g$ (`fid = 1`) | $13$ | $60940.76$ | $18.61 \pm 0.09$ | $18.61 \to 20.97$ |
| **`ZTF25abqamzb`** | $r$ (`fid = 2`) | $20$ | $60941.65$ | $18.51 \pm 0.07$ | $18.49 \to 20.15$ |

> **Physical Interpretation:** The $g$-band light curve peaks $\Delta t \approx 0.89\,\mathrm{days}$ earlier than the $r$-band and exhibits a steeper post-maximum decline ($\Delta m_g \approx 2.36\,\mathrm{mag}$ over $32\,\mathrm{days}$ vs. $\Delta m_r \approx 1.66\,\mathrm{mag}$ over $49\,\mathrm{days}$), a classic signature of **$\mathrm{^{56}Ni} \to \mathrm{^{56}Co} \to \mathrm{^{56}Fe}$** radiative cooling and iron-group line blanketing in the blue optical passband.

---

## 5. Stage III — SN Ia Standardization ($m_B^{\mathrm{eff}}$), K-Corrections & Dust Extinction

To place each supernova on a uniform cosmological distance scale, raw observed green-band magnitudes $m_{\mathrm{obs}}$ are transformed into **standardized rest-frame Bessel $B$-band peak magnitudes ($m_B^{\mathrm{eff}}$)** through three physical corrections:

### 5.1 Phillips Decline-Rate to Stretch Standardization ($s$)
Slowly declining SNe Ia (smaller $\Delta m_{15}$) are intrinsically more luminous (Phillips 1993). We convert the measured decline rate $\Delta m_{15} \pm \sigma_{\Delta m_{15}}$ into the dimensionless stretch factor $s$:

$$s = a + b\,\Delta m_{15}, \qquad \sigma_s = |b|\,\sigma_{\Delta m_{15}} \qquad (a = 1.24,\; b = -0.18)$$

and apply a width–luminosity slope $\alpha = 0.60$.

### 5.2 Milky Way Galactic Dust Extinction ($A_{\mathrm{gal}}$)
Line-of-sight Galactic reddening $E(B-V)$ is queried at each supernova's celestial coordinates $(\alpha, \delta)$ from the **Schlegel, Finkbeiner & Davis (1998; SFD98)** infrared dust map via `dustmaps.sfd.SFDQuery`:

$$A_{\mathrm{gal}} = R_g \, E(B-V)_{\mathrm{SFD}}, \qquad \sigma_{A_{\mathrm{gal}}} = 0.10\,A_{\mathrm{gal}} \qquad (R_g = 3.2)$$

### 5.3 Spectral Cross-Filter K-Correction ($K_{Bg}$)
Because cosmological redshift stretches the supernova spectrum across filter boundaries, we use **`sncosmo`** with the **Hsiao et al. (2007) SN Ia spectral template (`source='hsiao'`)** at peak phase ($t = 0$) to compute the exact cross-filter K-correction between observed SDSS/ZTF $g$-band (`sdssg`) at redshift $z$ and rest-frame Bessel $B$-band (`bessellb`):

$$K(z) = m_{\mathrm{AB}}^{\mathrm{Hsiao}}(\mathrm{sdssg},\, z,\, t=0) - m_{\mathrm{AB}}^{\mathrm{Hsiao}}(\mathrm{bessellb},\, z=0,\, t=0), \qquad \sigma_K = 0.02\,\mathrm{mag}$$

### 5.4 Master Standardization & Quadrature Error Propagation
Combining all three terms yields the effective rest-frame $B$-band magnitude across **$N = 1{,}442$ unique SNe Ia at peak brightness**:

$$m_B^{\mathrm{eff}} = m_{\mathrm{obs}} + \alpha(s - 1) - K(z) - A_{\mathrm{gal}}$$

$$\sigma_{m_B^{\mathrm{eff}}} = \sqrt{\sigma_{m_{\mathrm{obs}}}^2 + (\alpha\,\sigma_s)^2 + \sigma_K^2 + \sigma_{A_{\mathrm{gal}}}^2 + \sigma_{\mathrm{int}}^2}, \qquad \sigma_{\mathrm{int}} = 0.17\,\mathrm{mag}$$

---

## 6. Stage IV — Hubble–Lemaître Diagram & $\chi^2$ Cosmological Fit ($H_0$)

In a spatially flat $\Lambda\mathrm{CDM}$ universe ($\Omega_k = 0$, matter density $\Omega_m = 0.30$, dark energy density $\Omega_\Lambda = 0.70$), the luminosity distance $d_L(z; H_0, \Omega_m)$ and theoretical apparent magnitude $m_{\mathrm{model}}(z; H_0)$ are given by:

$$d_L(z; H_0, \Omega_m) = \frac{c(1+z)}{H_0} \int_0^z \frac{\mathrm{d}z'}{\sqrt{\Omega_m(1+z')^3 + (1 - \Omega_m)}}$$

$$m_{\mathrm{model}}(z; H_0) = M_0 + \mu(z; H_0, \Omega_m) = M_0 + 5\log_{10}\!\left(\frac{d_L(z; H_0, \Omega_m)}{\mathrm{Mpc}}\right) + 25$$

where $M_0 = -19.3\,\mathrm{mag}$ is the fiducial standardized rest-frame $B$-band absolute magnitude of a Type Ia supernova. Minimizing the weighted objective function across the **$N = 1{,}442$ unique peak SNe Ia**:

$$\chi^2(H_0) = \sum_{i=1}^{1442} \left(\frac{m_{B,\mathrm{eff},i} - m_{\mathrm{model}}(z_i; H_0, \Omega_m=0.30)}{\sigma_{m_B^{\mathrm{eff}},i}}\right)^2$$

yields our primary cosmological measurement:

$$\boxed{H_0 = 68.16 \pm 0.16\,\mathrm{(stat)}\,\mathrm{km\,s^{-1}\,Mpc^{-1}}}$$

<p align="center">
  <img src="assets/figs/snia_hubble_diagram.png" width="84%" alt="Type Ia Supernova Hubble Diagram (N=1,442 SNe Ia)"/>
</p>

### 6.1 Summary of Cosmological Fit Parameters

| Cosmological / Sample Parameter | Symbol | Value | Notes / Calibration |
| :--- | :---: | :---: | :--- |
| **Unique SNe Ia in Hubble Diagram** | $N_{\mathrm{SN}}$ | **$1{,}442$** | Selected at peak brightness (`groupby('tns_name')['mB_eff'].idxmin()`) |
| **Redshift Baseline** | $z_{\mathrm{TNS}}$ | $0.015 \le z \le 0.16$ | Spectroscopically anchored via IAU TNS |
| **Fixed Matter Density** | $\Omega_m$ | $0.30$ | Flat $\Lambda\mathrm{CDM}$ ($\Omega_\Lambda = 0.70$) |
| **Fiducial SN Ia Absolute Magnitude** | $M_0$ | $-19.30\,\mathrm{mag}$ | Rest-frame Bessel $B$-band calibration |
| **Intrinsic SN Ia Dispersion** | $\sigma_{\mathrm{int}}$ | $0.17\,\mathrm{mag}$ | Added in quadrature after $\Delta m_{15}$ stretch correction |
| **Best-Fit Hubble Constant** | $\widehat{H}_0$ | **$68.16 \pm 0.16\,\mathrm{km\,s^{-1}\,Mpc^{-1}}$** | Minimum $\chi^2$ optimization |

### 6.2 Contextualizing Our Result within the Hubble Tension

We compare the global landscape of $H_0$ determinations across **Cosmological Model Dependent** probes (CMB + BAO), **Direct Distance Ladder** measurements ($D$ vs. $z$), and **Modeled Astrophysical Phenomena** (Strong Gravitational Lensing, Fast Radio Bursts):

<p align="center">
  <img src="assets/figs/h0_tension_comparison.png" width="82%" alt="Hubble Constant Tension Whisker Comparison"/>
</p>

> **Note on the $M_0 - H_0$ Degeneracy:** Because Hubble-flow supernovae strictly constrain the intercept $\mathcal{M}_B = M_0 - 5\log_{10}(H_0) + 25$, adopting the fiducial absolute magnitude $M_0 = -19.30\,\mathrm{mag}$ (close to inverse-distance-ladder / TRGB-CCHP calibrations) naturally yields $H_0 = 68.16\,\mathrm{km\,s^{-1}\,Mpc^{-1}}$, in remarkable alignment with **BBN + eBOSS 2022 ($68.2 \pm 0.7$)**, **BBN + DESI BAO 2024 ($68.5 \pm 0.6$)**, and **JAGB JWST CCHP ($68.0 \pm 1.5$)**. Shifting the zero-point anchor to the Cepheid SH0ES calibration ($M_0 \approx -19.25\,\mathrm{mag}$) shifts the inferred $H_0$ upward by $\Delta H_0 / H_0 \approx 0.2\ln(10)\,\Delta M_0 \approx +2.3\%$.

---

## 7. Stage V — Preparing for Rubin: LSST Stamp Classifier Audit

To extend this cosmological pipeline to the **Vera C. Rubin Observatory (LSST)**, where early-epoch alerts must be selected before full multi-week light curves are accumulated, we audited the **ALeRCE Stamp Classifier (`stamp_classifier`)** across **$N = 1{,}694$ human-labeled LSST alerts** (`classification_FF_20260309.csv`) and extracted the top $200$ LSST SN candidates (`lsst_sn_candidates.csv`).

### 7.1 Multi-Class Evaluation (`classification_FF_20260309.csv`)

<p align="center">
  <img src="assets/figs/lsst_confusion_matrix.png" width="88%" alt="LSST Stamp Classifier Confusion Matrix"/>
</p>

| Visual Ground-Truth Class | Total Alerts ($N$) | Predicted `SN` | Predicted `BOGUS` | Predicted `VS` | Predicted `AGN` | Predicted `AST` | Class Recall |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`BOGUS`** (Artifacts / ghosts) | $810$ | $226$ | **$465$** | $66$ | $41$ | $12$ | **$57.4\%$** |
| **`VS`** (Variable Stars) | $760$ | $38$ | $27$ | **$422$** | $271$ | $2$ | **$55.5\%$** |
| **`AGN`** (Active Galactic Nuclei) | $96$ | $9$ | $6$ | $26$ | **$55$** | $0$ | **$57.3\%$** |
| **`SN`** (Supernovae) | $24$ | **$16$** | $4$ | $3$ | $1$ | $0$ | **$66.7\%$** |
| **`TDE`** (Tidal Disruption Events) | $3$ | $1$ | $0$ | $1$ | $1$ | $0$ | — |
| **`AST`** (Asteroids) | $1$ | $0$ | $0$ | $1$ | $0$ | **$0$** | $0.0\%$ |

### 7.2 High-Purity LSST SN Candidate Selection Cuts

While the Stamp Classifier recovers **$66.7\%$ (16/24)** of true LSST supernovae from a single alert cutout, $77.9\%$ ($226/290$) of raw `SN`-predicted alerts are false-positive image subtraction artifacts (`BOGUS`). By combining classifier confidence with multi-epoch astrometric scatter $\sigma_{\mathrm{sky}} = 3600\sqrt{\sigma_{\alpha}^2 + \sigma_{\delta}^2}$:

$$\mathcal{C}_{\mathrm{SN-clean}} : \quad \widehat{y}_{\mathrm{stamp}} = \mathtt{SN} \;\land\; P(\mathtt{SN}) \ge 0.45 \;\land\; N_{\mathrm{det}} \ge 2 \;\land\; \sigma_{\mathrm{sky}} \le 0.15''$$

we suppress `BOGUS` contamination while preserving extragalactic transients for spectroscopic follow-up and cosmological Hubble-diagram inclusion.

<p align="center">
  <img src="assets/figs/lsst_sn_quality_cuts.png" width="90%" alt="LSST SN Quality Cuts and Astrometric Scatter"/>
</p>

---

## 8. References

1. **Förster, F., Cabrera-Vives, G., Castillo-Navarrete, E., et al.** (2021), *"The Automatic Learning for the Rapid Classification of Events (ALeRCE) Alert Broker"*, The Astronomical Journal, 161, 242. [doi:10.3847/1538-3881/abe9bc](https://doi.org/10.3847/1538-3881/abe9bc) | [science.alerce.online](https://science.alerce.online/)
2. **Sánchez-Sáez, P., Reyes, I., Valenzuela, C., et al.** (2021), *"Alert Classification for the ALeRCE Broker System: The Light Curve Classifier"*, The Astronomical Journal, 161, 141. [doi:10.3847/1538-3881/abd5c1](https://doi.org/10.3847/1538-3881/abd5c1)
3. **Carrasco-Davis, R., Reyes, E., Valenzuela, C., et al.** (2021), *"Alert Classification for the ALeRCE Broker System: The Real-time Stamp Classifier"*, The Astronomical Journal, 162, 231. [doi:10.3847/1538-3881/ac0ef1](https://doi.org/10.3847/1538-3881/ac0ef1)
4. **Phillips, M. M.** (1993), *"The Absolute Magnitudes of Type IA Supernovae"*, The Astrophysical Journal Letters, 413, L105. [doi:10.1086/186970](https://doi.org/10.1086/186970)
5. **Hsiao, E. Y., Conley, A., Howell, D. A., et al.** (2007), *"K-Corrections and Spectral Templates of Type Ia Supernovae"*, The Astrophysical Journal, 663, 1187. [doi:10.1086/518232](https://doi.org/10.1086/518232)
6. **Schlegel, D. J., Finkbeiner, D. P., & Davis, M.** (1998), *"Maps of Dust Infrared Emission for Use in Estimation of Reddening and Cosmic Microwave Background Radiation Foregrounds"*, The Astrophysical Journal, 500, 525. [doi:10.1086/305772](https://doi.org/10.1086/305772)
7. **Perlmutter, S., Aldering, G., Goldhaber, G., et al.** (1999), *"Measurements of $\Omega$ and $\Lambda$ from 42 High-Redshift Supernovae"*, The Astrophysical Journal, 517, 565. [doi:10.1086/307221](https://doi.org/10.1086/307221)
8. **Riess, A. G., Filippenko, A. V., Challis, P., et al.** (1998), *"Observational Evidence from Supernovae for an Accelerating Universe and a Cosmological Constant"*, The Astronomical Journal, 116, 1009. [doi:10.1086/300499](https://doi.org/10.1086/300499)
9. **Planck Collaboration, Aghanim, N., Akrami, Y., et al.** (2020), *"Planck 2018 results. VI. Cosmological parameters"*, Astronomy & Astrophysics, 641, A6. [doi:10.1051/0004-6361/201833910](https://doi.org/10.1051/0004-6361/201833910)
10. **DESI Collaboration, Adame, A. G., Aguilar, J., et al.** (2024), *"DESI 2024 VI: Cosmological Constraints from the Measurements of Baryon Acoustic Oscillations"*, arXiv:2404.03002. [doi:10.48550/arXiv.2404.03002](https://doi.org/10.48550/arXiv.2404.03002)
11. **Bellm, E. C., Kulkarni, S. R., Graham, M. J., et al.** (2019), *"The Zwicky Transient Facility: System Overview, Performance, and First Results"*, Publications of the Astronomical Society of the Pacific, 131, 018002. [doi:10.1088/1538-3873/aaecbe](https://doi.org/10.1088/1538-3873/aaecbe)
12. **Ivezić, Ž., Kahn, S. M., Tyson, J. A., et al.** (2019), *"LSST: From Science Drivers to Reference Design and Anticipated Data Products"*, The Astrophysical Journal, 873, 111. [doi:10.3847/1538-4357/ab042c](https://doi.org/10.3847/1538-4357/ab042c)

---
*Last Updated: March 2026 — [ALeRCE Science & Hackathon 2026](https://science.alerce.online/)*
