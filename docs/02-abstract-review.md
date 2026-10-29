# Abstract review and experimental design

> **Corrections (2026-10-27, after full-text reads; details in `03-thesis-deep-dive.md` section 1).** Roy et al. is not a cross-dataset study. Sarhan et al. (arXiv 2104.07183) has no cross-dataset experiment; the real NetFlow-v2 generalisability study is Layeghy and Portmann, arXiv 2205.04112. DI-NIDS is transductive and its journal venue is unconfirmed. The adversarial-encoder mechanism proposed below has contradictory evidence (Pawlicki et al., ARES 2025: five domain-adaptation methods fail on CIC-IDS2018 to UNSW-NB15), and SHAP on encoder latents is not analyst-readable. The recommended design is now Variant 1 in `03-thesis-deep-dive.md`: domain-classifier-guided feature pruning, multi-source leave-one-dataset-out training, GBDT on raw features, label-budget curves.

Reviewed 2026-10-27 against the literature in `01-research-landscape.md` plus targeted follow-up searches. Verdict first, then claim by claim, then the design that makes the abstract defensible.

---

## Verdict

The abstract picks the right problem and the right two weaknesses (generalisation, trust). Three things will decide whether an examiner accepts it:

1. **The cross-dataset claim is the whole thesis, and the closest prior work shows it is hard.** Kılıç and Şengül (IEEE Access vol. 14, 2026) trained Random Forest and XGBoost on CICIDS2017 and tested on CSE-CIC-IDS2018 using TreeSHAP-guided feature selection. Macro-F1 collapsed to 0.127 (RF) and 0.119 (XGBoost); SHAP-guided reduction lifted RF to 0.166 and dropping port features to 0.207. That is your exact model family, your exact explainer and your exact dataset pair. The abstract must state a *mechanism* for generalisation that goes beyond feature selection, and must promise to "quantify and reduce the gap", not "demonstrate generalisation".
2. **The DNN in the hybrid must earn its place.** On tabular flow features XGBoost alone is the strongest known baseline, in-dataset and cross-dataset (Atif and Ali 2026: XGBoost beats a RoBERTa-LoRA model by ~15 F1 cross-dataset; Zarkadis and Douligeris 2026: XGBoost beat DNN and stacking). The defensible role for the DNN is as a *domain-invariant encoder* (DI-NIDS, Layeghy et al. 2022/2023; Roy et al., ECML 2025) whose output feeds the GBDT. Plain probability stacking will not survive an ablation.
3. **SHAP output must be evaluated, not just displayed.** Kumar and Thing (2025) found explainers disagree on NIDS models; Kalakoti et al. (ICISSP 2025) found DeepLIFT beat SHAP on real SOC alerts; Arreche et al. (IEEE Access 2024) give the six metrics to use. Report fidelity, stability and per-alert latency of the explanations.

Everything else (cloud framing, mitigation module, latency and complexity metrics) is sound and mostly an engineering and measurement job.

---

## Claim-by-claim

