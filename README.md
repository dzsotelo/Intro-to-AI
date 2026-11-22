# Milestone 3 – Group 8: AI-Assisted SPC Review for GC2

**Project:** A two-step, human-in-the-loop Statistical Process Control (SPC) prototype for GC2 lab-standard runs. Strict mathematical rules in Power BI screen every run for out-of-control (OOC) points and six-point trends. Normal runs stop quietly with zero alerts; only flagged events trigger Power Automate to bundle environmental context (`DailyLogs.xlsx`) for a generative-AI *advisory* review. A technician inspects the charts, and the lab manager makes every final decision.

All data is **synthetic**. No real lab, company, or customer data is used.

---

## How the Prototype Works

1. **Automated Rule Checking (Power BI):** Raw GC2 test results and reference Excel targets are loaded into Power BI, which builds the control charts and checks every reading against fixed math rules for **OOC points** (sudden jumps outside safe limits) or **trend violations** (six consecutive points drifting to one side of the mean). Passing runs stop immediately without triggering alerts or AI calls.
2. **Data Bundling (Power Automate):** When a violation is flagged and selected for review, Power Automate pulls that day's room temperature and humidity notes from `DailyLogs.xlsx` and packages them with the flagged run and 10-day baseline history.
3. **Advisory AI Diagnosis & Human Sign-Off:** Acting strictly as an assistant, the AI compares the flagged run against baseline history and room logs to suggest likely causes (e.g., low calibration gas or temperature spikes) and outputs a plain-English summary to `AI_Review_Log.xlsx` for technician inspection and manager approval.

---

## Repository Files & Milestone 3 Usage

| File / Folder | Milestone Role | How It Was Used in Milestone 3 |
|---|---|---|
| `PowerBI/` (`.pbix` / `.pbip`) | Core Prototype | Implemented SPC control charts, OOC/trend rule logic, data-quality filtering, and the user-triggered review action |
| `PowerAutomate/` | Core Prototype | Orchestrated the conditional flow bundling flagged Power BI events with `DailyLogs.xlsx` for AI review |
| `01_Representative_Input_Data.md` | Input Spec | Documents input schemas, control limits, and planted test scenarios |
| `data/Raw Data/` (90 runs, 6 scenario folders) | Input Data | Primary synthetic GC2 dataset loaded into Power BI (Get Data → Folder) |
| `data/Instrument Component List.xlsx`, `data/Lab Standard.xlsx` | Reference Data | Peak mapping and fixed control limits for rule-based detection |
| `data/DailyLogs.xlsx` | Context Data | Room temperature and humidity notes appended to the AI payload for flagged events only |
| `data/csv/` | Reference Copies | CSV copies of datasets and answer keys viewable directly on GitHub |
| `02_Expected_Results.md`, `data/Expected_Results_Reference.xlsx` | Validation Key | Ground-truth flags for all 450 readings and 3 AI events used to verify Power BI accuracy (**never sent to the AI**) |
| `05_Detection_Rules.md` | Rule Logic | Exact mathematical definitions implemented in Power BI |
| `06_AI_Review_Prompt_and_Reference_Outputs.md` | AI Prompting | Structured AI input payload, system prompt, and acceptable/unacceptable scoring benchmarks |
| `03_Testing_Plan.md` & `data/AI_Review_Log.xlsx` | Testing & Logging | Executed all 6 test cases (straightforward, challenging, invalid/missing data), logged PASS/FAIL results, and scored AI outputs |
| `04_Finalized_Prototype_Scope.md`, `01_AI_Capacity_Governance.md`, `01_KPIs.md` | Governance & Scope | Enforced prototype boundaries, advisory-only AI guardrails, and evaluation metrics |
| `00_Revision_Notes.md`, `07_Toolchain_Access_Check.md` | Documentation | Milestone 2 feedback responses and verified Microsoft/Gemini toolchain configuration |
| `scripts/` | Optional Utilities | Scripts used to generate synthetic data and verify answer keys |

---

## Scope Refinements Implemented in Milestone 3
- **Single-Instrument Focus:** Scoped to **one GC2 instrument, one SPC workflow, and one conditional AI-review path**.
- **User-Triggered Execution:** AI review is triggered on demand via a **"Review selected event"** action in Power BI rather than unattended background polling.
- **Data-Quality Isolation:** Invalid or missing data triggers a separate **data-quality flag** and is blocked from reaching the AI.
- **Advisory-Only Output:** Every AI summary includes observations, uncertainty caveats, and suggested checks, saved to `AI_Review_Log.xlsx` for mandatory human sign-off.
- **Toolchain Fallback:** Supports direct Power Automate → AI Builder execution with a verified fallback path (Power BI → Gemini → `AI_Review_Log.xlsx`) where AI Builder licensing is restricted.

---

## Soft Launch, Current Limitations & Next Steps

### Soft Launch & Validation Plan
To validate accuracy and reliability before full production deployment, the prototype is designed for a **2–4 week soft launch in shadow mode** alongside normal lab operations. During shadow mode, the pipeline processes incoming GC2 runs and generates AI summaries in the background without affecting live batch decisions. Lab analysts review the exact same runs manually so the team can benchmark alert precision, catch false alarms, and confirm that Power BI, Power Automate, and the AI layer remain stable under daily lab workloads.

### Current Prototype Limitations & Human Review
Even with Milestone 3 features in place, human oversight remains mandatory in four areas:
- **Checking Physical Equipment:** Although the AI reads daily room logs, it cannot inspect hardware. A technician must physically check the GC2 instrument for gas leaks, column wear, or unlogged manual adjustments before intervening.
- **Handling Rare or Compound Anomalies:** When multiple alarms trigger simultaneously or an unfamiliar drift pattern appears, the AI can oversimplify the cause, requiring senior analyst judgment.
- **Spotting Upstream Data Artifacts:** While basic invalid-data flags are filtered out, corrupted exports, baseline integration glitches, or mislabeled samples still require human chromatogram review to avoid false alarms.
- **Final Quality & Compliance Sign-Off:** The AI is strictly an assistant—official QA approvals, compliance records, and batch-hold decisions remain solely under human authority.

### Next Steps (Post-Milestone 3 Roadmap)
If development continues beyond Milestone 3, priority upgrades include:
1. **Self-Adjusting Alerts:** Using analyst feedback logged in `AI_Review_Log.xlsx` to automatically refine prompt examples and tune alert thresholds over time.
2. **Early-Warning Drift Forecasting:** Moving beyond reactive rule violations to flag subtle baseline drift before a run crosses critical control limits.
3. **Direct LIMS Integration:** Automatically pushing manager-approved AI summaries from the review log into the Laboratory Information Management System (LIMS) to eliminate manual compliance data entry.
