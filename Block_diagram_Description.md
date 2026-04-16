# Block Diagram — Detailed Walkthrough

## Drug Infusion Control System Simulation (`Drug_Infusion_Control_Simulation.vi`)

---

## 1. Top-Level Structure

The entire simulation is enclosed within a single **While Loop** on the Block Diagram.
The While Loop's **conditional terminal** is wired to the `STOP SIMULATION` Boolean control on the Front Panel (stop-if-true mode).

---

## 2. Initialisation (Before While Loop)

| Node | Purpose |
|---|---|
| `Initial Volume` numeric control | Wired to the **left terminal** of the Remaining Volume shift register |
| Numeric constant `0.0` | Wired to the **left terminal** of the Elapsed Time shift register |

---

## 3. Inside the While Loop

### 3.1 Shift Registers

```
Left terminal (init) ──────► [Remaining Volume SR] ──────► right terminal
                                       │
                              (previous V_rem)
                                       ▼
                               Formula Node (V_rem input)
```

Two shift registers maintain state across iterations:
- **Remaining Volume SR** — carries `V_rem` from one iteration to the next
- **Elapsed Time SR** — carries `t_elapsed`; incremented by `dt` each cycle

### 3.2 Formula Node

```
Inputs:   V_rem, R (flow rate), dt (0.000277 hr), V0 (initial volume)
Outputs:  V_new, V_infused, T_rem

Code:
  V_new     = V_rem - (R * dt);
  V_infused = V0 - V_new;
  T_rem     = (V_new > 0.0) ? (V_new / R) : 0.0;
```

- `V_new` → wired back to right terminal of Remaining Volume SR AND to `Remaining Volume` indicator
- `V_infused` → wired to `Total Infused` indicator
- `T_rem` → wired to `Time Remaining` indicator

### 3.3 Elapsed Time Logic

```
Elapsed Time SR (out) ──► [Add] ◄── dt constant (0.000277)
                               │
                               ├──► right terminal of Elapsed Time SR
                               └──► Elapsed Time indicator
```

### 3.4 Waveform Chart

```
Flow Rate (Slide control) ──────────────────► [Waveform Chart]
```
The flow rate control is wired directly to the Waveform Chart input. LabVIEW appends one new point per loop iteration, building the Flow Rate vs. Time curve automatically.

### 3.5 Case Structure — Alert Logic

Three **Comparison** nodes evaluate alert conditions:

```
R > R_max  ──► [Greater?] ──► Case: TRUE  → OVERDOSE LED = TRUE
                                            → Alert String = "ALERT: Overdose Detected!"

R < R_min  ──► [Less?]    ──► Case: TRUE  → UNDERDOSE LED = TRUE
                                            → Alert String = "ALERT: Underdose — Rate Too Low."

V_new ≤ 0  ──► [LessEq?] ──► Case: TRUE  → COMPLETE LED = TRUE
                                            → Alert String = "Infusion Complete."
                                            → Stop Condition = TRUE (exits While Loop)
```

All three LEDs default to FALSE in the else case to auto-reset when the condition clears.

### 3.6 Timing

```
[Wait (ms)] ◄── 1000 (constant)
```

A `Wait (ms)` function with value `1000` ensures each simulation iteration takes 1 real second, making the simulation run at 1× real-time speed (1 simulated second = 1 real second). This can be reduced for fast-forward simulation.

---

## 4. Mathematical Model Summary

```
V(t) = V₀ − (R × t)

V₀   = initial drug volume        [ml]
R    = infusion flow rate          [ml/hr]
t    = elapsed time                [hr]
dt   = 1 second = 1/3600 hr ≈ 0.000277 hr

Derived quantities:
  Total Infused  = V₀ − V(t) = R × t
  Time Remaining = V(t) / R
```

---

## 5. Alert Condition Truth Table

| Condition | Boolean Expression | LED | Severity |
|---|---|---|---|
| Overdose | `R > R_max` | 🔴 Red | CRITICAL |
| Underdose | `R < R_min` | 🟡 Yellow | WARNING |
| Infusion Complete | `V(t) ≤ 0` | 🟢 Green | INFO |
| System Normal | None of above | All OFF | OK |

---

## 6. Sub-VI: Alert_SubVI.vi

Inputs: `R`, `R_max`, `R_min`, `V_rem`
Outputs: `overdose_flag`, `underdose_flag`, `complete_flag`, `alert_string`

This sub-VI encapsulates the Case Structure logic for reusability across multi-channel infusion extensions.

---

## 7. Sub-VI: Infusion_Math.vi

Inputs: `V_rem`, `R`, `dt`, `V0`
Outputs: `V_new`, `V_infused`, `T_rem`

Contains the Formula Node computation for portability.
