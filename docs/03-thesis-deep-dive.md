# Deep dive: is the proposed thesis feasible, and in what form?

Written 2026-10-27 after full-text reads of the closest prior work, a novelty check, a dataset and code verification, and a sweep of the mitigation and explanation-evaluation literature. Tags: **[FT]** full text read, **[A]** abstract only, **[S]** search snippet only (verify before citing).

---

## 0. Bottom line

The thesis is feasible on your laptop and the problem is real, but the abstract's method needs one change. The adversarial "domain-invariant encoder" has contradictory evidence: it helped in one direction in DI-NIDS (which also used unlabelled target data), and five domain-adaptation methods failed outright on CIC-IDS2018 to UNSW-NB15 in a 2025 ARES paper. Worse, a tree head on encoder latents gives SHAP values over latent dimensions, which analysts cannot read. The recommended form keeps the GBDT on raw flow features, so explanations stay in flow-feature space, and gets its generalisation mechanism from **shift-aware feature engineering driven by a domain classifier**, **multi-source leave-one-dataset-out training**, and **label-budget curves with a target-only control**. The mitigation module and explanation evaluation stay as planned and are where the operational contribution lives. Section 5 gives three variants; Variant 1 is recommended.

---

## 1. Corrections to `02-abstract-review.md`

Full-text reads changed four things stated earlier.

- **Roy et al. (arXiv 2312.17300) is not cross-dataset.** Its "domains" are attack-class groups and capture days inside one dataset, and it tests novel attacks. Title changed in v4/v5 to "Improving Novel Anomaly Detection with Domain-Invariant Latent Representations". Keep it only as a recipe for a mutual-information bottleneck loss. Code: a single notebook at https://github.com/padmaksha18/MTRAE.
- **Sarhan et al. (arXiv 2104.07183) has no cross-dataset experiment.** It compares NetFlow (43 features) with CICFlowMeter within each dataset. NetFlow wins in-dataset (RF F1 0.98 vs 0.93 on CSE-CIC-IDS2018). The real cross-dataset NetFlow study is Layeghy and Portmann, "On Generalisability of ML-based NIDS", arXiv 2205.04112 [FT], journal version in Computers and Electrical Engineering 2023 (DOI 10.1016/j.compeleceng.2023.108692, journal name inferred from DOI prefix).
- **DI-NIDS is transductive.** The domain-adversarial network is trained with unlabelled target flows. Its journal venue is not confirmed on the arXiv page.
- **Kılıç and Şengül** are Can Kılıç (first author) and Gökhan Şengül, IEEE Access 14, pp. 90926-90936, 2026, DOI 10.1109/ACCESS.2026.3703481, CC BY. Only the abstract was obtainable; per-class numbers and code status are unverified.

---

## 2. The numbers you must beat

| Setting | Model | Result | Source |
|---|---|---|---|
| CIC-IDS2017 to CSE-CIC-IDS2018, binary | XGBoost | MCC 35.68, F1 26.43 | Cantone et al. 2024 [FT], Table 3 |
| CSE-CIC-IDS2018 to CIC-IDS2017, binary | XGBoost | MCC 41.75, F1 44.58 | same |
| LycoS-IDS2017 to LycoS-Unicas-IDS2018 | XGBoost | MCC 10.17, F1 14.42 | same; corrected labels made transfer *worse* in this direction |
| LycoS-Unicas-IDS2018 to LycoS-IDS2017 | XGBoost | MCC 40.29, F1 35.54 | same |
| CIC-IDS2017 to CSE-CIC-IDS2018, multi-class | XGBoost / RF | macro-F1 0.119 / 0.127; RF rises to 0.166 with TreeSHAP subset, 0.207 after dropping ports; no gain for XGBoost | Kılıç and Şengül 2026 [A] |
| NFv2 datasets, 12 directed pairs, supervised | ExtraTree / RF / FFNN / LSTM | average F1 decay 56.28%; UNSW-NB15 as target fails from every source; ExtraTree UNSW to BoT-IoT 94.83% but BoT-IoT to UNSW 4.90% | Layeghy and Portmann 2022 [FT] |
| NFv2-CIC-2018 to NFv2-UNSW-NB15 and reverse | RF | F1 0.84 / 7.70 | DI-NIDS [FT], Tables 5-6 |
| same | OSVM alone / DANN alone / DI-NIDS | 86.15 / 17.31 / 85.79 and 15.74 / 61.94 / 93.29 | same; the encoder helps in one direction only |
| CIC-IDS2018 to UNSW-NB15 | CORAL, deepCORAL, DANN, ADDA, CDAN | attack recall at most 0.35, often 0 | Pawlicki, Szelest, Kozik, Choraś, ARES 2025 [FT], https://zenodo.org/records/16919506 |
| Within-dataset, any of the above | any tree model | MCC 96-99.8 | Cantone; Layeghy |

