# 📡 Huawei RAN Configuration Audit & Golden Baseline Engine

![Python Version](https://img.shields.io/badge/Python-3.8%2B-blue)
![Pandas](https://img.shields.io/badge/Data-Pandas-green)
![Domain](https://img.shields.io/badge/Telecom-4G%20LTE%20%7C%205G%20NR-orange)

## 📖 Executive Summary

The **Huawei RAN Configuration Audit & Golden Baseline Engine** is an automated, high-throughput network standardization platform for 4G LTE and 5G NR Radio Access Networks (RAN). 

In modern telecommunications, configuration drift—where cell sites slowly deviate from standard engineering parameters due to manual errors or temporary workarounds—leads to silent network degradation, dropped calls, and suboptimal handover performance. 

This engine solves configuration drift by algorithmically ingesting raw Managed Object (MO) exports from Huawei CME/MAE, discovering the network's organic "Golden Baseline," and auditing every cell site against this standard. It transforms millions of rows of unreadable network data into actionable, targeted anomaly reports for engineering and optimization teams.

---

## ✨ Core Capabilities

* **🌐 Cross-Technology Support:** Seamlessly processes multi-layer LTE (FDD & TDD) and 5G NR configurations.
* **🗺️ Automated Topology Discovery:** Dynamically links 5G gNodeBs to anchor LTE eNodeBs and maps localized hardware IDs to global RF bands (e.g., `L21`, `L18`, `N35`) and carrier/beam profiles.
* **🧩 Intelligent Bitmask Decompilation:** Automatically detects and shatters complex Huawei compound switches (e.g., `SwitchA-1&SwitchB-0`) into individual boolean components for microscopic auditing.
* **📊 Statistical Baseline Generation:** Derives the true network baseline mathematically based on parameter deployment density, rather than relying on outdated static vendor documentation.
* **🎯 Precision Anomaly Detection:** Outputs exact mismatches with a side-by-side comparison of the "Expected" vs. "Configured" state.

---

## ⚙️ How It Works: The Execution Engine

To standardize configurations across a highly heterogeneous network, the engine executes a strict 3-stage processing pipeline to **Group**, **Study**, and **Audit** the parameters.

### 1. Data Ingestion & RF Abstraction (The Clustering Phase)
Raw network dumps cannot be compared directly because every site has a unique hardware identity (e.g., Site ID, Cell Name, Local Cell ID). To compare "apples to apples," the engine strips away these unique identifiers and translates them into **Abstracted Relation Groups**.
* **Primary Key Normalization:** Site-specific identifiers (NodeB names, IP addresses, tracking areas) are dynamically bypassed.
* **Band & Carrier Translation:** Local Cell IDs (e.g., Cell 1, Cell 2) are cross-referenced with internal and external network databases to deduce their actual RF function (e.g., `L21_1st_Carrier`, `N35_2nd_Beam`).
* **The Result:** A site's unique Cell 1 is perfectly clustered with every other site's Cell 1 that shares the exact same RF band and carrier profile across the entire network.

### 2. Statistical Mode Analysis (The Study Phase)
Once the parameters are clustered into their logical RF groups, the engine studies the distribution of values for *every single parameter* across the network.
* **Mode Calculation:** For any given parameter (e.g., `CellReselectionPriority`), the engine counts all configured values across the cluster. The value with the highest occurrence (the Mode) is declared the **Golden Baseline**.
* **Confidence Scoring:** The engine calculates a "Confidence %" for each baseline value (e.g., if 9,500 out of 10,000 cells use value '7', the baseline confidence is 95%).
* **Fallback Tracking:** It records the 2nd and 3rd most common values to help engineers identify planned regional variations versus pure configuration errors.

### 3. Compliance Splitting: Common vs. Non-Common (The Audit Phase)
With the Golden Baseline established mathematically, the engine re-evaluates the original raw network dump against this new standard to split the data into two distinct categories:

* ✅ **Common Configurations (The Baseline Matches):** 
  If a site's parameter matches the Golden Baseline precisely, it is routed to the Common configuration file. This proves the site is fully standardized and operating as intended.
  
* ❌ **Non-Common Configurations (The Anomalies):**
  If a site's parameter deviates from the Golden Baseline, it is flagged as an anomaly. The engine routes this to a vertical anomaly log, explicitly detailing:
  * The exact Site and Managed Object (MO).
  * The Parameter Name.
  * The **Baseline Expected Value**.
  * The **Actual Configured Value**.

---

## 📂 Output Deliverables

The engine generates four core deliverables directly to the output directory:

1. **`Statistical_Confidence_Matrix.xlsx`** 
   * *Target Audience: Management & Lead Architects*
   * A high-level health report showing exactly how standardized the network is per parameter, highlighting fragmented configurations that require governance.
2. **`Golden_Baseline.xlsx`**
   * *Target Audience: Rollout & Integration Teams*
   * A clean, universal configuration template generated dynamically from the live network, ready to be used for new site rollouts or script generation.
3. **`Common_Configs.xlsx`**
   * *Target Audience: Audit & Compliance Teams*
   * A verified audit trail of all standardized parameters proving network compliance.
4. **`NonCommon_Configs.xlsx` (Anomaly Log)**
   * *Target Audience: RF Optimization & Operations Teams*
   * A targeted hit-list of configuration errors, enabling engineers to immediately execute MML correction scripts to resolve network degradations.