| # | Abstract claim | Evidence status | What to change or add |
|---|---|---|---|
| 1 | Cloud environments face DDoS, brute force, botnets, infiltration | **Supported.** CSE-CIC-IDS2018 was collected on AWS (50 attacker machines; victim org with 420 machines and 30 servers) with exactly these scenarios plus Heartbleed and web attacks. CIDDS-001/002 were generated on OpenStack. ISOT-CID is from a real cloud. | Name the datasets in the abstract. "Cloud" is defensible with CSE-CIC-IDS2018 + CIDDS as the cloud pair. |
| 2 | Existing ML-IDS generalise poorly to unseen environments | **Strongly supported.** Cantone et al. 2024 (chance-level cross-dataset), Kılıç and Şengül 2026 (macro-F1 0.119), Atif and Ali 2026, Bilal et al. 2026 (30-point drop). | Keep. Cite these four. |
| 3 | Black-box nature prevents analyst trust | **Supported as motivation**, but SHAP itself is unstable on NIDS (Kumar and Thing 2025; Kalakoti 2025). | Add "explanations evaluated for fidelity and stability". |
| 4 | Hybrid ensemble: GBDT + DNN | **At risk.** Trees win on tabular flows; hybrids rarely beat XGBoost alone. | Make the DNN a domain-invariant encoder feeding the GBDT. Ablate GBDT-only, DNN-only, hybrid. |
| 5 | Trained on independently validated datasets, demonstrated with cross-dataset generalisation | **The hard claim.** Closest prior work got 0.12-0.21 macro-F1. CICFlowMeter datasets also have documented label and feature bugs. | Use corrected datasets (LYCOS / WTMC2021 / LycoS-Unicas) or the NF-v2 family. State the mechanism (domain-invariant encoder, multi-source training, environment-agnostic features, label-budget curves). Promise "quantify and reduce the gap". |
| 6 | Rule-driven mitigation: source blocking, alert escalation | **Reasonable, unevaluated in literature.** No rigorous academic study of auto-blocking cost was found; the base-rate fallacy (Axelsson 2000) says FPR dominates. | Evaluate false-block rate per day at realistic attack prevalence, time-to-mitigate, and escalation precision. Two-tier policy: auto-block only above a calibrated threshold with corroboration (repeat hits per source), else escalate. |
| 7 | Precision metrics, detection latency, computing complexity | **Good, incomplete.** | Add MCC, PR-AUC, FPR, false alarms/day, p50/p99 per-flow latency on CPU, explanation latency per alert, model size, training time. Never accuracy alone. |

Wording fixes: "a hybrid ensemble architectures" should be "a hybrid ensemble architecture"; "trained on the available datasets that are independently validated and demonstrated using cross-dataset generalization" is ambiguous (who validated what?).

---

## Closest prior work (position against these explicitly)

| Paper | What they did | What you add |
|---|---|---|
| Kılıç and Şengül, *SHAP-Guided Feature Selection for Cross-Dataset Generalization in NIDS*, IEEE Access 14, 2026. https://ada.atilim.edu.tr/items/d25563f0-322c-4384-b748-fe9d5695f1af (author order differs between IEEE and the repository; check IEEE before citing) | RF/XGBoost, CICIDS2017 to CSE-CIC-IDS2018, 80 aligned CICFlowMeter features, TreeSHAP feature subsets. Macro-F1 0.119 to 0.207. | A representation-level mechanism (domain-invariant encoder), corrected datasets, temporal splits, label-budget curves, explanation-quality metrics, mitigation loop. |
| Cantone, Marrocco, Bria, IEEE Access 2024. https://arxiv.org/abs/2402.10974 | Four classifiers, CIC-IDS2017/2018 + LycoS variants; cross-dataset at chance. | Same datasets, but a method designed to close the gap and an honest measurement of how much it closes. |
| Layeghy, Baktashmotlagh, Portmann, *DI-NIDS*, arXiv 2210.08252 (2022); Knowledge-Based Systems 2023, DOI 10.1016/j.knosys.2023.110626 | Domain-adversarial NN extracts invariant features; One-Class SVM on top; NFv2-CIC-2018 and NFv2-UNSW-NB15. | Supervised GBDT head instead of OSVM, SHAP on the head, three or more domains, mitigation. |
| Roy, Cody, Singhal, Choi, Jin, *Domain-Invariant Representation Learning in Latent Space*, arXiv 2312.17300 v5 (Oct 2025), listed as ECML 2025 | Multi-task latent space with classification + reconstruction + mutual-information bottleneck for domain generalisation. | A concrete recipe for the encoder loss if adversarial training proves unstable. |
| Ahmed et al., *Hybrid bagging and boosting with SHAP-based feature selection*, Scientific Reports Dec 2024. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11652632/ | Hybrid tree ensembles + SHAP feature selection on CIC-IDS2017 only; authors list dataset dependence and SHAP instability as limitations. | Cross-dataset and temporal evaluation they lack. |
| Zarkadis and Douligeris, arXiv 2605.13922 (2026). https://arxiv.org/abs/2605.13922 | Tree ensembles vs DNN vs stacking on UAVIDS-2025; XGBoost best; SHAP analysis; 10-fold CV only. | Shows why plain stacking is not a contribution. |
| Atif and Ali, arXiv 2609.13511 (2026) | XGBoost vs RoBERTa-LoRA under shift and evasion. | Your hybrid should be tested under the same three axes. |
| Sarhan, Layeghy, Portmann, *Evaluating Standard Feature Sets Towards Increased Generalisability and Explainability of ML-based NID*, arXiv 2104.07183 | Compares NetFlow vs CICFlowMeter feature sets for generalisation and SHAP explainability. | Justifies your feature-set choice. |
| Arreche et al., E-XAI, IEEE Access 2024 (DOI 10.1109/ACCESS.2024.3365140); Kumar and Thing, arXiv 2408.14040; Kalakoti et al., ICISSP 2025, arXiv 2506.07882 | How to score explanations (fidelity, stability, robustness, sparsity); evidence SHAP is not automatically trustworthy. | Use their metrics; report TreeSHAP vs DeepLIFT on your model. |

