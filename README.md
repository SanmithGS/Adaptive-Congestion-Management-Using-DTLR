# ⚡ Adaptive Congestion Management Using DTLR, Smart EV Load Shedding & Voltage Support for System Integrity Protection

A simulation study of coordinated congestion management in a modern power system with high renewable energy and electric vehicle (EV) penetration, implemented on the **IEEE 14-Bus System** using **DIgSILENT PowerFactory** and **Python**.

Five progressive study cases evaluate the transition from static thermal rating to a fully adaptive, weather-aware protection framework — benchmarked against the standard voltage limits of **0.95–1.05 p.u.**

> 📄 Results written up as a technical report in IEEE format
> **Course Project | Minor Project (EE16096) | NIT Warangal | 2026**

---

## 🛠️ Tools & Environment

| Tool | Version | Purpose |
|------|---------|---------|
| DIgSILENT PowerFactory | 2023 | Network modelling, N-1 contingency, RMS simulation |
| Python | 3.x | DTLR computation, EV shedding logic, data processing |
| NumPy | — | Numerical computation |
| Pandas | — | CSV data handling |
| Matplotlib | — | Result visualization |

---

## 🏗️ System Architecture

| Component | Description |
|-----------|-------------|
| Test System | IEEE 14-Bus Transmission System |
| Renewable Generation | Solar PV (50 MW) at Bus 6; Wind (35 MW) at Bus 9 |
| EV Load | 70 MW controllable load at Bus 14 (Block 1: 15 MW, Block 2: 20 MW, Block 3: 35 MW) |
| Critical Line | Line 0009–0014 (highest post-contingency loading) |
| Critical Bus | Bus 8 (highest voltage deviation) |
| EV/Voltage Bus | Bus 14 (EV concentration + undervoltage risk) |
| Contingency | N-1 outage of Line 06–13 applied at t = 5 s |
| Simulation Duration | 0 to 1200 seconds (RMS solver) |
| Voltage Limits | 0.95 – 1.05 p.u. |
| DTLR Standard | IEEE Std 738-2012 (heat balance equation) |

---

## 📁 Project Structure

```
Minor_Project/
│
├── Case_1_IEEE14/                    # Base case — standard IEEE 14-bus load flow
│   ├── syst_dia.png                  # PowerFactory network diagram
│   └── op.png                        # Load flow output window
│
├── Case_2_RE/                        # System with RES + EV integration
│   ├── Syst_dia.png                  # Modified network with solar, wind, EV
│   └── op.png                        # Load flow output showing stress
│
├── Case_3_Static/                    # N-1 contingency with static rating
│   ├── Syst_dia_1.png                # Network with Line 06-13 outage
│   ├── Syst_dia_2.png
│   └── op.png                        # Line loading vs static rating graph
│
├── Case_4_DTLR/                      # N-1 with IEEE-738 DTLR + alarm mechanism
│   ├── IEEE738_Summer.png            # DTLR vs Loading — Summer
│   ├── IEEE738_Monsoon.png           # DTLR vs Loading — Monsoon
│   ├── IEEE738_Winter.png            # DTLR vs Loading — Winter
│   ├── BusVoltage.png                # Voltage profile of all 14 buses
│   ├── CriticalBusVoltage.png        # Dynamic voltage at Bus 8 over time
│   ├── RenewableProfiles.png         # Solar and wind generation profiles
│   ├── DTLR_results.csv              # Computed DTLR values per time step
│   ├── DTLR_Alarm_Code_updated_IEEE738_with_Voltage_V2.txt   # Python control logic
│   ├── System_Dia_01.png
│   ├── System_Dia_02.png
│   ├── op1.png
│   ├── op_summer.png                 # Console output — Summer alarms
│   ├── op_monsoon.png                # Console output — Monsoon (SAFE)
│   └── op_winter.png                 # Console output — Winter (mixed)
│
├── Case_5_SLS+STAT/                  # Full SIP: DTLR + EV Shedding + STATCOM
│   ├── CASE5_SUMMARY_Summer.png      # 6-panel result — Summer
│   ├── CASE5_SUMMARY_Monsoon.png     # 6-panel result — Monsoon
│   ├── CASE5_SUMMARY_Winter.png      # 6-panel result — Winter
│   ├── DTLR_results.csv
│   ├── Syst_dia.png
│   ├── code.txt                      # Python SIP control logic
│   ├── op_summer.png
│   ├── op_monsoon.png
│   └── op_winter.png
│
└── Documennts/
│   ├── Minor Project Report.pdf      # Project Report
│   └── San Minor Project Presentation.pptx        # Project Presentation
│
└── Extras/
    ├── Project Overview.png          # High-level framework summary
    └── Study_case_manager.png        # PowerFactory study case structure
```

