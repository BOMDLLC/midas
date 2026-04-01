Based on the **Master Work Breakdown Schedule (v8)** and the **Product Roadmap**, the structural priority follows a "Foundation-First" sequence. The system is architected so that each Wave provides the data integrity required for the next level of automation.

The hierarchy of priority is divided into three primary "Phases" of the program:

### 1. Hardened Foundations (Current Focus: Waves 1–3)
**Objective:** Eliminate silent failures and ensure every run is identical (idempotency).
* **Epic: Dead-Letter Queue (DL) & Error Visibility:** (Priority: **CRIT/HIGH**)
    * *Structural Role:* Prevents the system from proceeding with missing data (e.g., FRED or SOFR failures).
    * *Key Item:* `DL-3` (Write FAILED.json) to reduce conviction when data is missing.
* **Epic: AWAP Pattern & Schema Compat:** (Priority: **HIGH**)
    * *Structural Role:* "Audit-Write-Audit-Promote." Ensures the "Gold" layer isn't corrupted by malformed API responses.
    * *Key Item:* `SC-2` (validate_schema) injected into all fetch steps.
* **Epic: Idempotency:** (Priority: **HIGH**)
    * *Structural Role:* Allows for re-runs without duplicating logs or data.
    * *Key Item:* `ID-3` (write_atomic) to prevent partial file writes.

### 2. Corpus Intelligence (Mid-Term: Waves 4–6)
**Objective:** Transition from raw data to "Scored" alpha signals and ML readiness.
* **Epic: Scrapling Integration:** (Priority: **HIGH**)
    * *Structural Role:* Expands the news corpus beyond standard Western feeds (e.g., BoJ, PBOC, Caixin) to feed the GMISR reporter.
* **Epic: Scored Metrics & GMISR v2.0:** (Priority: **MED/HIGH**)
    * *Structural Role:* Hard-codes the math for HDS, VPI, and LFI so the AI doesn't have to "guess" the numbers.
* **Epic: ML Regime Classifier & Persistence:** (Priority: **MED**)
    * *Structural Role:* The "Brain" update. Uses HMM (Hidden Markov Models) to classify market regimes.
    * *Constraint:* This is structurally blocked by the **30-trade Kelly Gate** (Item `P4-3`).

### 3. Autonomous Orchestration (Long-Term: Wave 7+)
**Objective:** Scale the system into a cloud-native Medallion Architecture.
* **Epic: Databricks Medallion Architecture:** (Priority: **HIGH** for Wave 7)
    * *Structural Role:* Moving from local `.txt` files to Unity Catalog (Bronze → Silver → Gold).
    * *Key Item:* `ARCH-1` (Provisioning) is the absolute gate for all Wave 7 work.
* **Epic: Governance:** (Priority: **ONGOING**)
    * *Structural Role:* Ensuring the `SYNC_CONTRACT` (thresholds) stays in alignment with the ETL code.
    * *Critical Rule:* `GOV-3` — A mandatory NotebookLM ---corpus reload on every MAJOR ETL version bump (e.g., 2.0.0.0).

### Summary of Work Item Status (at v8)
| Category | Priority | Total Items | Done | Pts Total |
| :--- | :--- | :--- | :--- | :--- |
| **Wave 0 (Infra)** | CRIT | 20 | 12 | 29 |
| **Waves 1-3 (Foundations)**| HIGH | 21 | 6 | 75 |
| **Waves 4-5 (Intelligence)**| MED/HIGH | 36 | 1 | 101 |
| **Waves 6-7 (ML/Cloud)** | MED | 34 | 0 | 115 |

**Next Structural Gate:** You are currently transitioning from **Wave 0** into **Wave 1/2**. The most critical work items currently open are the **Cron Fixes (CRON-1/4)** and the **Dead-Letter Queue threading (DL-2c)**.

-------------------------------------------------------------------------------------------------

Applying **PgMP (Program Management Professional)** standards to the Dexter-Midas program requires shifting from "Task Management" to **Benefit Realization**. In a quantitative trading environment, the primary benefit is "Alpha Fidelity"—the assurance that the signal driving the trade is accurate, timely, and risk-adjusted.



### 1. Program Risk Management (Strategic Alignment)
In PgMP, risk is not just a bug; it is anything that threatens the **Benefit (Alpha)**.

* **The Risk Register (Active Architecture):**
    * **Data Integrity Risk (Structural):** If a FRED API change breaks the `net_liquidity` calculation, the "benefit" of the GMISR report vanishes.
    * **Mitigation (Wave 2/3):** Implementing the **AWAP Pattern** (Audit-Write-Audit-Promote) is your primary PgMP risk mitigation strategy. It prevents "corrupted benefits" from reaching the decision-making Gold layer.
* **Operational Risk (The "Local Node" Factor):**
    * Since you are running on a local K3s cluster, hardware failure is a program risk. 
    * **Mitigation (Wave 6b):** The **SLA Monitoring (OB-1)** and **Offline Observer (OO-2)** are your program-level controls to detect if the "factory" has stopped producing.