Two facts to carry into the thesis: a single mRMR-selected feature gives within-dataset MCC 69.7 on these datasets (Cantone), which is shortcut learning in plain sight; and the NetFlow-v3 label-budget preprint (preprints.org 202610.1041 [S]) reports source pretraining helps in 8% and hurts in 38% of 540 comparisons, with target-only training at 5% labels matching fine-tuning.

---

## 3. What actually moves cross-dataset performance

| Mechanism | Best evidence | Verdict |
|---|---|---|
| Drop identifiers, drop or bucketise ports, drop absolute-time features | macro-F1 0.127 to 0.207 (Kılıç); Cantone's identifier-drop still leaves MCC ~30 | Small, consistent, cheap. Necessary, not sufficient. |
| Standard NetFlow feature set | Better in-dataset (Sarhan); RF still 0.84 F1 cross-dataset (DI-NIDS) | Use NF-v2 for comparability, not as a mechanism. |
| Domain-classifier-guided pruning (train source-vs-target classifier, remove its top SHAP features) | Not found in NIDS literature (two targeted searches). The NF-v3 preprint notes a domain classifier reaches AUC 0.996 on these pairs [S]. | **Open and cheap.** Interpretable, keeps SHAP on raw features. Core of Variant 1. |
| Multi-source training, leave-one-dataset-out | No NIDS evidence found | **Open.** Natural with the NF-v2 trio. |
| Domain-adversarial encoder | DI-NIDS one direction; Pawlicki fails | Contradictory. Variant 2 only, with ablation. |
| MI-bottleneck encoder | Roy, within-dataset only | Weak. |
| Few-shot target labels | +1 to 2.5 AUC at 0.1% labels (Koukoulis et al., arXiv 2505.08816 [FT], code https://github.com/koukipp/contrastive_transformers_ids); target-only matches at 5% [S] | Modest but practical. Variant 3 and a chapter in Variant 1. |
| Test-time adaptation | NetSight pre-adaptation F1 0.42, +11.7 points over OWAD; retraining from scratch still wins [A] | Thin. |
| Foundation-model pretraining | pair-dependent AUC 63-94 (Koukoulis) | Mixed. Out of scope. |

Shortcut features named in full-text reads: TTL fields (MAX_TTL, MIN_TTL, DNS_TTL_ANSWER) and PROTOCOL/L7_PROTO in NetFlow (Layeghy); Idle Mean/Min/Max carrying over 50% of RF importance in CICFlowMeter (Sarhan); ports (Kılıç, Pawlicki, arXiv 2607.00553 [S]). Your pruning step should rediscover these automatically; that is a good sanity check.

---

## 4. Novelty

No paper was found that publishes "invariant encoder, GBDT head, TreeSHAP, cross-dataset with label-budget curves". The pieces exist separately: DANN then OSVM (DI-NIDS), DA plus SHAP on a neural head with negative results (Pawlicki), TreeSHAP feature selection on trees cross-dataset (Kılıç), few-shot transfer (Koukoulis). Probably new: leave-one-dataset-out multi-source training on NF-v2, domain-classifier-guided feature pruning for NIDS, label-budget curves with a target-only control, and a mitigation module evaluated for collateral cost. Two further papers to position against: Onuorah, Sun, Mashao, IEEE Access 14, 2026, DOI 10.1109/ACCESS.2026.3675887 (CNN + SMOTE + SHAP feature selection on CSE-CIC-IDS2018, CICDDoS2019, TII-SSRC-23; cross-dataset numbers not retrieved), and Guerra-Manzanares and Huang, ICISSP 2026, arXiv 2602.23874 (feature-set transferability across four IoT datasets with Argus, Zeek and CICFlowMeter). The search used web tools, not Scopus; treat this as absence of evidence.

---

## 5. Three possible theses

### Variant 1 (recommended): shift-aware explainable GBDT with multi-source training and a costed mitigation loop
- **Question.** How much of the cross-network gap in cloud intrusion detection is caused by environment-specific features, and how far can shift-aware feature engineering plus multi-source training close it without touching the model class analysts can explain?
- **Method.** (1) Hygiene: drop identifiers, timestamps, absolute-time features; bucketise ports. (2) Domain-classifier pruning: train XGBoost to distinguish source from target flows (labels unused), rank features by TreeSHAP on that classifier, remove or transform the top-k until domain AUC falls below a threshold; report what was removed. (3) Per-environment robust scaling. (4) Multi-source training on two NF-v2 datasets, test on the held-out third (leave-one-dataset-out), plus the CIC-IDS2017 to CSE-CIC-IDS2018 pair for comparability. (5) Label-budget curves at 0, 1, 5, 20% target labels against a target-only control. (6) TreeSHAP per alert on raw features, evaluated with the protocol in section 6. (7) Calibrated, two-tier mitigation evaluated by replay.
- **Why this one.** Every component has evidence or is a clean open gap; explanations stay in flow-feature space; the baseline comparisons are exact (Cantone's table, Kılıç's 0.207); all CPU on the laptop. Even if the gain is modest, the measurement is the contribution, which is how the strongest 2024-2026 papers in this area are written.
- **Risk.** Gains may be modest; transductive use of unlabelled target flows in step 2 must be stated explicitly and ablated against an inductive version (prune using a third dataset as proxy target).

### Variant 2: the abstract as written, with an inductive invariant encoder feeding GBDT
- Same datasets and protocol as Variant 1, but the generalisation mechanism is a DANN or MI-bottleneck encoder trained on multiple *source* datasets only (inductive), with the GBDT on [hygienic raw features + encoder latents]. Explanations: TreeSHAP on raw features plus DeepSHAP composed through the encoder for the latent part.
- **Risk.** Evidence says the encoder may add nothing (DANN alone 17.31 F1 in DI-NIDS; Pawlicki). A negative result is publishable only with a very rigorous protocol, which is harder to defend at MTech level. Choose this only if your supervisor wants a deep-learning component.

### Variant 3: "how many target labels does a new cloud environment need?"
- Reframe around the label-budget question, which cloud providers face when onboarding a new tenant or region. Compare zero-shot transfer, few-shot fine-tuning of GBDT, Koukoulis-style contrastive pretraining with few-shot heads, and target-only training at matched budgets, across the NF-v2 trio and the CIC pair; add calibration under shift (deferral rates, see section 6) and the mitigation loop.
- **Risk.** Overlaps with the unreviewed NF-v3 preprint [S]; needs careful positioning. Lower novelty on the detection side, strong on the operational side.

Recommendation: Variant 1 as the thesis, with Variant 3's label-budget curves as one chapter.

---

## 6. Evaluation protocols (citable)

### 6.1 Mitigation
1. Replay time-ordered held-out flows through the calibrated detector; sweep attack prevalence p in {0.1%, 1%, 10%}; report PPV and false alerts per day (Axelsson 2000; Erbacher, arXiv 2203.08801 [FT]: ML false-alarm rates ~1e-2 yield hundreds of thousands of false positives per hour at enterprise volumes). Worked arithmetic: TPR 0.99, p = 0.001, FPR 0.01 gives PPV ~9%; FPR 0.001 gives ~50%.
2. Policy tiers: log / escalate / temporary block with TTL. Auto-block requires calibrated p-hat at or above tau_b and n alerts from the source within window W; escalate between tau_e and tau_b. Threshold policies are the optimal structure for this decision (Hammar and Stadler, IEEE TNSM 2024, arXiv 2301.06085).
3. Cost model after Lee et al., J. Computer Security 2002: total = operational + FN x damage + TP x response + FP_blocked x collateral + FP_escalated x analyst time. Break-even tau_b = C_fb / (C_fb + D) (derived, not from the paper); sweep D:C_fb over 10:1, 100:1, 1000:1.
4. Calibration: isotonic or Platt on validation; report Brier and ECE with reliability diagrams; re-test on the target dataset, since shift destroys calibration (dual-threshold conformal deferral preprint, preprints.org 202607.1357 [S]: deferral rises to 79-99% under CIC2017 to 2018 shift). Learning-to-defer baseline: Jalalvand et al., arXiv 2506.18462.
5. Metrics: containment (share of attack flows after block takes effect), time-to-respond p50/p95, collateral (benign flows, bytes, unique sources blocked, NAT-shared sources), false blocks per day, availability impact, escalations per day against 12 alerts per analyst-hour (Erbacher).
6. Actuator: measure or inject enactment latency. No peer-reviewed measurement of AWS NACL, WAF IP sets, Kubernetes NetworkPolicy or nftables as an ML-driven actuator was found; state that gap. Note security groups are allow-only and vanilla NetworkPolicy has no deny action, so NACL, WAF IP sets or Cilium/Calico deny are the realistic actuators.
7. Baselines: no response, block-every-alert, fixed 0.5, cost-optimal, all-escalate. Safety: allow-list, TTL, rule quota, rollback. Five time-block splits with bootstrap CIs. Frame against the IRS mapping study (Rezapour et al., IEEE Access 2024) which finds no reliable fully automated IRS exists.

### 6.2 Explanations
1. Explainers: TreeSHAP primary; KernelSHAP and LIME as references; random and global-importance baselines; fixed benign k-means background.
2. Faithfulness: descriptive accuracy / deletion-insertion AUC with benign-median replacement (Warnecke et al., IEEE EuroS&P 2020, arXiv 1906.02108 [FT]); Faithfulness Correlation and Monotonicity (Kalakoti et al., ICISSP 2025 [FT]); ROAR retraining at k = 1, 3, 5, 10 against random removal (Hooker et al., NeurIPS 2019).
3. Stability: top-k intersection over runs (Warnecke); Max-Sensitivity at radius 0.1 (Kalakoti); local Lipschitz (Alvarez-Melis and Jaakkola 2018); global-vs-local agreement and cross-dataset rank correlation (Kumar and Thing).
4. Sparsity: mass-around-zero curve, attribution entropy, features needed for 80% of absolute SHAP mass.
5. Semantic ground truth: freeze attack-to-feature sets from ATT&CK/D3FEND before running; report FAP, FAR, FAF1 (Alquliti, Karafili, Kang, arXiv 2505.08006 [FT]; published FAF1 is only 0.06-0.32, so report against chance) and RRA/RMA (Kalakoti).
6. Efficiency: per-alert latency p50/p95/p99 at batch size 1 and the explanation-to-prediction time ratio (Tolay, arXiv 2608.10349 [A]: TreeSHAP ~0.29 ms per sample on XGBoost vs ~140 ms on RF). Benchmark TreeSHAP vs KernelSHAP vs DeepSHAP yourself; no NIDS head-to-head exists.
7. Organise by Nauta et al. Co-12 properties (ACM CSUR 2023); paired Wilcoxon with bootstrap CIs. Libraries: OpenXAI (tabular-native), Quantus (image-first, check tabular), E-XAI code (Arreche et al.).

---

## 7. Data and code availability (verified 2026-10-27 by HEAD/range requests and API calls; nothing downloaded)

### 7.1 Datasets

| Dataset | Where | Size | Terms | Notes |
|---|---|---|---|---|
| CSE-CIC-IDS2018 processed CSVs | `s3://cse-cic-ids2018/Processed Traffic Data for ML Algorithms/` via `aws s3 sync --no-sign-request` | 10 files, 6.4 GiB; the 20-02 file alone is 3.8 GiB (its name is misspelled "Thuesday") | cite UNB | live |
| NF-UNSW-NB15-v2 | UQ RDM uuid `8c6e2a00-ef9c-11ed-827d-e762de186848` | 34 MB zip, 421 MiB CSV, 2.39M rows, 4% attack | academic use after citing; commercial needs agreement | live |
| NF-CSE-CIC-IDS2018-v2 | uuid `ce5161d0-ef9c-11ed-827d-e762de186848` | 612 MB zip, ~3 GiB CSV, 18.9M rows, 12% attack | same | live |
| NF-ToN-IoT-v2 | uuid `a4ad7080-ef9c-11ed-a964-b70596e96ad5` | 168 MB zip, 2.5 GiB CSV, 16.9M rows | same | live; cap at ~1M rows |
| NF-BoT-IoT-v2 | uuid `b97fa3c0-ef9c-11ed-8007-832d3bc76ba8` | 459 MB zip, 5.6 GiB CSV, 37.8M rows, 99.6% attack | same | live; too skewed, cap at ~1M |
| NF-v3 (temporal features, 53 cols) | UNSW `abd2f5d8-e268-4ff0-84fb-f2f7b3ca3e8f` 111 MB; CSE18 `4ac221b1-6bd6-42b1-bdf7-03f4fc7efb22` 780 MB | | same | live |
| WTMC2021 corrected CIC-IDS2017 (Engelen) | https://intrusion-detection.distrinet-research.be/WTMC2021/Dataset/dataset.zip | 318 MiB | cite | live; labelling code https://github.com/GintsEngelen/WTMC2021-Code; fixed CICFlowMeter https://github.com/GintsEngelen/CICFlowMeter |
| CNS2022 improved CIC-IDS2017 / CSE-CIC-IDS2018 (Liu, Engelen et al.) | https://intrusion-detection.distrinet-research.be/CNS2022/Datasets/ `CICIDS2017_improved.zip`, `CSECICIDS2018_improved.zip` | 328 MiB / 9.7 GiB | cite | live; relabel "Attempted" flows as Benign per the authors |
| LYCOS-IDS2017 (Rosay) | https://lycos-ids.univ-lemans.fr/download-lycos-ids2017.html | 508 MiB | researchers, cite | live; LycoSTand extractor also live |
| LycoS-Unicas-IDS2018 (Cantone) | Google Drive via https://github.com/MarcoCantone/LycoS-Unicas-IDS2018 | ~5.1 GB, 13.69M rows, 77 features, 14 classes | no licence file | live |
| NFS-2023-nTE / TE | https://github.com/FlowFrontiers/CyberML-DataQuality/tree/main/4-NFS-2023 | 161 MB (five day ZIPs) | none stated | live; authorship as Pekar and Jozsa unconfirmed |
| CIDDS-001 / CIDDS-002 | https://www.hs-coburg.de/forschen/cidds-coburg-intrusion-detection-data-sets/ | 384 MiB / 204 MiB zips; 4.05 / 2.0 GiB CSV | cite Ring et al. ECCWS 2017 | live; unidirectional NetFlow, 16 columns, `Bytes` as strings like "2.1 M", only ~6 features overlap NF-v2. Feature-poor extra domain only |
| CIC-IDS2017 original CSV direct link | cicresearch.ca `MachineLearningCSV.zip` | | | dead (redirects); use the UNB page link |

The NF-v2/v3 download links on https://staff.itee.uq.edu.au/marius/NIDS_datasets/ are a JavaScript app. Scriptable route: `GET https://api.rdm.uq.edu.au/datasets/<uuid>/download` returns JSON whose `url` is a short-lived presigned S3 link; each ZIP is a BagIt bundle with `data/<name>.csv` and `data/NetFlow_v2_Features.csv`. The 43 v2 columns were confirmed from the shipped feature file.

### 7.2 Code

| Need | Choice | Status |
|---|---|---|
| Trees, SHAP, torch | xgboost 3.4.1, lightgbm 4.7.0, shap 0.54.1, torch 2.14.1; all have macOS arm64 wheels for Python 3.12 and 3.14; torch needs macOS 14+ | verified; `brew install libomp` if xgboost import fails |
| Python version | 3.12 via `uv venv --python 3.12` | ids2eval excludes 3.14; older DA libraries will not work on 3.14 |
| Domain-adversarial encoder (Variant 2 only) | ~30 lines of gradient-reversal in plain PyTorch, or https://github.com/fungtion/DANN | tllib and pytorch-adapt have stale pins; avoid |
| DI-NIDS reference | https://github.com/layeghy/DI-NIDS (BSD-3, created 2026-08-28, needs pandas<3) | live; official status unverified, paper does not link it |
| Dataset audit | IDS2Eval 0.2.3 (AGPL, Python <3.14, low maturity) or your own duplicate/label-conflict checks | live |
| Calibration | netcal 1.4.0 or sklearn CalibratedClassifierCV plus hand-written ECE | live |
| Explanation metrics | OpenXAI from source (tabular-native); Quantus 0.6.0 (image-first); shap.benchmark | live |
| Prior-work code | Kılıç and Şengül: none found. Cantone: data only, no training code. Sarhan preprocessing: nProbe v9 command documented in arXiv 2011.09144 | you will re-implement baselines |

### 7.3 Laptop feasibility

- **Disk.** Core tier (NF-v2 four datasets plus CIDDS) is ~20 GiB during extraction and 3-5 GiB once converted to zstd Parquet with ZIPs deleted. Corrected-label tier adds ~1.5 GiB of ZIPs. Skip NF-UQ-NIDS-v2 (a union, 12.8 GiB). Everything extracted at once would be 70-90 GiB; do not do that.
- **RAM.** 4M rows x 80 features as float32 is 1.3 GB; XGBoost `tree_method=hist` needs 2-4 GB and trains in minutes on CPU (no Metal backend). Run TreeSHAP on a 50k-200k sample. A DNN encoder on the same data runs in tens of seconds per epoch on MPS or CPU.
- **Subsampling plan.** Convert each CSV to Parquet once with polars streaming (float32, drop IPs and ports for modelling, keep them in a side table for the mitigation replay). Dedupe and resolve label conflicts first. Split by time or capture block, never random rows. Per-dataset training pool 1-1.5M rows with per-class caps of 100-200k and benign filled to 3:1. Cross-dataset test: 1M stratified rows at the natural prior; full sets only for final numbers. Report both sampled-prior and natural-prior results. CIDDS stays a separate low-feature experiment.

---

## 8. Risk register

| Risk | Likelihood | Mitigation |
|---|---|---|
| Gains over port-dropped XGBoost are small | High | The contribution is the measurement and the decomposition of the gap; say so in chapter 1 |
| Domain-classifier pruning removes attack-relevant features | Medium | Report per-class recall after each removal; cap removals; compare to task-SHAP selection (Kılıç) |
| Transductive leakage in pruning | Medium | Ablate with a proxy third dataset; state it |
| Label-budget curves show target-only wins | Medium | That is a finding; it motivates the mitigation and calibration chapters |
| SHAP explains shortcuts faithfully | High | That is the point of the semantic-alignment metrics; show it before and after pruning |
| Dataset download or licence problem | Low to medium | See section 7 |
| Time | High (full-time job) | Every experiment is a script with a seed and a results CSV; baseline first, encoder never |

---

## 9. Add to the reading list

- Layeghy and Portmann, On Generalisability of ML-based NIDS, arXiv 2205.04112 [FT]
- Pawlicki et al., SHAP Insights Into Domain Adaptation in NetFlow NIDS, ARES 2025, https://zenodo.org/records/16919506 [FT]
- Koukoulis, Syrigos, Korakis, contrastive transformers for IDS, arXiv 2505.08816 [FT]
- Erbacher, Base-Rate Fallacy Redux, arXiv 2203.08801 [FT]
- Warnecke et al., Evaluating Explanation Methods for DL in Security, EuroS&P 2020 [FT]
- Alquliti, Karafili, Kang, Feature Alignment Metrics for X-IDS, arXiv 2505.08006 [FT]
- Lee et al., cost-sensitive modeling for ID and response, J. Computer Security 2002
- Rezapour, GhasemiGol, Takabi, Systematic Mapping Study on IRS, IEEE Access 2024
- Hammar and Stadler, Near-Optimal Intrusion Responses, IEEE TNSM 2024

---

## 10. Next two weeks

1. Supervisor meeting: present Variant 1 vs 2 vs 3 with the table in section 2. Get a decision on whether a deep-learning component is required.
2. Environment: `uv` project on Python 3.12 with pandas, pyarrow, scikit-learn, xgboost, lightgbm, shap, netcal, openxai.
3. Download NF-UNSW-NB15-v2 and the CSE-CIC-IDS2018 CSVs; convert to Parquet; run the dataset audit.
4. Reproduce two numbers: Cantone XGB CIC17 to CIC18 (MCC ~36) and Kılıç port-dropped (macro-F1 ~0.2). Commit the scripts with seeds.
5. Train the domain classifier and look at its top SHAP features. If TTL, ports and idle-time features come out on top, the thesis story is confirmed before any method work.