---

## ▶️ How to Run

### DIgSILENT PowerFactory Simulation

1. Open **DIgSILENT PowerFactory 2023** (or compatible version)
2. Import the project network with the 5 configured study cases
3. Run **load flow** for Cases 1 and 2 (steady-state)
4. Apply the **N-1 contingency** (trip Line 06–13 at t = 5 s) for Cases 3–5
5. Execute **RMS simulation** (0–1200 s) and export results as CSV

### Python Post-Processing

```bash
pip install numpy pandas matplotlib
```

Open the Python script from `Case_4_DTLR/` or `Case_5_SLS+STAT/` and run:

```bash
python DTLR_Alarm_Code_updated_IEEE738_with_Voltage_V2.txt
```

The script will:
- Read exported CSV from PowerFactory
- Compute IEEE-738 DTLR for Summer / Winter / Monsoon profiles
- Detect overload conditions (Loading > DTLR)
- Apply stepwise EV load shedding
- Simulate STATCOM reactive injection
- Generate all result plots automatically

> ⚠️ No additional toolboxes required beyond the packages listed above.

---

## 📊 Key Simulation Results

### Seasonal DTLR Comparison — Post N-1 Contingency

| Season | Line Loading (%) | DTLR Range (%) | Status | EV Shedding Required |
|--------|-----------------|----------------|--------|----------------------|
| Summer | 122.73 | 98.88 – 112.72 | ⚠️ OVERLOAD (most critical) | ✅ Yes — Blocks 1 & 2 |
| Winter | 122.73 | 107.25 – 130.16 | ⚠️ Partial overload | ✅ Yes — Block 1 only |
| Monsoon | 122.73 | 129.64 – 138.94 | ✅ SAFE throughout | ❌ Not required |

Static rating limit: **100%** — exceeded in all seasons. DTLR reveals the true thermal margin.

---

### Case 5 — EV Load Shedding Actions (Summer)

| Time (s) | Loading (%) | DTLR (%) | Shedding Action | New Loading (%) |
|----------|-------------|----------|-----------------|-----------------|
| 0 | 93.92 | 98.50 | None (Safe) | 93.92 |
| 300 | 117.51 | 98.75 | Block 1 shed (15 MW) | 97.38 |
| 600 | 117.51 | 94.40 | Block 1 + Block 2 (35 MW) | 70.55 |
| 900 | 117.51 | 102.60 | Block 1 shed (15 MW) | 97.38 |
| 1200 | 117.51 | 106.25 | Block 1 shed (15 MW) | 97.38 |

---

### Voltage Observations (Case 4 — Post Contingency)

| Bus | Voltage (p.u.) | Status |
|-----|----------------|--------|
| Most buses (1–13) | 0.95 – 1.05 | ✅ Within limits |
| Bus 14 (EV Bus) | ~0.913 | ❌ Undervoltage |
| Bus 8 (Gen-rich) | ~1.095–1.10 | ⚠️ Overvoltage |

**STATCOM at Bus 14 (Case 5):** Continuously injects reactive power → voltage restored to **0.95–1.05 p.u.** across all seasonal scenarios.

---

### Comparative Case Summary

| Case | Condition | Congestion | Voltage | Control Action |
|------|-----------|------------|---------|----------------|
| Case 1 | Normal load flow | None | Stable | None |
| Case 2 | RES + EV integration | Moderate | Slight deviation | None |
| Case 3 | N-1 + Static Rating | **Severe** (>100%) | Unstable | None |
| Case 4 | N-1 + DTLR + Alarm | Reduced (season-dependent) | Violations at Bus 14 & 8 | Alarm only |
| **Case 5** | **DTLR + EV Shedding + STATCOM** | **Eliminated** | **Stable (0.95–1.05 pu)** | **Full SIP** |

---

## 🔑 DTLR — IEEE 738-2012 Heat Balance Equation

The permissible line current is governed by the thermal equilibrium:

```
I² × R = Qc + Qr − Qs
```

Solving for the dynamic current rating:

```
I_DTLR = √[(Qc + Qr − Qs) / R]
I_DTLR = f(Ta, Vw, Gs)
```

| Parameter | Description |
|-----------|-------------|
| `Qc` | Convective cooling — proportional to wind speed and (Tc − Ta) |
| `Qr` | Radiative cooling — ε × σ × (Tc⁴ − Ta⁴) |
| `Qs` | Solar heat gain — α × Gs |
| `Ta` | Ambient temperature |
| `Vw` | Wind speed |
| `Gs` | Solar irradiance |

---

## 🌦️ Seasonal Weather Profiles (Karnataka — IMD Data)

**Summer**