* **Modeling Risk (Calculation Drift):**
    * **Mitigation:** Your requirement from **2026-02-25** to suffix metrics with the model name is a classic PgMP quality control to ensure "configuration management" across the program.

### 2. Program Priority Management (The "Fast Flow" Logic)
PgMP prioritizes work based on **interdependencies** and **weighted benefits**, rather than just "urgency."

* **The Critical Path (The "Kelly Gate"):**
    * Structurally, the **ML Regime Classifier (P4-1)** has the highest potential benefit but the highest risk. 
    * **PgMP Priority:** You cannot build the HMM Brain (Wave 6) until the Parquet Snapshot Layer (Wave 6) is stable. Therefore, the "Data Foundry" infrastructure tasks are technically higher priority than the "Quant Lab" modeling tasks because they are **enabling dependencies**.
* **Component-Based Sprints:**
    * **Priority 1: Infrastructure (Wave 0/1).** If the cron fires at the wrong time (CRON-1), the program produces zero benefit.
    * **Priority 2: Error Visibility (Wave 1/2).** Until the Dead-Letter Queue (DL-3) is active, you are "flying blind," which is a violation of PgMP program governance.
    * **Priority 3: Scaling (Wave 7).** Transitioning to Databricks is a "Strategic Growth" milestone. It is prioritized lower than "Foundational Stability" but higher than "Incremental Features."

### 3. Benefit Realization & Governance
To manage this effectively as a Program Manager (bomdadmin), you should utilize the **Governance Epics** in your WBS:

* **Sync Contract (GOV-1):** This is your **Program Governance Board**. Every time the ETL changes, the "Contract" must be updated. This ensures that the "Sentinel Alpha" team (strategy) stays in sync with the "Data Foundry" team (pipeline).
* **The 30-Trade Activation Gate:** This is a **Program Phase Gate**. In PgMP, you do not "realize" the benefit of an automated Kelly fraction until you have passed the validation gate. 



### Summary of Recommended Actions:
1.  **Enforce the "Circuit Breaker":** Do not allow any code from Wave 5 (Advanced Signals) to be promoted to "Live" until the Wave 2 (Data Quality Gates) are 100% "Done."
2.  **Monitor Team Cognitive Load:** Using your **Team Topologies** map, ensure the "Assurance & Ops" tasks aren't being ignored in favor of "Quant Lab" tasks. 
3.  **Audit Calculation Drift:** Weekly, check the `model_name` suffix in your TIER_AUDIT files to ensure the logic hasn't diverged during your Databricks migration.

-----------------------------------------------------------------------------------------

To translate the **Dexter-Midas** structural priorities into a management-ready Jira Roadmap, you need a multi-level hierarchy that groups these technical "Waves" into strategic "Themes." 

In Jira, this is best achieved by mapping **Waves** to **Jira Releases/Versions**, and **Epics** to the **Timeline**.

### 1. Jira Roadmap Hierarchy (Strategic View)
This structure allows management to see the "Big Picture" (Alpha Fidelity) while keeping the technical dependencies (ETL Fixes) visible.

| Jira Level | Program Mapping | Management Focus |
| :--- | :--- | :--- |
| **Theme (Label/Component)** | High-Level Category (e.g., *Hardened Foundations*) | Value Proposition (e.g., "Why are we doing this?") |
| **Release (Version)** | The "Wave" (e.g., *Wave 1: Error Visibility*) | Deadline & Delivery Window |
| **Epic** | Technical Sub-track (e.g., *Dead-Letter Queue*) | Budget, Resource, & Complexity |
| **Story** | Specific WBS Item (e.g., *DL-3: Write FAILED.json*) | Execution & Status |

---

### 2. The Timeline (2026-2027)
Based on the **WBS v8 Roadmap**, here is how the Epics map to a standard Jira Roadmap view:



#### **Phase 1: Hardened Foundations (Mar 2026 - July 2026)**
* **Q1-End (Mar):** **Infrastructure & Session Fixes (Wave 0)**
    * *Management Goal:* Stop the bleeding. Fix Cron UTC offsets and credential security.
* **Q2 (Apr-Jun):** **Error Visibility & Quality Gates (Waves 1-2)**
    * *Epics:* Dead-Letter Queue, Offline Observer, AWAP Pattern, Schema Compat.
    * *Management Goal:* Reliability. Ensure we aren't trading on "ghost data" or failed API runs.
* **Q3-Start (Jul):** **Idempotency (Wave 3)**
    * *Epic:* Idempotency (Atomic Writes).
    * *Management Goal:* Efficiency. Enable re-runs and partial recovery.

#### **Phase 2: Corpus Intelligence (Aug 2026 - Dec 2026)**
* **Q3 (Aug-Sep):** **Enrichment & Metrics (Waves 4-5)**
    * *Epics:* Scrapling (Category A), Scored Metrics (HDS/VPI/LFI), GMISR v2.0.
    * *Management Goal:* Competitive Edge. Move from "reading news" to "scoring math."
