# 💉 Drug Infusion Control System Simulation
### *with Safety Alerts — Built in LabVIEW*

> A software-only simulation of an intravenous (IV) drug infusion pump, implementing real-time volume tracking, flow-rate visualisation, and a three-level safety alert system — developed entirely in National Instruments LabVIEW.

---

##  Project Overview

This project simulates the behaviour of a clinical IV infusion pump using LabVIEW's graphical programming environment. The system models drug volume depletion over time, provides real-time graphical feedback, and triggers safety alerts under unsafe operating conditions — all without any physical hardware.

**Mathematical Core:**
```
V(t) = V₀ − (R × t)
```
Where:
- `V(t)` = Remaining drug volume at time `t` (ml)
- `V₀`   = Initial drug volume (ml)
- `R`    = Infusion flow rate (ml/hr)
- `t`    = Elapsed simulation time (hr)

---

##  Features

- ✅ **Real-time Volume Tracking** — Remaining volume updated every simulated second
- ✅ **Flow Rate vs. Time Graph** — Live Waveform Chart in LabVIEW
- ✅ **Total Drug Infused Calculator** — Running cumulative volume delivered
- ✅ **Time-to-Completion Predictor** — Dynamic estimate based on current rate
- ✅ **Safety Alert System:**
  - 🔴 **OVERDOSE** — Flow rate exceeds maximum safe limit (R > R_max)
  - 🟡 **UNDERDOSE** — Flow rate falls below minimum safe limit (R < R_min)
  - 🟢 **INFUSION COMPLETE** — Drug volume reaches zero
- ✅ **Adjustable Parameters** — Volume, rate, and thresholds configurable at runtime

---

##  Repository Structure

```
drug-infusion-labview-simulation/
│
├── src/                          # LabVIEW source files
│   ├── Drug_Infusion_Control_Simulation.vi   # Main VI (Front Panel + Block Diagram)
│   ├── Alert_SubVI.vi            # Safety alert sub-VI (reusable)
│   └── Infusion_Math.vi          # Formula node sub-VI for calculations
│
├── docs/                         # Documentation
│   ├── Drug_Infusion_LabVIEW_Report.docx    # Full academic report
│   └── Block_Diagram_Description.md         # Detailed block diagram walkthrough
│
├── assets/                       # Visual assets
│   ├── front_panel_screenshot.png
│   ├── block_diagram_screenshot.png
│   ├── system_architecture.png
│   └── alert_demo.png
│
├── data/                         # Sample output data
│   ├── test_run_baseline.csv     # V₀=250ml, R=50ml/hr output log
│   ├── test_run_overdose.csv     # Overdose scenario test data
│   └── test_run_underdose.csv    # Underdose scenario test data
│
├── README.md                     # This file
├── .gitignore                    # LabVIEW-specific ignore rules
└── LICENSE                       # MIT License
```

---

##  Requirements

| Requirement | Details |
|---|---|
| **LabVIEW Version** | LabVIEW 2020 or later (Community Edition supported) |
| **Operating System** | Windows 10/11, macOS 11+, or Linux (RHEL/CentOS) |
| **Hardware** | No hardware required — pure software simulation |
| **Additional Toolkits** | None required (base LabVIEW installation sufficient) |

---

##  Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/drug-infusion-labview-simulation.git
cd drug-infusion-labview-simulation
```

### 2. Open the Main VI
- Launch LabVIEW
- Go to **File → Open**
- Navigate to `/src/Drug_Infusion_Control_Simulation.vi`
- The Front Panel will open automatically

### 3. Configure Parameters
In the Front Panel, set:
- **Initial Volume (ml):** e.g., 250
- **Flow Rate (ml/hr):** e.g., 50 (adjustable via slider during run)
- **Max Safe Rate:** e.g., 100
- **Min Safe Rate:** e.g., 10

### 4. Run the Simulation
- Click the **Run** button (▶) or press **Ctrl+R**
- Observe the Waveform Chart, digital indicators, and LED alerts
- Adjust the Flow Rate slider in real time to test alert conditions
- Click **STOP SIMULATION** to end the run

---

##  Sample Output

### Baseline Test (V₀ = 250 ml, R = 50 ml/hr)

| Time (hr) | Remaining Volume (ml) | Total Infused (ml) | Time Remaining (hr) |
|---|---|---|---|
| 0.0 | 250.0 | 0.0 | 5.00 |
| 1.0 | 200.0 | 50.0 | 4.00 |
| 2.5 | 125.0 | 125.0 | 2.50 |
| 4.0 | 50.0 | 200.0 | 1.00 |
| 5.0 | 0.0 | 250.0 | 0.00 ✅ COMPLETE |

### Alert Conditions

| Condition | Trigger | LED | Message |
|---|---|---|---|
| Overdose | R = 150 ml/hr > 100 | 🔴 Red ON | "ALERT: Overdose Detected! Reduce Rate." |
| Underdose | R = 5 ml/hr < 10 | 🟡 Yellow ON | "ALERT: Underdose — Rate Too Low." |
| Infusion Complete | V(t) = 0 | 🟢 Green ON | "Infusion Complete. Remove Line." |

---

## 📐 System Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    FRONT PANEL (UI)                     │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────┐   │
│  │  CONTROLS   │  │  INDICATORS  │  │ SAFETY ALERTS │   │
│  │ ─ Volume    │  │ ─ Remain.Vol │  │ 🔴 Overdose  │    │
│  │ ─ Flow Rate │  │ ─ Infused    │  │ 🟡 Underdose │    │
│  │ ─ R_max     │  │ ─ Time Rem.  │  │ 🟢 Complete  |    |
│  │ ─ R_min     │  │ ─ Graph      │  └───────────────┘   │
│  └─────────────┘  └──────────────┘                      │
└────────────────────────┬────────────────────────────────┘
                         │ dataflow
┌────────────────────────▼────────────────────────────────┐
│                BLOCK DIAGRAM (LOGIC)                    │
│                                                         │
│  ┌──────────────────────────────────────────────────┐   │
│  │               WHILE LOOP                         │   │
│  │  Shift Reg: V_rem, t_elapsed                     │   │
│  │  ┌─────────────────────┐                         │   │
│  │  │    FORMULA NODE     │  V_new = V_rem−R×dt     │   │
│  │  │    V(t) = V₀−R×t   │  T_rem = V_new / R       │   │
│  │  └─────────────────────┘                         │   │
│  │  ┌─────────────────────┐                         │   │
│  │  │   CASE STRUCTURE    │  Overdose / Underdose   │   │
│  │  │   (Alert Logic)     │  / Complete checks      │   │
│  │  └─────────────────────┘                         │   │
│  │  Waveform Chart Write → Flow Rate vs Time        │   │
│  │  Wait (ms) → 1000 ms per iteration               │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

## 📈 Commit History

See [`COMMITS.md`](docs/COMMITS.md) for the full step-by-step development commit log.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