| Time (s) | Temp (°C) | Wind (m/s) | Solar (W/m²) |
|----------|-----------|------------|--------------|
| 0 | 32 | 2.0 | 900 |
| 300 | 34 | 2.5 | 950 |
| 600 | 36 | 2.0 | 920 |
| 900 | 33 | 3.0 | 880 |
| 1200 | 32 | 3.5 | 850 |

**Winter**

| Time (s) | Temp (°C) | Wind (m/s) | Solar (W/m²) |
|----------|-----------|------------|--------------|
| 0 | 20 | 1.2 | 600 |
| 300 | 22 | 1.5 | 650 |
| 600 | 24 | 2.0 | 700 |
| 900 | 21 | 2.2 | 620 |
| 1200 | 19 | 2.5 | 580 |

**Monsoon**

| Time (s) | Temp (°C) | Wind (m/s) | Solar (W/m²) |
|----------|-----------|------------|--------------|
| 0 | 26 | 4.0 | 400 |
| 300 | 27 | 4.5 | 350 |
| 600 | 28 | 5.0 | 300 |
| 900 | 26 | 5.5 | 280 |
| 1200 | 25 | 6.0 | 250 |

---

## 🔑 Key Concepts Demonstrated

- Forward power flow analysis using Newton-Raphson load flow (DIgSILENT PowerFactory)
- N-1 contingency simulation and post-fault power redistribution
- IEEE Std 738-2012 heat balance model for Dynamic Thermal Line Rating
- Seasonal impact of weather on transmission line ampacity (Summer < Winter < Monsoon)
- Stepwise EV load shedding as a demand-side congestion management strategy
- STATCOM reactive power compensation for undervoltage correction
- System Integrity Protection (SIP) — coordinated thermal + voltage control
- Voltage violation detection and reactive power imbalance analysis
- Hybrid simulation: PowerFactory (network) + Python (control logic + visualization)

---

## 📄 Key Findings

1. **DTLR increases line utilization** — Monsoon DTLR reaches **138.94%** vs 100% static: **38% more usable capacity**
2. **EV shedding is fast and effective** — Block 1 (15 MW) reduces loading from **117.51% → 97.38%** in a single step during summer contingency
3. **Season is decisive** — Summer DTLR falls to 98–105% making shedding mandatory; Monsoon DTLR alone is sufficient
4. **Reactive imbalance requires coordinated control** — Bus 14 undervoltage (0.913 pu) and Bus 8 overvoltage (1.10 pu) co-exist post-contingency
5. **Integrated framework eliminates congestion** — DTLR + EV Shedding + STATCOM maintains 0.95–1.05 pu across all seasonal scenarios

---

## 🔭 Future Scope

1. **Real-time DTLR** — Live IMD weather API integration for continuous dynamic rating updates
2. **AI-based EV shedding** — ML model to predict congestion and pre-schedule EV dispatch decisions
3. **Adaptive STATCOM control** — Closed-loop Q-V droop with automatic gain tuning for reactive regulation
4. **Digital protection relay integration** — DTLR-integrated protection relays for hardware deployment
5. **DTLR-assisted asset management** — Maintenance scheduling based on cumulative thermal stress

---

## 📚 References

1. Y. Cong, P. Regulski, P. Wall, M. Osborne, and V. Terzija, "On the Use of Dynamic Thermal-Line Ratings for Improving Operational Tripping Schemes," *IEEE Transactions on Power Delivery*, vol. 31, no. 4, pp. 1891–1900, Aug. 2016.
2. IEEE Std 738-2012: *Standard for Calculating the Current-Temperature Relationship of Bare Overhead Conductors*, IEEE, 2013.
3. P. S. Georgilakis, "Dynamic thermal rating of power lines: A review," *Electric Power Systems Research*, vol. 75, no. 2–3, pp. 74–83, 2005.
4. T. Seppa, "Accurate ampacity determination: Temperature-sag model for operational real time ratings," *IEEE Transactions on Power Delivery*, vol. 10, no. 3, pp. 1460–1470, Jul. 1995.
5. M. Begovic, V. Madani, and D. Novosel, "System integrity protection schemes," *IREP Symposium on Bulk Power System Dynamics and Control*, 2007.
6. DIgSILENT GmbH, *PowerFactory User Manual*, DIgSILENT, Germany, 2023.

---

## 👤 Author

**Sanmith G S**

🎓 M.Tech – Smart Electric Grid, NIT Warangal
🎓 B.E – Electrical & Electronics Engineering, BMS College of Engineering, Bengaluru
🔗 [LinkedIn](https://www.linkedin.com/in/sanmith-g-s)
🐙 [GitHub](https://github.com/sanmithgs)
📧 sannysanmith@gmail.com