---

## Datasets for the cloud framing

| Dataset | Cloud? | Format | Size | Access | Role |
|---|---|---|---|---|---|
| CSE-CIC-IDS2018 | Yes, AWS | pcap + logs + CICFlowMeter-V3 CSV (80+ features) | ~16M flows (subsample to 2-4M) | `aws s3 sync --no-sign-request s3://cse-cic-ids2018/`; redistributable with citation. https://www.unb.ca/cic/datasets/ids-2018.html | Primary cloud dataset |
| CIC-IDS2017 (corrected: LYCOS-IDS2017 or WTMC2021) | No (campus testbed) | same CICFlowMeter family | ~2.8M flows | Rosay ICISSP 2022; Engelen WTMC 2021 | Source domain for the 2017 to 2018 transfer, directly comparable to Kılıç and Şengül and Cantone |
| NF-CSE-CIC-IDS2018-v2, NF-UNSW-NB15-v2, NF-ToN-IoT-v2 | 2018 one is AWS | 43 NetFlow features, common across all three | 18.9M / 2.4M / 16.9M | https://www.cyber.uq.edu.au/node/824 | Leave-one-dataset-out with 3 domains; same setting as DI-NIDS |
| CIDDS-001, CIDDS-002 | Yes, OpenStack | NetFlow (few fields) | ZIP 384 MB / 204 MB | https://www.hs-coburg.de/cidds ; cite Ring et al. 2017 | Narrow-feature out-of-domain cloud test (binary); only DoS, brute force, scans |
| ISOT-CID | Yes, real cloud | pcap, hypervisor + VM traffic, logs, syscalls | 8 TB | Supervisor-signed agreement + SFTP. https://onlineacademiccommunity.uvic.ca/isot/2022/11/25/cloud-security-datasets/ | Optional, only if a server is available |
| Kubernetes / microservice datasets (2025, preprints) | Yes, cloud-native | flows | e.g. ~3.18M flows, 456 threat-origin (Research Square rs-9883128, **unverified**); Ficco et al. microservice DDoS (**unverified**) | https://ieee-dataport.org/keywords/kubernetes-security | Optional modern cloud OOD test; check licences |

Do not use NSL-KDD. Use CIC-IDS2017 only in a corrected form; the original has >25% artefact flows (Engelen et al. 2021).

---

## Experimental design that makes the abstract defensible

### Tracks
- **Track 1 (main, comparable to prior work):** corrected CIC-IDS2017 to CSE-CIC-IDS2018 and the reverse, on the aligned CICFlowMeter feature space. This is the Kılıç and Şengül / Cantone setting; you must beat 0.207 macro-F1 or explain why not.
- **Track 2 (robustness):** NF-v2 trio, leave-one-dataset-out. Compare against DI-NIDS.
- **Track 3 (cloud OOD):** CIDDS-001 binary, using only the NetFlow fields common to NF-v1. Expect a drop; report it.

### Labels
Binary (benign vs attack) as the headline. Multi-class on the shared families: DoS/DDoS, brute force, botnet, infiltration, web, scan. Report macro-F1 and MCC; the 0.119 collapse in prior work is a majority-class bias, so per-class recall matters.

