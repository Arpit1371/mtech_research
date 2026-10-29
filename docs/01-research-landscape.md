# Research landscape: intrusion detection in AI networking

Compiled 2026-10-26 from three parallel literature sweeps (web search + arXiv abstract pages). Every paper listed was seen in a search result or fetched page; items marked **(unverified)** came from a snippet only. Reported numbers are abstract-level unless stated. Re-check anything you cite.

---

## 1. First decision: what does "AI networking" mean for this thesis?

The phrase maps to three different research areas with very different literature density, novelty and feasibility. Settle this with your supervisor before anything else.

| # | Interpretation | What the IDS protects | Literature (2024-26) | Novelty | Feasible on M2 Pro + free Colab? |
|---|---|---|---|---|---|
| I | **AI *for* network security**: ML/DL-based NIDS on ordinary IP traffic | Enterprise / IoT networks | Very dense, saturated | Low unless you attack a known weakness (generalisation, drift, shortcuts, adversarial) | Yes, fully |
| II | **IDS for AI-driven networks**: SDN / P4 in-network ML, O-RAN near-RT RIC xApps, 5G core, intent-based networking | The programmable / AI-controlled network and its control loop | Dense for P4 and O-RAN (many 2025-26 preprints); SDN-DDoS-with-ML is saturated | Medium-high | Yes with Mininet/BMv2 or Docker-based 5G/RIC emulation; steeper learning curve |
| III | **Networking *for* AI**: GPU-cluster fabrics (RoCE/InfiniBand/NCCL), LLM-serving and MCP/agent traffic | AI infrastructure itself | Thin. Attack papers exist (LoRDMA, ReDMArk, Whisper Leak, AgentPrint); no ML detectors, no public datasets | High | RDMA: no (needs hardware or ns-3). LLM-serving/MCP traffic: yes, build the dataset locally |

**Recommendation.** Do not pick I in its generic form ("deep model X on CIC-IDS2017, 99% accuracy"). That result is the baseline, not a contribution, and examiners who know the 2022-26 critiques will see through it. Pick either (a) I with a rigour angle, (b) II with a public 5G/O-RAN dataset, or (c) III on LLM-serving traffic. Section 3 ranks concrete problem statements.

---

## 2. What the 2024-2026 literature actually says

### 2.1 In-dataset accuracy is meaningless; cross-dataset and temporal results are what count
- Cantone, Marrocco, Bria, *On the Cross-Dataset Generalization of ML for NID*, IEEE Access 2024, https://arxiv.org/abs/2402.10974 : four classifiers, four datasets; near-perfect in-dataset, "largely commensurate with random chance" cross-dataset.
- Atif & Ali, *Three-Axis Stress Test of LLM vs Classical ML for NID*, arXiv 2609.13511 (Sep 2026): XGBoost and RoBERTa-LoRA tie in-dataset; XGBoost wins cross-dataset by ~15 F1; LoRA model wins under evasion by ~17 F1.
- *The Evaluation Protocol Is the Hidden Variable*, preprints.org 202606.0903 **(unverified)**: macro-F1 ~0.8 under random split vs ~0.02 under chronological split on CSE-CIC-IDS2018.
- Oct 2026 cross-dataset preprint, preprints.org 202610.1041 **(unverified)**: across 12 NetFlow-v3 dataset pairs, source data helps ~8% and hurts ~38%; target-only training at 5% labels matches fine-tuning.
- Bilal et al., *Dataset-centric evaluation of federated IDS in IoT*, Scientific Reports Jan 2026: in-domain ~98% macro-F1, up to a 30-point drop cross-dataset.
- Pendlebury et al., TESSERACT, USENIX Security 2019; Andresini et al., INSOMNIA, AISec 2021: time-ordered splits and drift handling are mandatory for credible NIDS evaluation.

