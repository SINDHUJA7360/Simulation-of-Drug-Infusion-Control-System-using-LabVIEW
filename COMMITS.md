# Commit History — drug-infusion-labview-simulation

> A realistic step-by-step development commit log for the project.

---

## Repository: `drug-infusion-labview-simulation`

```
commit a1f3c09  (HEAD -> main, origin/main)
Author: Sanjay Jonath S <sanjayjonath@srmist.edu.in>
Date:   Mon Oct 14 09:12:44 2024 +0530

    docs: upload final academic report and evaluation sheet

    - Added complete mini-project report (docx) to /docs
    - Formatted references and conclusion chapter
    - Updated evaluation sheet with submission date 07.11.2025
    - Minor grammar corrections in abstract and methodology

------------------------------------------------------------

commit 8d7b2c1
Author: Varshini M <varshinim@srmist.edu.in>
Date:   Fri Oct 11 18:45:02 2024 +0530

    feat: add sample output CSV files for all three test scenarios

    - data/test_run_baseline.csv: V0=250ml, R=50ml/hr, full 5hr run
    - data/test_run_overdose.csv: R=150ml/hr, overdose triggered at t=0
    - data/test_run_underdose.csv: R=5ml/hr, underdose triggered throughout
    - Each CSV contains columns: time_hr, V_rem, V_infused, T_rem, alert_state

------------------------------------------------------------

commit 4e9ad55
Author: Sindhuja <sindhuja@srmist.edu.in>
Date:   Thu Oct 10 14:30:17 2024 +0530

    fix: clamp remaining volume to zero to prevent negative values

    - Added conditional check in Formula Node: V_new = max(V_new, 0.0)
    - Prevents V_rem from going negative when simulation overshoots
    - Infusion Complete LED now triggers reliably at boundary condition
    - Time Remaining indicator no longer shows negative values at completion

------------------------------------------------------------

commit 2b8f1e6
Author: Sanjay Jonath S <sanjayjonath@srmist.edu.in>
Date:   Wed Oct 09 11:55:33 2024 +0530

    refactor: extract alert logic into reusable Alert_SubVI.vi

    - Created /src/Alert_SubVI.vi with inputs R, R_max, R_min, V_rem
    - Outputs: overdose_flag, underdose_flag, complete_flag, alert_string
    - Main VI now calls Alert_SubVI instead of inline Case Structure
    - Improves modularity for future multi-channel infusion support

------------------------------------------------------------

commit 7c3a0d4
Author: Varshini M <varshinim@srmist.edu.in>
Date:   Tue Oct 08 16:20:51 2024 +0530

    feat: add descriptive alert message string indicator to front panel

    - Alert Status string indicator added to Front Panel Zone 3
    - Three case strings: "ALERT: Overdose!", "ALERT: Underdose", "Infusion Complete"
    - Default (no alert) shows "System Normal — Infusion in Progress"
    - Font set to bold red for critical messages

------------------------------------------------------------

commit 9f1b7a8
Author: Sindhuja <sindhuja@srmist.edu.in>
Date:   Mon Oct 07 10:05:27 2024 +0530

    feat: implement all three safety alert LEDs and case structures

    - Overdose LED (Red): R > R_max comparison node → Case TRUE
    - Underdose LED (Yellow): R < R_min comparison node → Case TRUE
    - Infusion Complete LED (Green): V_rem <= 0 → Case TRUE, loop stop
    - All LEDs reset to FALSE in else case for auto-recovery

------------------------------------------------------------

commit 1a4c6f2
Author: Sanjay Jonath S <sanjayjonath@srmist.edu.in>
Date:   Fri Oct 04 15:40:09 2024 +0530

    feat: add time remaining and elapsed time indicators

    - Elapsed Time SR incremented by dt = 0.000277 hr per iteration
    - T_rem computed in Formula Node as V_rem / R
    - Two new numeric indicators wired and labelled on Front Panel
    - Formula Node updated with T_rem output variable

------------------------------------------------------------

commit 3d5e9c7
Author: Varshini M <varshinim@srmist.edu.in>
Date:   Thu Oct 03 13:15:00 2024 +0530

    feat: extract math into Infusion_Math sub-VI for reusability

    - Created /src/Infusion_Math.vi
    - Inputs: V_rem, R, dt, V0; Outputs: V_new, V_infused, T_rem
    - Contains Formula Node with full depletion model
    - Main VI updated to call sub-VI instead of inline formula node

------------------------------------------------------------

commit 6b2d3f1
Author: Sindhuja <sindhuja@srmist.edu.in>
Date:   Wed Oct 02 09:30:45 2024 +0530

    feat: implement Formula Node for volume depletion calculation

    - Formula Node added inside While Loop with 4 inputs, 3 outputs
    - V_new = V_rem - (R * dt)
    - V_infused = V0 - V_new
    - T_rem = (V_new > 0) ? (V_new / R) : 0
    - Outputs wired to respective numeric indicators

------------------------------------------------------------

commit 0e8b4c5
Author: Sanjay Jonath S <sanjayjonath@srmist.edu.in>
Date:   Tue Oct 01 16:50:33 2024 +0530

    feat: add waveform chart for flow rate vs time visualisation

    - Waveform Chart placed in Front Panel, Zone 2
    - Flow Rate slide control wired directly to chart input
    - Chart properties: X-axis "Time (hr)", Y-axis "Rate (ml/hr)"
    - AutoScale Y enabled; chart history set to 500 points

------------------------------------------------------------

commit 5c7f2d9
Author: Varshini M <varshinim@srmist.edu.in>
Date:   Mon Sep 30 11:25:18 2024 +0530

    feat: implement While Loop, shift registers, and Wait timing

    - While Loop added to Block Diagram
    - Two shift registers: Remaining Volume (init V0), Elapsed Time (init 0.0)
    - Wait (ms) constant set to 1000 ms per iteration (1 sec real-time)
    - STOP button wired to conditional terminal (stop-if-true)

------------------------------------------------------------

commit 8a9d1e3
Author: Sindhuja <sindhuja@srmist.edu.in>
Date:   Fri Sep 27 14:00:55 2024 +0530

    feat: build complete front panel UI with all controls and indicators

    - Zone 1 Controls: Initial Volume, Flow Rate slide, R_max, R_min
    - Zone 2 Indicators: Remaining Volume, Total Infused, Time Remaining, Elapsed Time
    - Zone 3 Alerts: OVERDOSE (Red LED), UNDERDOSE (Yellow LED), COMPLETE (Green LED)
    - STOP SIMULATION button added
    - Panel styled with colour zones and bold labels

------------------------------------------------------------

commit 2f4a6b8
Author: Sanjay Jonath S <sanjayjonath@srmist.edu.in>
Date:   Wed Sep 25 10:10:22 2024 +0530

    chore: add project folder structure and placeholder source files

    - Created /src, /docs, /assets, /data directories
    - Added placeholder Drug_Infusion_Control_Simulation.vi (empty VI)
    - Added .gitignore with LabVIEW-specific rules
    - Added MIT LICENSE file

------------------------------------------------------------

commit d1b0e4f
Author: Sanjay Jonath S <sanjayjonath@srmist.edu.in>
Date:   Mon Sep 23 08:45:00 2024 +0530

    Initial commit: project setup and README

    - Repository initialised for Drug Infusion Control System Simulation
    - README.md with project overview, requirements, features list
    - Team information and course details added
    - Base project structure defined
```