### Splits and protocol
- Within-dataset: **temporal** split by capture day (train earlier days, test later days). No random k-fold.
- Cross-dataset: train on A, test on B with zero B labels; then label-budget curves with 1%, 5%, 20% of B labels for calibration or fine-tuning, against a target-only baseline at the same budget.
- Controls: label-shuffle (must be at chance), XGBoost-only, MLP-only.
- Dataset audit before modelling (duplicates, label conflicts, leakage): IDS2Eval or manual checks.
- Feature hygiene: drop IPs, flow IDs, timestamps; bucketise or drop ports (Kılıç and Şengül got their biggest gain from dropping ports); drop CICFlowMeter features that encode absolute time (Active/Idle bug, fixed in the Engelen fork); robust per-dataset scaling.
- Record exporter and version.

### Models
- Baselines: XGBoost or LightGBM; MLP.
- H1: probability stacking GBDT + DNN (expected to fail; include as ablation).
- H2 (the proposal): DNN encoder trained with a domain-adversarial loss (DI-NIDS) or a mutual-information bottleneck (Roy et al.) on 2+ source domains; GBDT trained on [raw hygienic features + encoder embedding]; SHAP on the GBDT head via TreeSHAP (exact, fast).
- Report under three axes: in-dataset, cross-dataset, and problem-space-constrained evasion (Perturb-ability Score masking).

### Explainability evaluation
- TreeSHAP per alert on the GBDT head; DeepLIFT or Integrated Gradients on the encoder for comparison.
- Metrics (Arreche et al.): fidelity via deletion/insertion curves, stability via top-k Jaccard across seeds and small perturbations, sparsity, and explanation latency per alert.
- Qualitative: 10-20 alerts per attack family with the top-5 features, checked against what the attack actually does.

### Mitigation evaluation
- Replay test-set flows in time order through detector, calibrated threshold (report ECE or reliability diagram), and policy: auto-block if score above t_high and N hits from the same source in W seconds; escalate if above t_low; else ignore.
- Metrics: false-block rate per day at assumed attack prevalence of 0.1% and 1%; time-to-mitigate from first malicious flow; escalation precision; number of actions per day. Simulate the enforcement API (nftables, AWS security group, or Kubernetes NetworkPolicy) with a mock; no cloud account needed.

### Compute
All CPU on the M2 Pro. Subsample CSE-CIC-IDS2018 by day to 2-4M flows; store Parquet; XGBoost trains in minutes. The DNN encoder trains on MPS or CPU in under an hour per run at this size. No GPU rental needed.

---

## Suggested revision of the abstract (optional)

Cloud platforms concentrate shared, multi-tenant infrastructure and are exposed to DDoS, brute-force, botnet and infiltration attacks. Machine-learning intrusion detection systems report near-perfect accuracy on single benchmark datasets, yet recent studies show their performance falls to near chance when applied to a different network, and their opaque decisions give analysts little basis for action. This thesis presents an explainable detection and mitigation framework for cloud network traffic that targets both weaknesses. Detection uses a hybrid model in which a neural encoder, trained across multiple source networks for domain-invariant representations, feeds a gradient-boosted decision-tree classifier. The framework is evaluated on corrected cloud-collected datasets (CSE-CIC-IDS2018 on AWS, CIC-IDS2017, CIDDS on OpenStack, and the NetFlow-v2 family) under time-ordered and leave-one-dataset-out protocols, with label-budget curves that quantify how much the cross-network gap is reduced relative to tree-only and feature-selection baselines. Per-alert SHAP explanations are generated with TreeSHAP and assessed for fidelity, stability and latency rather than assumed correct. A calibrated, rule-driven mitigation module converts high-confidence, corroborated detections into source blocking or analyst escalation, and is evaluated by false-block rate at realistic attack prevalence and time-to-mitigate. Results are reported as precision-recall, MCC, false alarms per day, inference and explanation latency, and model complexity on CPU-only hardware. The outcome is a reproducible, interpretable detection pipeline whose generalisation and operational cost are measured rather than claimed.

---

## Immediate next steps

1. Decide Track 1 dataset form: LYCOS/WTMC2021 corrected CSVs vs NF-v2. Default: both, since Track 1 and Track 2 use different ones.
2. Set up the Python environment and download CSE-CIC-IDS2018 CSVs plus NF-UNSW-NB15-v2 (laptop-sized).
3. Reproduce the Kılıç and Şengül baseline (XGBoost, 2017 to 2018, ports dropped) to confirm the ~0.2 macro-F1 starting point. That number is your chapter-1 motivation and your chapter-5 yardstick.
4. Only then build the encoder.