### 2.2 The standard datasets are flawed, and corrected versions exist
- CIC-IDS2017: Engelen et al. (WTMC 2021) found >25% of flows are artefacts and >20% of traffic was reconstructed or relabelled; Lanvin et al. (CRiSIS 2022, https://hal.archives-ouvertes.fr/hal-03775466) found misordering, duplicates and mislabelled port scans; Rosay et al. (ICISSP 2022) found five classes of CSV problems.
- CSE-CIC-IDS2018: Liu et al. (IEEE CNS 2022, https://archive.cps-vo.org/node/97449) found undocumented errors in orchestration, feature generation and labels.
- Corrected variants: WTMC2021 (Engelen; fixed CICFlowMeter at https://hub.docker.com/r/thelurps/gintsengelen-cicflowmeter), LYCOS-IDS2017 (Rosay), LycoS-Unicas-IDS2018 (Cantone), NFS-2023 (Pekar & Jozsa, https://arxiv.org/abs/2401.16843, re-flowed with NFStream).
- Caveat from Pekar & Jozsa: Random Forest stays near-perfect on original and corrected versions alike. Fixing labels does not make the benchmark hard.
- Survey: Goldschmidt & Chudá, *Network Intrusion Datasets: A Survey, Limitations, and Recommendations*, Computers & Security 156 (2025), https://arxiv.org/abs/2502.06688 (89 datasets).

### 2.3 Pretrained "traffic foundation models" are promising but shortcut-prone
- Models: netFound (Beltiukov et al., arXiv 2310.17025 v5 2026; code, weights and a 4.2B-flow corpus released), TrafficLLM (arXiv 2504.04222), Lens (arXiv 2402.03646), NetMamba (arXiv 2405.11449), plus landmarks ET-BERT (WWW 2022), YaTC (AAAI 2023), NetGPT (2023).
- Critique: Zhao et al., *The Sweet Danger of Sugar: Debunking Representation Learning for Encrypted Traffic Classification*, **ACM SIGCOMM 2025**, https://arxiv.org/abs/2507.16438 : the "98% accuracy" of prior pretrained models comes from data-preparation shortcuts; without them they perform poorly.
- Wang et al., *Bias in the Shadows* (BiasSeeker), arXiv 2601.10180 (Jan 2026): model-agnostic shortcut-feature detection across 19 datasets.
- Both critiques target traffic *classification*. Nobody has yet applied them systematically to *intrusion* datasets. That is an open, bounded gap.

### 2.4 LLMs are poor detectors, possibly useful explainers or rule writers
- Houssel et al., arXiv 2408.04342: "LLMs are unfit for the detection of malicious NetFlows"; value as explainers with RAG and function calling.
- Rehman et al., *From Flows to Words*, arXiv 2510.17883 (Oct 2025): zero/few-shot 7B LLM reaches macro-F1 ~0.78 on 200 UNSW-NB15 flows and degrades at 2,000; tabular baselines are more stable and far faster.
- Kim et al., arXiv 2503.20798 **(snippet)**: LLM tokenizers add no accuracy over a non-LLM payload baseline (99.969% vs 99.971%) at higher cost.
- Alert-Bench (Tsegai & Gao, NeurIPS 2026 workshop): every LLM-agent SOC-triage configuration misses >= 40% of attack alerts; more compute does not help.
- Rule generation: GenTI (arXiv 2606.05844) and GRIDAI (arXiv 2510.13257) generate Snort/Suricata rules; self-reported only.

### 2.5 Adversarial evaluation must be problem-space constrained
- elShehaby & Matrawy, *Perturb-ability Score*, arXiv 2409.07448: scores how easily each flow feature can be manipulated by an attacker; masking high-PS features cuts evasion success at little accuracy cost.
- Grini et al., arXiv 2505.01328: up to 80% of unconstrained adversarial flow examples are invalid traffic.
- Ennaji et al., *Transferability Feasibility Score*, arXiv 2504.08480.
- Wang et al., *Are We There Yet? Unraveling the SOTA Graph NIDS*, arXiv 2503.20281: reproducibility study; large gaps in reported false-positive rates and robustness of E-GraphSAGE-family models.

### 2.6 Drift and continual learning are active but un-benchmarked
- SSF (Zhang et al., IEEE INFOCOM 2025, https://arxiv.org/abs/2412.16264), NetSight (arXiv 2508.15100), DriftXpert (arXiv 2609.08623), E2D2 (IFIP Networking 2026). Each uses a different dataset and protocol; no common time-aware benchmark exists. MAWIFlow (ICISSP 2026, https://arxiv.org/abs/2506.17041) and the NF-v3 temporal datasets (Luay et al., arXiv 2503.04404) are the only multi-year flow benchmarks found.

### 2.7 AI-native networks: detectors collapse under adaptive attackers
- P4 / in-network ML: Planter (SIGCOMM CCR 2024), NetBeacon (USENIX Sec 2023, MIT-licensed artifact), Jewel (2024), HALIDS (IEEE ICNP 2024; P4 IDS with active-learning fallback, BMv2 only), Akem & Fiore P4-UPF IDS (2024, 98% F1). Surveys: Rukh & Ahmed (Computer Networks 281, 2026), Singh (arXiv 2507.22165).
- SDN control plane: Marionette (Chen et al., **ACM CCS 2024**, https://arxiv.org/abs/2408.16940) poisons topology via RL-chosen flow rules on 5 controllers (CVE-2024-37018); learned detection of such behaviour is open. Topology-poisoning dataset: https://data.mendeley.com/datasets/vsbstvcwhw.
- O-RAN: ARGOS IDS xApp (arXiv 2506.06916), Moore et al. LLM IDS xApp (IEEE ICC 2025), Hung et al. E2/xApp anomaly detection (IEEE OJCOMS 2025, rule-based), Kakani et al. (IEEE OJCOMS 2025: adversarial xApp drops detector accuracy 92% to ~16%), Gajjar et al. black-box evasion of O-RAN apps (arXiv 2510.18160), MalO-RAN backdoored DRL xApps (Computer Networks 2025).
- 5G core: SAGE-5GC (Manca et al., arXiv 2602.03596, Feb 2026) shows 5GC anomaly detectors are evaluated under unrealistic i.i.d. and non-adaptive-attacker assumptions and are degraded by a genetic-algorithm attacker.
- Intent-based networking with LLMs: NetInspector (arXiv 2609.21103, synthetic benchmark), MARE (arXiv 2604.06856, architecture only), sIBN (arXiv 2511.05133). Brand new, evaluations are synthetic.

### 2.8 Networking for AI: attacks exist, detection does not
- RDMA/RoCE: LoRDMA (**NDSS 2024**) low-rate DoS exploiting PFC + DCQCN cuts NCCL throughput 18-56%; ReDMArk (USENIX Sec 2021), Bedrock (USENIX Sec 2022, in-network auth/ACL, no learned detection), NeVerMore (2022). No ML detector and no public dataset found.
- LLM serving: Whisper Leak (McDonald et al., arXiv 2511.03675, Nov 2025) infers prompt topic from encrypted streaming packet sizes and timing across 28 LLMs; AgentPrint (arXiv 2510.07176) fingerprints user-to-agent traffic with F1 0.866. Both attack-side; defences partial. Industry-only work on MCP traffic detection (Cloudflare). No academic MCP/agent-to-agent traffic-monitoring paper found.
- Cluster monitoring work (Reveal, Mycroft, NIXT, CCL-D, Collie, Hawkeye) is performance diagnosis, not security.

---

## 3. Candidate thesis directions, ranked

Each is sized for ~12 months alongside a full-time job on an M2 Pro (32 GB, no GPU) with free Colab/Kaggle for occasional GPU work.

### A. (Recommended) Generalisation-first NIDS: do traffic foundation models buy anything over XGBoost once leakage, time and shortcuts are controlled?
- **Problem.** Pretrained traffic models report 96-99% on classification benchmarks, but SIGCOMM 2025 shows those numbers are shortcut-driven, and every cross-dataset study shows classical models collapse to chance across networks. No study fine-tunes a traffic foundation model on intrusion data under a leakage-controlled, time-aware, cross-network protocol with target-only baselines at equal label budget.
- **Contribution.** A benchmark and findings paper: netFound (weights public) and NetMamba vs XGBoost/RF on NF-UNSW-NB15-v2, NF-CSE-CIC-IDS2018-v2 and one AI-network dataset (5G-CGAD or InSDN), under temporal splits, leave-one-dataset-out, label-budget curves (1%, 5%, 20%), a BiasSeeker-style shortcut audit, and a Perturb-ability-Score-constrained evasion test.
- **Why it fits you.** Pipeline engineering, reproducibility and careful evaluation are your strengths; no P4 or RAN stack to learn.
- **Resources.** CSVs fit in RAM; netFound fine-tuning on a subsample needs Colab GPU hours. Risk: netFound code may need adaptation to flow CSVs (it expects pcaps for some paths). Fallback: NetMamba or ET-BERT, or XGBoost-only with the rigour protocol as the contribution.
- **Ties "AI networking" in** by including 5G-CGAD (5G core GTP-U attacks, CC BY 4.0) or InSDN (SDN testbed) as the third network.

### B. Adaptive-attacker-aware anomaly detection for 5G core / O-RAN, with a latency budget
- **Problem.** SAGE-5GC and Kakani et al. show detectors for AI-native networks are evaluated under i.i.d. and non-adaptive assumptions and collapse under adaptive attackers. Near-RT RIC xApps also face a 10 ms-1 s decision budget nobody benchmarks against.
- **Contribution.** Benchmark tree, autoencoder, VAE and small transformer detectors on 5G-CGAD, 5G-NIDD, the 5GC PFCP dataset and NetsLab-5G O-RAN IDD under (i) temporal splits, (ii) a GA/black-box evasion attacker, (iii) measured inference latency and memory on CPU. Optionally deploy the best one as an xApp on the OSC near-RT RIC with srsRAN/OAI in Docker (virtual radio, no SDR).
- **Resources.** Public datasets; Docker. Risk: 5G/O-RAN stack learning curve, Colosseum access not available.
- **Literally "AI networking".** Strongest story for an examiner who reads the title literally.

### C. Network-level detection and defence for LLM-serving and MCP traffic
- **Problem.** Whisper Leak and AgentPrint show encrypted LLM/agent traffic leaks topic and agent identity, and MCP servers are a new lateral-movement surface, but there is no public dataset, no academic detector, and defences are partial.
- **Contribution.** Build and release a labelled dataset of streaming LLM sessions and MCP tool-call traffic (Ollama or vLLM + several MCP servers + a scripted client), reproduce the fingerprinting attacks, evaluate padding/batching defences, and train a detector for anomalous or rogue MCP-server behaviour. A dataset paper plus a detector paper.
- **Resources.** Laptop only; small models run on M2 Pro. Risk: supervisor may see fingerprinting as privacy rather than intrusion detection; frame the rogue-MCP-server / data-exfiltration detection as the IDS core.
- **Highest novelty, lowest hardware risk.**

### D. Adversarial robustness of in-switch (P4) tree-based IDS
- Show range-marked / table-constrained trees (NetBeacon, Planter style) are evadable by attacker-controllable features; harden via PS-based feature masking, ensembles, and HALIDS-style active-learning fallback. Mininet + BMv2 on laptop. Needs P4 comfort; Tofino unavailable.

### E. Learned detection of SDN topology poisoning / malicious flow-rule behaviour
- Marionette-style attacks on ONOS/Ryu in Mininet; anomaly detection on controller and OpenFlow telemetry; Mendeley topology-poisoning dataset as a starting point. Risk: attack code availability.

### F. Common time-aware drift benchmark for NIDS
- Re-run SSF, NetSight, DriftXpert, E2D2 under one protocol (temporal splits, delayed labels, MAWIFlow + NF-v3). Pure methodology thesis; feasible; less "AI networking" flavour unless paired with a 5G/SDN dataset.

Not recommended: anything RDMA/RoCE (no hardware, simulation fidelity is the thesis' weak point); LLM-as-the-detector (literature already says no); generic "new deep model on CIC-IDS2017".

---

## 4. Evaluation protocol to follow regardless of direction

Work through this list and report it in the thesis. It is what separates a credible 2026 NIDS thesis from the hundreds that are not.

1. **Arp et al. pitfalls P1-P10** (USENIX Security 2022, https://www.usenix.org/conference/usenixsecurity22/presentation/arp): sampling bias, label inaccuracy, data snooping, spurious correlations, biased parameter selection, inappropriate baseline, inappropriate measures, base-rate fallacy, lab-only evaluation, inappropriate threat model.
2. **Time-ordered train/test splits** (TESSERACT / INSOMNIA). Never random k-fold on flow data.
3. **Leave-one-dataset-out** on a shared feature set (NetFlow v2/v3 family makes this easy). Report within-dataset alongside.
4. **Metrics**: MCC, precision-recall AUC, and false alarms per day at a realistic attack base rate (Axelsson 2000; Sommer & Paxson 2010). Never accuracy alone.
5. **Controls**: label-shuffle control (should be at chance), RF/XGBoost baselines, target-only baseline at the same label budget.
6. **Dataset audit** before modelling: duplicates, label conflicts, leakage features (IPs, ports, timestamps). IDS2Eval (https://github.com/hashtagRR/IDS2Eval) has 28 checks.
7. **Shortcut audit**: BiasSeeker-style perturbation of suspect features.
8. **Adversarial test** with problem-space constraints (Perturb-ability Score, validity checks).
9. **Record the flow exporter and its settings** (CICFlowMeter version/fork, NFStream, Zeek). Same pcap yields different flows per exporter.
10. **Release code, seeds, and labelling logic.**

---

## 5. Dataset shortlist

| Dataset | Year | Why | Size | URL |
|---|---|---|---|---|
| NF-UNSW-NB15-v2 | 2021 | Shared 43-feature NetFlow set, laptop-sized | 2.39M flows | https://www.cyber.uq.edu.au/node/824 |
| NF-CSE-CIC-IDS2018-v2 | 2021 | Same feature set, second network for cross-dataset | 18.9M (subsample) | same |
| NF-*-v3 temporal variants | 2025-26 | Temporal features for drift work | varies | https://arxiv.org/abs/2503.04404 |
| LYCOS-IDS2017 / WTMC2021 | 2021-22 | Corrected CIC-IDS2017 if you must use it | ~2.8M | Rosay ICISSP 2022; Engelen WTMC 2021 |
| 5G-CGAD | 2025 | Open 5G core GTP-U attacks, CC BY 4.0, ~84 features | not stated | https://etsin.fairdata.fi/dataset/5dad463d-0306-455f-b47a-bef771b819fc |
| 5G-NIDD | 2022 | Real 5G test network, DoS + scans | not stated | https://arxiv.org/abs/2212.01298 |
| 5GC PFCP IDS | 2023 | SMF/UPF control-plane attacks | labelled | https://ieee-dataport.org/node/11131 |
| NetsLab-5G O-RAN IDD | n/a | O-RAN DoS/DDoS with radio telemetry (Kaggle) | n/a | https://netslab.ucd.ie/netslab-5g-oran-idd/ |
| InSDN | 2020 | SDN testbed, CC BY-NC | ~343k | https://aseados.ucd.ie/?p=177 |
| CICIoT2023 | 2023 | IoT scale, 33 attacks | 46.7M (subsample) | https://www.unb.ca/cic/datasets/iotdataset-2023.html |
| MAWIFlow | 2026 | Multi-year backbone flows for drift | n/a | https://arxiv.org/abs/2506.17041 |
| Topology-poisoning (SDN) | n/a | Only dataset for control-plane attacks | ~247k | https://data.mendeley.com/datasets/vsbstvcwhw |

Avoid NSL-KDD as primary evidence (2009, DARPA98 lineage, no pcap). Use CIC-IDS2017 only in a corrected form.

---

## 6. A 12-month plan (adjust to your semester calendar)

| Months | Milestone | Output |
|---|---|---|
| 1 | Fix interpretation with supervisor; read the 15 papers in section 7; write a 2-page problem statement | `docs/02-problem-statement.md` |
| 2 | Reproduce one strong baseline (XGBoost on NF-UNSW-NB15-v2 with temporal split + LODO) and one critique (cross-dataset collapse) | `experiments/00-baseline/` with seeds and results table |
| 3 | Dataset audit + leakage/shortcut checks on chosen datasets; pin flow-exporter settings | audit report in `notes/` |
| 4-6 | Core experiments (the direction's main contribution) | results, plots |
| 6 | Mid-term review; write literature review chapter | thesis ch. 2 |
| 7-8 | Adversarial / drift / latency evaluation (whichever applies) | results |
| 9 | Ablations, error analysis, failure cases | results |
| 10 | Paper draft (workshop or IEEE Access / Computers & Security scale) | paper |
| 11 | Thesis writing, code release | thesis draft, repo |
| 12 | Buffer, defence prep | final |

Keep every experiment a script with a fixed seed and a results CSV; never a notebook-only result.

---

## 7. Read these first (in order)

1. Arp et al., Dos and Don'ts of ML in Computer Security, USENIX Sec 2022. https://arxiv.org/abs/2010.09470
2. Sommer & Paxson, Outside the Closed World, IEEE S&P 2010. https://www1.icsi.berkeley.edu/pubs/networking/outsidethe10.pdf
3. Cantone et al., Cross-Dataset Generalization of ML for NID, IEEE Access 2024. https://arxiv.org/abs/2402.10974
4. Zhao et al., The Sweet Danger of Sugar, ACM SIGCOMM 2025. https://arxiv.org/abs/2507.16438
5. Beltiukov et al., netFound, arXiv 2310.17025 (v5 2026). https://arxiv.org/abs/2310.17025
6. Atif & Ali, Three-Axis Stress Test LLM vs classical NID, arXiv 2609.13511.
7. Houssel et al., Towards Explainable NID using LLMs, arXiv 2408.04342. https://arxiv.org/abs/2408.04342
8. Engelen et al., Troubleshooting an Intrusion Detection Dataset (CIC-IDS2017), WTMC 2021; and Pekar & Jozsa, arXiv 2401.16843.
9. Sarhan et al., NetFlow datasets for ML-based NIDS, arXiv 2101.11315. https://arxiv.org/abs/2101.11315
10. Pendlebury et al., TESSERACT, USENIX Sec 2019; Andresini et al., INSOMNIA, AISec 2021.
11. elShehaby & Matrawy, Perturb-ability Score, arXiv 2409.07448. https://arxiv.org/abs/2409.07448
12. Wang et al., Are We There Yet? Graph NIDS, arXiv 2503.20281. https://arxiv.org/abs/2503.20281
13. Manca et al., SAGE-5GC, arXiv 2602.03596. https://arxiv.org/abs/2602.03596
14. Chen et al., Marionette, ACM CCS 2024. https://arxiv.org/abs/2408.16940
15. McDonald et al., Whisper Leak, arXiv 2511.03675. https://arxiv.org/abs/2511.03675
16. Goldschmidt & Chudá, Network Intrusion Datasets survey, Computers & Security 2025. https://arxiv.org/abs/2502.06688
17. El Mahdaouy et al., DL for Contextualized NetFlow-Based NID (survey), arXiv 2602.05594. https://arxiv.org/abs/2602.05594

---

## 8. Questions to settle with your supervisor

1. Which interpretation (I, II or III in section 1) does the department mean by "AI networking"? Does the title have to stay literal?
2. Does the supervisor's group already have a dataset, testbed or hardware (P4 switch, SDRs, GPU server)? That can flip the ranking in section 3.
3. Is a publication expected (conference/journal tier)? That decides how much rigour vs. novelty to invest in.
4. Thesis timeline: submission month, mid-term review dates.
5. Is a systems contribution (xApp, P4 program, dataset release) valued more than an empirical-findings contribution?

---

## 9. Local environment (checked 2026-10-26)

Apple M2 Pro, 32 GB RAM, 132 GB free disk, Docker installed, Python 3.14 via Homebrew, `uv` available. No PyTorch, scikit-learn, Zeek, Suricata or tshark yet. Plan: a `uv`-managed Python 3.12 environment (broader wheel support than 3.14) with pandas, scikit-learn, xgboost, lightgbm, torch (MPS backend), and Zeek/NFStream in Docker when pcaps are needed.
