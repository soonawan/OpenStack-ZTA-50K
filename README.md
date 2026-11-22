# OpenStack-ZTA-50K: Synthetic Zero Trust Cloud Access-Control Dataset

[[License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[[Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)

**OpenStack-ZTA-50K** is a reproducible synthetic evaluation corpus designed to benchmark Zero Trust Architecture (ZTA) access-control policies, deterministic rule checks, and machine-learning risk scorers in OpenStack-like cloud environments (spanning Nova, Neutron, and Cinder).

---

## Dataset Overview

Each generated corpus contains **50,000 labeled access requests** distributed across **200 simulated users** and **4 resource sensitivity/privilege tiers** (Tier 1: public read-only; Tier 2: internal project resources; Tier 3: sensitive configuration; Tier 4: administrative actions).

### Class Distribution (per 50,000-request run)

| Class / Scenario | `label` | `attack_type` | Count | Share |
| :--- | :---: | :--- | :---: | :---: |
| Benign Traffic | `0` | `benign` | 42,500 | 85.0% |
| Unauthorized Access | `1` | `unauthorized_access` | 1,500 | 3.0% |
| Credential Theft | `1` | `credential_theft` | 1,500 | 3.0% |
| Insider Threat | `1` | `insider_threat` | 1,500 | 3.0% |
| Lateral Movement | `1` | `lateral_movement` | 1,500 | 3.0% |
| DDoS | `1` | `ddos` | 1,500 | 3.0% |

---

## Schema & Feature Dictionary (`openstack_zta_50k_seed_42.csv`)

| Column | Type | Description |
| :--- | :--- | :--- |
| `request_id` | `string` | Unique request identifier (`req_000001` to `req_050000`) |
| `user_id` | `string` | Simulated user identifier (`user_001` to `user_200`) |
| `authorized_tier` | `int` | Maximum privilege tier authorized for the user (`1`–`4`) |
| `mfa_status` | `int` | Binary MFA freshness indicator (`1` = verified within window, `0` = otherwise) |
| `device_compliant` | `int` | Binary endpoint posture compliance flag (`1` = compliant, `0` = non-compliant) |
| `t_idle` | `float` | Elapsed idle time (in seconds) since last MFA verification |
| `resource_sensitivity` | `int` | Target resource tier (`1`–`4`) |
| `behavioral_deviation` | `float` | Deviation score between current request context and user historical baseline |
| `network_risk` | `float` | Normalized network-origin risk score in `[0.0, 1.0]` (ASN reputation / geo-velocity) |
| `request_rate` | `float` | Request frequency (requests per minute per principal) |
| `tier_mismatch` | `int` | Binary indicator (`1` = privilege/tier mismatch, `0` = no mismatch) |
| `attack_type` | `string` | Specific traffic category (`benign` or one of the 5 attack types) |
| `label` | `int` | Binary ground-truth target (`0` = benign, `1` = attack) |

---

## Reproducing the Dataset

### 1. Install Dependencies

```bash
pip install numpy pandas scikit-learn==1.3.0
```

### 2. Generate a Single Seed (e.g., Seed 42)

```bash
python generate_openstack_zta_dataset.py --seed 42 --out-dir data
```

### 3. Generate All 20 Evaluation Seeds (Seeds 42–61)

```bash
python generate_openstack_zta_dataset.py --all-seeds --out-dir data
```

---

## Intended Use & Limitations

**Intended Use:** Controlled comparative benchmarking of RBAC, rule-based Zero Trust, and ML-assisted risk scoring across overlapping benign and attack distributions.

**Limitations:** Distributions are synthetically calibrated rather than replayed from live production telemetry, attackers are modeled non-adaptively, and downstream service execution is outside the access-decision simulation.

---

## Citation

If you use this dataset or generator script in your research, please cite the accompanying study:

> Muhannad Tahboush, Hasan Abualese, Mary Agoyi, Adel Hamdan, and Mohammad H. Baniata, "A Simulation-Based Evaluation of an AI-Assisted Zero Trust Security Framework For OpenStack Cloud Environments," 2026.

---

> **Note on repository size:** If we also include the `openstack_zta_50k_seed_42.csv` file as full (`50,000` rows, ~3.5–4 MB), GitHub handles it easily — just avoid committing all 20 CSV files (~70–80 MB total) directly to Git if you prefer keeping the repository lightweight; instead, let users generate seeds `42–61` via `--all-seeds` or attach a `.zip` archive to your GitHub / Zenodo release.