* **Q4 (Oct-Dec):** **ML Persistence (Wave 6)**
    * *Epics:* Parquet Snapshot Layer, Feature Store, HMM Regime Classifier.
    * *Management Gate:* **30-Trade Kelly Activation Gate.** (ML remains "Off" until this passes).

#### **Phase 3: Autonomous Orchestration (Jan 2027+)**
* **Q1 2027:** **Medallion Architecture (Wave 7)**
    * *Epics:* Unity Catalog Setup, Bronze/Silver/Gold DLT, Claude MCP Integration.
    * *Management Goal:* Scalability. Move from a "Home Lab" script to a production Cloud-Native pipeline.

---

### 3. Management "Risk & Priority" Dashboard Rules
To manage this in Jira, you should apply these **PgMP priorities** as colored flags or priority levels:

* **Priority 1: Critical Gates (Red)**
    * *DL-3 (FAILED.json)* and *ARCH-1 (Unity Catalog)*. These are "Showstoppers." If they don't land, the entire Phase is blocked.
* **Priority 2: Alpha Fidelity (Orange)**
    * *Scored Metrics* and *HMM Classifier*. These represent the "Value" of the program.
* **Priority 3: Operational Excellence (Green)**
    * *Docs* and *Governance*. These ensure the program survives a "Bus Factor" (if a lead developer leaves).

### Next Steps for Implementation:
1.  **Release Mapping:** Create Jira Releases named `Wave 0`, `Wave 1`, etc.
2.  **Epic Links:** Assign all stories from your **v8 Full Backlog CSV** to their respective Epics.
3.  **Start/Due Dates:** Use the timeline above to populate the "Start Date" and "Target Date" fields in Jira to generate the visual Roadmap.

--------------------------------------------------------

[DEXTER_MIDAS_Jira_Artifacts_20260331.xlsx]

The provided Jira artifacts represent a comprehensive program-level transition for the **DEXTER-MIDAS** project, shifting from a local script-based workflow to a hardened, enterprise-grade Medallion Architecture[cite: 5, 8, 17]. 

### **1. Structural Assessment: Backlog & Hierarchy**
[cite_start]The backlog effectively maps the technical "Waves" of the WBS into a Jira-native structure of Epics and Stories[cite: 1, 8].
* [cite_start]**Logical Phasing**: The program is correctly sequenced into three phases: **Hardened Foundations** (Waves 0-3), **Corpus Intelligence** (Waves 4-6), and **Autonomous Orchestration** (Wave 7)[cite: 8, 17].
* [cite_start]**Infrastructure Priority**: High-priority "Wave 0" items, such as fixing cron UTC offsets (`CRON-1`) and securing API credentials (`CRON-2`), address immediate operational risks[cite: 8, 11].
* [cite_start]**Audit Integration**: The inclusion of the **AWAP Pattern** (Audit-Write-Audit-Promote) as high-priority stories (e.g., `AW-2`, `AW-3`) ensures data integrity is baked into the development lifecycle[cite: 14].

### **2. Risk Management (PgMP Alignment)**
[cite_start]The synthesis applies Program Management Professional (PgMP) standards by linking technical deliverables to strategic risk mitigation[cite: 16].
* [cite_start]**Critical Gates**: The "Kelly Activation Gate" (`P4-3`) and the "30-trade gate" act as phase-gate controls, preventing the deployment of autonomous trading features before the model is statistically validated[cite: 7, 8, 17].
* [cite_start]**Data Integrity Controls**: The **Dead-Letter Queue** (`DL-1` to `DL-3`) and **Offline Observer** (`OO-1` to `OO-2`) are positioned as structural mitigations against silent API failures, which could otherwise poison the "Gold" decision layer[cite: 15, 16].
* [cite_start]**Architecture Continuity**: By annotating Wave 2/3/6 items with their Medallion layer equivalents (`SYNC-5`), the program avoids redundant engineering efforts during the Databricks migration[cite: 13, 16].

### **3. Operational Readiness & Governance**
[cite_start]The artifacts define a clear path toward a "Fully Agentic" system while maintaining human-in-the-loop governance[cite: 17, 18].
* [cite_start]**Medallion Maturity**: Wave 7 stories (e.g., `ARCH-1`, `BRNZ-1`) provide a concrete roadmap for migrating local flat-file outputs into a structured Unity Catalog on Databricks[cite: 13, 17].
* [cite_start]**Governance Sync**: Continuous stories like `GOV-1` and `GOV-3` mandate that thresholds and model reloads stay in sync with ETL version bumps, protecting the system from "calculation drift"[cite: 12, 16].
* [cite_start]**Monitoring**: The roadmap explicitly includes **Observability Maturity** (Wave 6b), adding SLA monitoring and skew detection to ensure the pipeline's health is visible to the "Assurance & Ops" team[cite: 5, 16].


### **Conclusion**
The synthesis is highly effective. It successfully decomposes a complex quantitative trading system into manageable, risk-weighted Jira components. [cite_start]The most critical next step is the completion of **Wave 0** and **Wave 1** to provide the visibility required to move into the more advanced **Intelligence** and **ML** phases[cite: 17, 18].



