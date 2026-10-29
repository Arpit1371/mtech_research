# Paper index

One line per paper. Keep `key` stable so notes and the thesis bibliography can reference it. `status`: todo / skimmed / read. Items marked (unverified) were seen only in a search snippet; confirm before citing.

| key | title | venue/year | URL | why it matters | status |
|---|---|---|---|---|---|
| arp2022 | Dos and Don'ts of Machine Learning in Computer Security | USENIX Security 2022 | https://arxiv.org/abs/2010.09470 | The P1-P10 pitfalls checklist every experiment must pass | todo |
| sommer2010 | Outside the Closed World: On Using ML for NID | IEEE S&P 2010 | https://www1.icsi.berkeley.edu/pubs/networking/outsidethe10.pdf | Why ML-NIDS fails in deployment; base rates, semantic gap | todo |
| axelsson2000 | The Base-Rate Fallacy and the Difficulty of Intrusion Detection | ACM TISSEC 2000 | https://www.profsandhu.com/cs6393_s20/p186-axelsson.pdf | False-alarm rate limits usefulness | todo |
| pendlebury2019 | TESSERACT: Eliminating Experimental Bias in Malware Classification | USENIX Security 2019 | https://www.usenix.org/conference/usenixsecurity19/presentation/pendlebury | Temporal/spatial bias; time-ordered evaluation | todo |
| andresini2021 | INSOMNIA: Towards Concept-Drift Robustness in NID | AISec 2021 | DOI 10.1145/3474369.3486864 | TESSERACT adapted to NIDS | todo |
| cantone2024 | On the Cross-Dataset Generalization of ML for NID | IEEE Access 2024 | https://arxiv.org/abs/2402.10974 | Cross-dataset collapse to chance; LycoS-Unicas-IDS2018 | todo |
| atif2026 | Three-Axis Stress Test of LLM vs Classical ML for NID | arXiv 2609.13511, 2026 | https://arxiv.org/abs/2609.13511 | XGBoost vs RoBERTa-LoRA under shift and evasion | todo |
| zhao2025sugar | The Sweet Danger of Sugar: Debunking Representation Learning for Encrypted Traffic Classification | ACM SIGCOMM 2025 | https://arxiv.org/abs/2507.16438 | Pretrained traffic models exploit shortcuts | todo |
| wang2026bias | Bias in the Shadows: Shortcuts in Encrypted Traffic Classification (BiasSeeker) | arXiv 2601.10180, 2026 | https://arxiv.org/abs/2601.10180 | Shortcut-feature detection method | todo |
| beltiukov2026netfound | netFound: Principled Design for Network Foundation Models | arXiv 2310.17025 v5, 2026 | https://arxiv.org/abs/2310.17025 | Open weights + corpus; candidate model for direction A | todo |
| wang2024netmamba | NetMamba: Efficient Traffic Classification via Pre-training Unidirectional Mamba | arXiv 2405.11449, 2024 | https://arxiv.org/abs/2405.11449 | Lightweight pretrained alternative | todo |
| cui2025trafficllm | TrafficLLM | arXiv 2504.04222, 2025 | https://arxiv.org/abs/2504.04222 | LLM-tuned traffic model, self-reported results | todo |
| lin2022etbert | ET-BERT | WWW 2022 | https://arxiv.org/abs/2202.06335 | Landmark pretrained traffic model | todo |
| houssel2024 | Towards Explainable NID using LLMs | arXiv 2408.04342, 2024 | https://arxiv.org/abs/2408.04342 | LLMs unfit as NetFlow detectors; explainer role | todo |
| rehman2025 | From Flows to Words: Can Zero-/Few-Shot LLMs Detect Network Intrusions? | arXiv 2510.17883, 2025 | https://arxiv.org/abs/2510.17883 | Prompt-only LLM detection degrades with scale | todo |
| tsegai2026alertbench | Alert-Bench: LLM Agents for SOC Alert Triage | NeurIPS 2026 workshop | https://neurips.cc/virtual/2026/168137 | LLM agents miss >=40% of attack alerts | todo |
| engelen2021 | Troubleshooting an Intrusion Detection Dataset: the CICIDS2017 Case Study | WTMC 2021 | fixed CICFlowMeter: https://hub.docker.com/r/thelurps/gintsengelen-cicflowmeter | CIC-IDS2017 flaws + corrected flows | todo |
| lanvin2022 | Errors in the CICIDS2017 Dataset and the Significant Differences in Detection Performances It Makes | CRiSIS 2022 | https://hal.archives-ouvertes.fr/hal-03775466 | Packet misordering, duplicates, mislabels | todo |
| liu2022 | Error Prevalence in NIDS Datasets: CIC-IDS2017 and CSE-CIC-IDS2018 | IEEE CNS 2022 | https://archive.cps-vo.org/node/97449 | Recreated datasets; release labelling logic | todo |
| pekar2024 | NFS-2023: CIC-IDS2017 re-flowed with NFStream | arXiv 2401.16843, 2024 | https://arxiv.org/abs/2401.16843 | RF near-perfect on corrected data too | todo |
| sarhan2021 | NetFlow Datasets for ML-based NIDS (NF-v1/v2) | arXiv 2101.11315, 2021 | https://arxiv.org/abs/2101.11315 | Shared feature set enabling cross-dataset tests | todo |
| luay2026 | Temporal Analysis of NetFlow Datasets for NIDS (NF-v3) | arXiv 2503.04404 v3, 2026 | https://arxiv.org/abs/2503.04404 | Temporal features for drift work | todo |
| goldschmidt2025 | Network Intrusion Datasets: A Survey, Limitations, and Recommendations | Computers & Security 2025 | https://arxiv.org/abs/2502.06688 | 89-dataset survey, selection guidance | todo |
| elmahdaouy2026 | DL for Contextualized NetFlow-Based NID: Methods, Data, Evaluation, Deployment | arXiv 2602.05594, 2026 | https://arxiv.org/abs/2602.05594 | Survey; inflation sources | todo |
| elshehaby2025ps | A Novel Perturb-ability Score to Mitigate Evasion Attacks on Flow-Based ML-NIDS | arXiv 2409.07448 v4, 2025 | https://arxiv.org/abs/2409.07448 | Problem-space feature manipulability | todo |
| grini2025 | Constrained Network Adversarial Attacks: Validity, Robustness, Transferability | arXiv 2505.01328, 2025 | https://arxiv.org/abs/2505.01328 | Most unconstrained AEs are invalid traffic | todo |
| wang2025gnn | Are We There Yet? Unraveling the SOTA Graph NIDS | arXiv 2503.20281, 2025 | https://arxiv.org/abs/2503.20281 | GNN-NIDS reproducibility gaps | todo |
| guerra2025graphids | GraphIDS: Self-Supervised Graph Representations for NID | NeurIPS 2025 | https://arxiv.org/abs/2509.16625 | Current GNN SOTA | todo |
| zhang2025ssf | Continual Learning with Strategic Selection and Forgetting for NID | IEEE INFOCOM 2025 | https://arxiv.org/abs/2412.16264 | Continual learning baseline | todo |
| mousavipour2026netsight | NetSight: Shift Detection and Adaptation for NID | arXiv 2508.15100 v2, 2026 | https://arxiv.org/abs/2508.15100 | Online drift adaptation | todo |
| bilal2026fl | Dataset-centric evaluation of federated IDS in IoT networks | Scientific Reports 2026 | https://pmc.ncbi.nlm.nih.gov/articles/PMC12824137/ | 30-pt cross-dataset drop under FL | todo |
| manca2026sage5gc | SAGE-5GC | arXiv 2602.03596, 2026 | https://arxiv.org/abs/2602.03596 | Adaptive attacker vs 5GC anomaly detectors | todo |
| chen2024marionette | Marionette: OpenFlow Link Discovery Topology Poisoning | ACM CCS 2024 | https://arxiv.org/abs/2408.16940 | SDN control-plane attack; detection open | todo |
| zhou2023netbeacon | NetBeacon: Efficient Intelligent Network Data Plane | USENIX Security 2023 | https://www.usenix.org/conference/usenixsecurity23/presentation/zhouguangmeng | In-switch tree IDS, MIT artifact | todo |
| brandino2024halids | HALIDS: Detecting Attacks at Switching Speed | IEEE ICNP 2024 | https://publications.ait.ac.at/de/publications/detecting-attacks-at-switching-speed-aiml-and-active-learning-for/ | P4 IDS + active learning fallback | todo |
| kakani2025 | Mitigating ML-Driven Adversarial Attacks on xApps | IEEE OJCOMS 2025 | https://www.dfki.de/web/forschung/projekte-publikationen/publikation/17015 | xApp detector 92% to 16% under attack | todo |
| dimou2025argos | ARGOS: Anomaly Recognition through O-RAN Sensing | arXiv 2506.06916, 2025 | https://arxiv.org/abs/2506.06916 | IDS xApp reference design | todo |
| soltani2024ric | Intelligent Control in 6G Open RAN: Security Risk or Opportunity? | arXiv 2405.08577, 2024 | https://arxiv.org/abs/2405.08577 | RIC security survey | todo |
| wang2024lordma | LoRDMA: Low-Rate DoS in RDMA Networks | NDSS 2024 | https://ndss-symposium.org/ndss-paper/lordma-a-new-low-rate-dos-attack-in-rdma-networks | Only recent RDMA attack with NCCL impact | todo |
| mcdonald2025whisperleak | Whisper Leak | arXiv 2511.03675, 2025 | https://arxiv.org/abs/2511.03675 | Encrypted LLM traffic side channel | todo |
| zhang2025agentprint | AgentPrint: LLM User Privacy via Traffic Fingerprinting | arXiv 2510.07176, 2025 | https://arxiv.org/pdf/2510.07176 | Agent traffic fingerprinting | todo |
| zhang2026netinspector | NetInspector: agentic verification of LLM-generated IBN policies | arXiv 2609.21103, 2026 | https://arxiv.org/abs/2609.21103 | LLM-IBN security, synthetic eval | todo |
| kilic2026shap | SHAP-Guided Feature Selection for Cross-Dataset Generalization in NIDS | IEEE Access 14, 2026 | https://ada.atilim.edu.tr/items/d25563f0-322c-4384-b748-fe9d5695f1af | CLOSEST PRIOR WORK: XGBoost+TreeSHAP, CICIDS2017 to 2018, macro-F1 0.119 to 0.207 | todo |
| layeghy2022dinids | DI-NIDS: Domain Invariant Network Intrusion Detection System | arXiv 2210.08252; Knowledge-Based Systems 2023 | https://arxiv.org/abs/2210.08252 | Domain-adversarial encoder + OSVM on NFv2 datasets; template for the hybrid's DNN | todo |
| roy2025dirl | Improving Intrusion Detection with Domain-Invariant Representation Learning in Latent Space | arXiv 2312.17300 v5; ECML 2025 | https://arxiv.org/abs/2312.17300 | MI-bottleneck domain generalisation; alternative encoder loss | todo |
| ahmed2024hybrid | Hybrid bagging and boosting with SHAP-based feature selection for IDS | Scientific Reports 2024 | https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11652632/ | Hybrid+SHAP on CIC-IDS2017 only; lists SHAP instability as limitation | todo |
| zarkadis2026 | Tree ensembles vs DNN vs stacking on UAVIDS-2025 | arXiv 2605.13922, 2026 | https://arxiv.org/abs/2605.13922 | XGBoost beats DNN and stacking; why plain stacking is not a contribution | todo |
| sarhan2021features | Evaluating Standard Feature Sets Towards Increased Generalisability and Explainability of ML-based NID | arXiv 2104.07183 | https://arxiv.org/abs/2104.07183 | NetFlow vs CICFlowMeter feature sets for generalisation + SHAP | todo |
| arreche2024exai | E-XAI: Evaluating Black-Box XAI Frameworks for NID | IEEE Access 2024 | DOI 10.1109/ACCESS.2024.3365140 | Six metrics for scoring SHAP/LIME explanations | todo |
| kumar2025xai | Evaluating the Explainability of SOTA DL-based NIDS | arXiv 2408.14040 v4, 2025 | https://arxiv.org/abs/2408.14040 | Explainers disagree on NIDS models | todo |
| kalakoti2025xai | Evaluating XAI for DL-based NIDS Alert Classification | ICISSP 2025 | https://arxiv.org/abs/2506.07882 | DeepLIFT beat SHAP on real SOC alerts | todo |
| ring2017cidds | Flow-based benchmark data sets for intrusion detection (CIDDS-001) | ECCWS 2017 / JIW 2017 | https://www.hs-coburg.de/cidds | OpenStack cloud NetFlow dataset; CIDDS-002 successor | todo |
| aldribi2020isotcid | Cloud intrusion detection based on change tracking and a new benchmark dataset (ISOT-CID) | UVic dissertation 2020 | https://onlineacademiccommunity.uvic.ca/isot/2022/11/25/cloud-security-datasets/ | Real-cloud 8 TB dataset; access by agreement | todo |
| sharafaldin2018 | Toward Generating a New Intrusion Detection Dataset and Intrusion Traffic Characterization (CIC-IDS2017 / CSE-CIC-IDS2018) | ICISSP 2018 | https://www.unb.ca/cic/datasets/ids-2018.html | Canonical citation; 2018 set collected on AWS | todo |
| layeghy2022gen | On Generalisability of ML-based NIDS | arXiv 2205.04112; Comput. Electr. Eng. 2023 (DOI 10.1016/j.compeleceng.2023.108692) | https://arxiv.org/abs/2205.04112 | 12 NFv2 pairs; avg F1 decay 56%; asymmetry; TTL/protocol shortcuts | read (extract) |
| pawlicki2025da | SHAP Insights Into Domain Adaptation in NetFlow NIDS | ARES 2025 | https://zenodo.org/records/16919506 | CORAL/DANN/ADDA/CDAN fail CIC2018->UNSW (attack recall <=0.35) | todo |
| koukoulis2025contrastive | Contrastive pretraining transformers for cross-dataset IDS | arXiv 2505.08816; IFIP Networking 2025 | https://github.com/koukipp/contrastive_transformers_ids | Few-shot cross-dataset AUC; code available | todo |
| guerra2026transfer | Benchmark Study of Feature Transferability in IoT Botnet Attack Detection | ICISSP 2026, arXiv 2602.23874 | https://arxiv.org/abs/2602.23874 | Argus vs Zeek vs CICFlowMeter transferability; feature-engineering guidelines | todo |
| onuorah2026 | Toward Generalization and Interpretable DL-Based IDS for Heterogeneous Networks | IEEE Access 14, 2026, DOI 10.1109/ACCESS.2026.3675887 | https://pure.uj.ac.za/en/publications/toward-generalization-and-interpretable-deep-learning-based-intru/ | CNN+SMOTE+SHAP on CSE-CIC-IDS2018/CICDDoS2019/TII-SSRC-23; numbers not retrieved | todo |
| farasat2024testbed | Comprehensive Testbed and Dataset for ML-Based Intrusion Detection (Kubernetes, eBPF/XDP) | arXiv 2410.18332 | https://arxiv.org/abs/2410.18332 | Cloud-native testbed; small dataset claimed public, no link | todo |
| erbacher2022baserate | Base-Rate Fallacy Redux and a Deep Dive Review in Cybersecurity | arXiv 2203.08801 | https://arxiv.org/abs/2203.08801 | Modern false-alarm arithmetic; 12 alerts/analyst-hour | todo |
| warnecke2020 | Evaluating Explanation Methods for Deep Learning in Security | IEEE EuroS&P 2020 | https://arxiv.org/abs/1906.02108 | Definitions: descriptive accuracy, sparsity (MAZ), stability, efficiency; KernelSHAP 0.4-9 s/sample | todo |
| alquliti2025fam | Evaluating Explanation Quality in X-IDS Using Feature Alignment Metrics | arXiv 2505.08006 | https://arxiv.org/abs/2505.08006 | FAP/FAR/FAF1 vs ATT&CK/D3FEND feature sets; FAF1 0.06-0.32 | todo |
| tolay2026cost | Beyond Detection Accuracy: Measuring Explanation Cost, Stability, Utility (IoT IDS) | arXiv 2608.10349 | https://arxiv.org/abs/2608.10349 | TreeSHAP latency XGBoost ~0.29 ms vs RF ~140 ms per sample | todo |
| arreche2025whitebox | Comparative Analysis of DNN-based White-Box XAI Methods in Network Security | arXiv 2501.07801 | https://arxiv.org/abs/2501.07801 | LRP/IG/DeepLIFT on six metrics | todo |
| hedstrom2023quantus | Quantus: XAI evaluation toolkit | JMLR 24(34) 2023 | https://jmlr.org/papers/v24/22-0142.html | 35+ metrics; image-first | todo |
| agarwal2022openxai | OpenXAI: Transparent Evaluation of Model Explanations | NeurIPS 2022 D&B, arXiv 2206.11104 | https://arxiv.org/abs/2206.11104 | Tabular-native faithfulness/stability metrics | todo |
| hooker2019roar | A Benchmark for Interpretability Methods in DNNs (ROAR) | NeurIPS 2019, arXiv 1806.10758 | https://arxiv.org/abs/1806.10758 | Remove-and-retrain fidelity; random baseline | todo |
| alvarezmelis2018 | On the Robustness of Interpretability Methods | ICML WHI 2018, arXiv 1806.08049 | https://arxiv.org/abs/1806.08049 | Local Lipschitz stability | todo |
| nauta2023co12 | From Anecdotal Evidence to Quantitative Evaluation Methods (Co-12) | ACM CSUR 2023, arXiv 2201.08164 | https://arxiv.org/abs/2201.08164 | Organising properties for the XAI chapter | todo |
| nadeem2023sok | SoK: Explainable ML for Computer Security Applications | IEEE EuroS&P 2023, arXiv 2208.10605 | https://arxiv.org/abs/2208.10605 | Stakeholder taxonomy; 14% user studies | todo |
| lee2002cost | Toward cost-sensitive modeling for intrusion detection and response | J. Computer Security 10, 2002 | https://www.fsl.cs.sunysb.edu/docs/cost-jcs-lee/jcs_lee.pdf | Standard IDS/IRS cost model | todo |
| rezapour2024irs | A Systematic Mapping Study on Intrusion Response Systems | IEEE Access 12, 2024, DOI 10.1109/ACCESS.2024.3381998 | https://doaj.org/article/ecb398ac0814433790948097b6f6c716 | 287-paper IRS map; no reliable fully-automated IRS | todo |
| hammar2024irs | Learning Near-Optimal Intrusion Responses Against Dynamic Attackers | IEEE TNSM 21(1) 2024, arXiv 2301.06085 | https://arxiv.org/abs/2301.06085 | Threshold structure of optimal response | todo |
| chowdhury2026trust | Decision-Aware Trust Signal Alignment for SOC Alert Triage | arXiv 2601.04486 | https://arxiv.org/abs/2601.04486 | Calibration + cost-weighted thresholds | todo |
| jalalvand2025l2d | Adaptive alert prioritisation via learning to defer with human feedback | arXiv 2506.18462 | https://arxiv.org/abs/2506.18462 | Learning-to-defer baseline for escalation tier | todo |
| stakhanova2007 | A taxonomy of intrusion response systems | IJICS 1(1/2) 2007 | https://www.inderscience.com/offer.php?id=12248 | Classic IRS taxonomy | todo |
| cantone2024data | LycoS-Unicas-IDS2018 dataset repository | GitHub | https://github.com/MarcoCantone/LycoS-Unicas-IDS2018 | Corrected CSE-CIC-IDS2018 features used by Cantone | todo |
| liu2022improved | CNS2022 improved CIC-IDS2017 and CSE-CIC-IDS2018 (datasets) | IEEE CNS 2022 | https://intrusion-detection.distrinet-research.be/CNS2022/Datasets/ | Live corrected datasets (328 MiB / 9.7 GiB); relabel "Attempted" as Benign | todo |
| engelen2021data | WTMC2021 corrected CIC-IDS2017 (dataset + code) | WTMC 2021 | https://intrusion-detection.distrinet-research.be/WTMC2021/Dataset/dataset.zip ; https://github.com/GintsEngelen/WTMC2021-Code | 318 MiB; fixed CICFlowMeter at github.com/GintsEngelen/CICFlowMeter | todo |
| rosay2021lycos | LYCOS-IDS2017 download | WI-IAT 2021 / ICISSP 2022 | https://lycos-ids.univ-lemans.fr/download-lycos-ids2017.html | 508 MiB; LycoSTand extractor | todo |
| layeghy2026code | DI-NIDS reference implementation | GitHub, BSD-3 | https://github.com/layeghy/DI-NIDS | Created 2026-08; official status unverified; needs pandas<3 | todo |
| sarhan2020nprobe | NetFlow Datasets for ML-based NIDS (v1, nProbe command) | arXiv 2011.09144 | https://arxiv.org/abs/2011.09144 | Documents the nProbe v9 extraction command for NF datasets | todo |
