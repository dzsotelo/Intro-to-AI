# Milestone 3 – Group 8: AI-Assisted SPC Review for GC2

**Project:** A two-step, human-in-the-loop Statistical Process Control (SPC) prototype for GC2 lab-standard runs. Strict mathematical rules in Power BI screen every run for out-of-control (OOC) points and six-point trends. Normal runs stop quietly with zero alerts; only flagged events trigger Power Automate to bundle environmental context (`DailyLogs.xlsx`) for a generative-AI *advisory* review. A technician inspects the charts, and the lab manager makes every final decision.

All data is **synthetic**. No real lab, company, or customer data is used.

---

## How the Prototype Works

1. **Automated Rule Checking (Power BI):** Raw GC2 test results and reference Excel targets are loaded into Power BI, which builds the control charts and checks every reading against fixed math rules for **OOC points** (sudden jumps outside safe limits) or **trend violations** (six consecutive points drifting to one side of the mean). Passing runs stop immediately without triggering alerts or AI calls.
2. **Data Bundling (Power Automate):** When a violation is flagged and selected for review, Power Automate pulls that day's room temperature and humidity notes from `DailyLogs.xlsx` and packages them with the flagged run and 10-day baseline history.
3. **Advisory AI Diagnosis & Human Sign-Off:** Acting strictly as an assistant, the AI compares the flagged run against baseline history and room logs to suggest likely causes (e.g., low calibration gas or temperature spikes) and outputs a plain-English summary to `AI_Review_Log.xlsx` for technician inspection and manager approval.

---

## Files and how they will be used

| File | Assignment part | How it is used in Milestone 3 |
|---|---|---|
| `01_Representative_Input_Data.md` | 1 – Input data | Describes every data file, its columns, the control limits, and what is planted in each test scenario |
| `data/Raw Data/` (90 runs, 6 scenario folders) | 1 – Input data | Primary input loaded into Power BI (Get Data → Folder) |
| `data/Instrument Component List.xlsx`, `data/Lab Standard.xlsx` | 1 – Input data | Peak mapping and fixed control limits for the detection rules |
| `data/DailyLogs.xlsx` | 1 – Input data | Environmental context added to the AI payload for flagged events only |
| `data/csv/` | 1 & 2 | CSV copies of the data and answer key, viewable directly on GitHub |
| `02_Expected_Results.md`, `data/Expected_Results_Reference.xlsx` | 2 – Expected results | Answer key: expected flags for all 450 readings and the 3 AI events. Power BI output is checked against it. **Never sent to the AI.** |
| `05_Detection_Rules.md` | 2 – Expected results | Exact rule definitions behind the answer key |
| `06_AI_Review_Prompt_and_Reference_Outputs.md` | 2 – Expected results | AI input payload, prompt, and reference "acceptable / unacceptable" AI responses |
| `03_Testing_Plan.md` | 3 – Testing plan | 6 test cases (straightforward, challenging, missing info / expected failure), how incorrect results are recognized, AI scoring checklist |
| `data/AI_Review_Log.xlsx` | 3 – Testing plan | Review log, test-results (auto PASS/FAIL), and AI scoring sheets |
| `04_Finalized_Prototype_Scope.md` | 4 – Scope | What Milestone 3 will build |
| `01_AI_Capacity_Governance.md`, `01_KPIs.md` | Supporting | What the AI may and may not do; KPIs |
| `07_Toolchain_Access_Check.md` | Supporting | Confirms which Microsoft/Gemini tools the team can actually use |
| `scripts/` | Optional | Regenerates the synthetic data and verifies the answer key (no code was required for this milestone) |

---

## Soft Launch, Current Limitations & Next Steps

### Soft Launch & Validation Plan
Before full deployment, the prototype will run in **shadow mode for 2–4 weeks** alongside normal lab operations. The pipeline processes incoming GC2 runs and generates AI summaries in the background without affecting live decisions, while analysts review the same runs manually to benchmark alert accuracy, catch false alarms, and verify system reliability under daily workloads.

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
