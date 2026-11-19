# Proposal: Bearing fault diagnosis under realistic domain shift

**Track:** A (reproduce and extend a research paper)

**By:** Moosa Jilani, 581860 

**Instructor:** Dr. Saqib Nazir

## 1. Benchmark paper

J. Hendriks, P. Dumond, D. A. Knox, "Towards better benchmarking using the CWRU bearing fault dataset," *Mechanical Systems and Signal Processing*, vol. 169, p. 108732, 2022. DOI: [10.1016/j.ymssp.2021.108732](https://doi.org/10.1016/j.ymssp.2021.108732)

- **Venue:** Mechanical Systems and Signal Processing (Elsevier), a Q1 journal in Scimago. Citations: [?] (check Google Scholar).
- **Code:** no public code from the authors. I will write my own Python version.
- **Data:** the public Case Western Reserve University (CWRU) Bearing Data Center dataset, 12 kHz drive-end and fan-end recordings.

**Why this paper:** most CNN papers on CWRU report 95–100% accuracy. This paper shows that the usual train/test split lets the *same physical bearings* appear in both the training and the testing set. When the split uses separate bearings (split by fault size instead of motor load), accuracy drops sharply. That is a much more honest picture of how these models would do in a real factory.

## 2. Result to reproduce

The cross-domain testing accuracy of the **WDCNN** model (raw time-signal input) under:
- the **original framework** (datasets split by motor load: A→B, A→C, …), and
- the **proposed framework** (datasets split by fault size, so train and test bearings are different: D→E, D→F, …).

**Metric:** testing accuracy (%) on the 7-class problem, averaged over the 6 cross-domain cases.
**Paper's numbers (WDCNN, time input):** **90.6%** average in the original framework (Fig. 3) vs **36.2%** in the proposed framework (Fig. 4).

**Setup taken from the paper:** datasets as in Tables 1 and 2 (12 kHz data); 2048-point windows with 97% overlap (Table 3, Sec. 3.2); Adam optimizer, learning rate 0.0005, 4 epochs (Sec. 3.4).

## 3. Research question

> Under the paper's bearing-independent split, how much does WDCNN's accuracy drop further when Gaussian noise (signal-to-noise ratio from 10 dB down to 0 dB) is added to the test vibration signals, compared with the clean result?

**Why it matters:** real factory sensors pick up noise from nearby machines. WDCNN was published as a model with "good anti-noise ability" (Zhang et al., 2017), but that claim was made using the old way of splitting the data, where the same bearings appear in training and testing. The benchmark paper does not test noise at all, so it is open whether the claim still holds when the test bearings are new. If a model that is already weak on unseen bearings also breaks down under noise, it is not ready for real use.

## 4. Data and compute

- CWRU dataset (free download, a few hundred MB of `.mat` files).
- Python: NumPy, SciPy, PyTorch, scikit-learn, Matplotlib.
- WDCNN is a small 1D CNN, so training runs in minutes on a **Google Colab** GPU (or CPU).
- Every experiment is repeated with several random seeds, and I report mean ± standard deviation.

## 5. Plan

| Date | Goal |
|---|---|
| By Fri 27 Nov | Load CWRU data, build datasets A–F, implement WDCNN, first training runs |
| **By Fri 4 Dec (checkpoint)** | Paper's main result reproduced: WDCNN accuracy under both frameworks, compared with the paper's numbers |
| By Fri 11 Dec | Extension: noise experiments at several SNR levels, plots |
| **By Fri 18 Dec** | Final results, IEEE-format paper, clean code and README on GitHub |

**Fallback if reproduction fails:** if my numbers do not match the paper, I will still compare the *size of the drop* between the two frameworks. I will also explain the likely reasons for this drop. If WDCNN cannot be made to work, I will use a simpler 2-layer 1D CNN and run the same two-framework comparison.
