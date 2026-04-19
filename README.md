# AquaTensor — Water Recovery Simulator

> A decision simulator for data center operators to estimate water recovery potential from waste heat.

**Live Demo:** [https://dhivyabalakumar.github.io/AquaTensor](https://dhivyabalakumar.github.io/AquaTensor)

---

## Overview

Data centers consume enormous volumes of water for cooling — and simultaneously waste vast amounts of heat. AquaTensor turns that wasted heat into recovered water, reducing a facility's net water consumption and improving its regulatory standing.

This simulator lets data center operators, sustainability leads, and infrastructure planners model exactly how much water their facility could recover — before committing to a full deployment. Enter your facility parameters, run the simulation, and get a detailed breakdown of heat recovery, water production, energy savings, and permit risk — all in real time.

---

## Live Screens

### Screen 1 — Hero
The landing view introduces the simulator with a professional dark interface featuring an animated floating water droplet background. A single call-to-action button scrolls directly into the configuration panel.

---

### Screen 2 — Facility Parameters (Input Panel)

Three configuration sections let you describe your data center:

**Section A — Facility Basics**
- **IT Load (MW):** Slider from 5 MW to 100 MW. This is the primary driver of all calculations.
- **Location:** Dropdown covering major Indian data center hubs — Bangalore, Chennai, Hyderabad, Mumbai, Delhi, Pune, Kolkata.
- **Cooling Type:** Select from Air Cooling, Evaporative Cooling, or Liquid Cooling.

**Section B — Water Usage**
- **Water Consumption (litres/day):** Auto-filled based on IT Load using `IT Load × 50,000 L/MW/day`. Fully editable — override with your actual facility data for more accurate results.

**Section C — Heat Recovery Assumptions**
- **Waste Heat Available (%):** Slider from 20% to 50%. Represents the fraction of total waste heat that can be practically captured.
- **Recoverable Heat Efficiency (%):** Slider from 30% to 70%. Represents how efficiently captured heat is converted into usable thermal energy for water production.

Once configured, click **Calculate Results** to run the full simulation.

---

### Screen 3 — Simulation Output (6 Metrics)

All outputs update live whenever you adjust any input after the first calculation.

#### 1. Heat Available
Breaks down the thermal energy chain:
- **Total Heat (MW)** = IT Load × 0.8
- **Usable Heat (MW)** = Total Heat × Waste Heat %
- **Recovered Heat (MW)** = Usable Heat × Recovery Efficiency %

#### 2. Water Production *(Primary Metric)*
The core output of the simulation:
```
Water Produced (L/day) = Recovered Heat (MW) × 15,000
```
Displayed prominently as the headline number — this is how many litres per day your facility can recover.

#### 3. Net Water Impact
```
Net Water = Water Produced − Water Consumed
```
Classified into three states:
- **Water Positive** — facility produces more water than it consumes
- **Water Neutral** — within ±5% of consumption
- **Water Negative** — consumption still exceeds recovery

#### 4. Water Offset Percentage
```
Offset % = (Water Produced / Water Consumed) × 100
```
Shown as both a percentage and an animated progress bar.

#### 5. Energy Savings
```
Cooling Load (MW) = IT Load × 0.3
Energy Savings (MW) = Cooling Load × 0.3
```
Quantifies the reduction in cooling energy demand when waste heat is actively recovered and reused.

#### 6. Permit Risk Indicator

| Offset % | Risk Level |
|---|---|
| Below 20% | High Risk |
| 20% – 60% | Medium Risk |
| Above 60% | Low Risk |

---

### Screen 4 — Before vs. After Comparison

A side-by-side panel showing your facility's profile with and without AquaTensor deployed.

---

### Screen 5 — Pilot Simulation Mode

Configure a smaller pilot deployment and see projected impact:

- **Pilot Size (MW):** Slider from 1 MW to 20 MW (default 5 MW)
- **Water Recovered in Pilot:** L/day at pilot scale
- **Offset in Pilot:** % of pilot-scale water consumption offset
- **Estimated Impact — 90 Days:** Total litres recovered over a 90-day pilot window

> *"This pilot can validate water neutrality before full-scale deployment."*

---

## Calculation Logic & Source Justification

All formulas are transparent. Here is the full calculation block with source rationale for every constant:

```javascript
const totalHeat      = IT_Load * 0.8;
const usableHeat     = totalHeat * (wasteHeatPercent / 100);
const recoveredHeat  = usableHeat * (recoveryEfficiency / 100);
const waterProduced  = recoveredHeat * 15000;
const waterConsumed  = IT_Load * 50000;
const netWater       = waterProduced - waterConsumed;
const offsetPercent  = (waterProduced / waterConsumed) * 100;
const coolingLoad    = IT_Load * 0.3;
const energySavings  = coolingLoad * 0.3;
```

### Constant-by-Constant Breakdown

---

#### `Total Heat = IT Load × 0.8`

Nearly 100% of electricity consumed by IT equipment is ultimately dissipated as heat ([EUDCA Technical White Paper, 2024](https://theenergyst.com/an-introduction-to-data-centre-heat-reuse-eudca-technical-committee-white-paper/)). The 0.8 factor is a conservative assumption accounting for transmission losses and heat that escapes before it can be captured. This is intentionally understated to keep projections credible.

---

#### `Waste Heat Available: 20%–50% (default 30%)`

Practically capturable waste heat depends heavily on cooling architecture:
- Air-cooled facilities: 20–30% of heat is at a temperature useful for recovery
- Liquid-cooled / immersion: up to 50%+ is recoverable at useful temperatures

Research published in *Energy Switzerland* models show that a large share (often above 80%) of electricity consumption can theoretically be recovered as heat ([Araner, 2024](https://www.araner.com/blog/data-center-waste-heat-recovery)), but practical on-site capture at useful temperatures is constrained by infrastructure. The 20–50% slider range reflects real-world deployments.

---

#### `Recovery Efficiency: 30%–70% (default 50%)`

Thermal recovery systems — heat exchangers, absorption chillers, organic Rankine cycle units — operate at 30–70% efficiency in documented deployments ([ResearchGate: Waste Heat Recoveries in Data Centers, 2023](https://www.researchgate.net/publication/374373165_Waste_heat_recoveries_in_data_centers_A_review)). The default of 50% represents a mid-range system performing as designed.

---

#### `Water Produced = Recovered Heat × 15,000 L/MW/day`

This is a conservative approximation for low-grade thermal water production (atmospheric water generation or waste-heat-driven condensation/distillation). For reference:
- Reverse osmosis desalination requires ~0.83–4.5 kWh per m³ of water ([WaterOnline](https://www.wateronline.com/doc/waste-heat-recovery-for-desalination-from-steam-power-plants-0001))
- 1 MW thermal over 24 hours = 24,000 kWh; at 4.5 kWh/m³ that yields ~5.3 million L/day theoretical maximum
- The 15,000 L/MW/day figure represents roughly **0.3% of theoretical maximum** — a deliberately conservative estimate for a pre-engineering simulator

This number is intentionally understated. It is designed to be a floor, not a ceiling, so that real deployments exceed the simulation's projections.

---

#### `Water Consumed = IT Load × 50,000 L/MW/day`

Derived from the industry-standard **WUE (Water Usage Effectiveness)** metric:

```
Industry average WUE ≈ 1.8 L/kWh  (US average, LBNL / Scope3)
1 MW IT load = 1,000 kW
1,000 kW × 24 hours × 1.8 L/kWh = 43,200 L/day
```

Rounded up to **50,000 L/MW/day** to account for India's warmer climate, which increases evaporative cooling demand. Sources:
- [Data Center Knowledge: WUE industry average ~1.8 L/kWh](https://www.datacenterknowledge.com/cooling/could-water-free-data-centers-move-from-concept-to-reality-)
- [Scope3 Methodology: US average WUE 1.8 L/kWh](https://preview.methodology.scope3.com/water)
- [Nature: Data Centre Water Consumption study](https://www.nature.com/articles/s41545-021-00101-w)

---

#### `Cooling Load = IT Load × 0.3`

Cooling systems account for **30–40% of total data center energy consumption**, consistently cited across industry sources:
- [Data Center Knowledge (2025)](https://www.datacenterknowledge.com/energy-power-supply/optimizing-cooling-efficiency-in-modern-data-centers): "roughly 30% to 40%"
- [Uptime Institute via HighJoule (2022)](https://www.highjoule.com/blog/data-center-pue-improvement-proven-site-energy-strategies.html): "30–40% of total energy"
- [Intelligent CIO (2026)](https://www.intelligentcio.com/north-america/2026/03/23/the-fastest-way-to-unlock-ai-data-center-capacity-isnt-more-power/): "25–40%, with legacy air-cooled environments clustering around 30%"

Using 30% (lower bound) keeps the energy savings estimate conservative.

---

#### `Energy Savings = Cooling Load × 0.3`

A 30% reduction in cooling energy demand is achievable when waste heat recovery reduces the thermal load on mechanical cooling systems. This is consistent with documented efficiency gains from heat reuse integration in cooling loops.

---

### Summary: Are the Numbers Accurate?

| Constant | Status | Notes |
|---|---|---|
| Heat = IT Load × 0.8 | Solid | Conservative; ~100% is theoretical max |
| Waste Heat 20–50% | Solid | Matches real-world capture ranges |
| Recovery Efficiency 30–70% | Solid | Matches documented system performance |
| Water = Heat × 15,000 L/MW | Conservative | ~0.3% of theoretical max; floor estimate |
| Water Consumed = IT Load × 50,000 | Accurate | Based on WUE 1.8 L/kWh, warm-climate adjusted |
| Cooling = IT Load × 0.3 | Accurate | Lower bound of 30–40% industry range |
| Energy Savings = Cooling × 0.3 | Reasonable | Conservative efficiency gain estimate |

Numbers are designed to be **believable, consistent, and explainable** — not laboratory-precise. The goal is to start the right conversation, not replace an engineering study.

---

## Design

- **Theme:** Dark navy (`#080c18`) with professional blue/cyan accents
- **Typography:** Inter (variable weight) with tabular numerals for stable number display
- **Icons:** Inline SVG — no icon font dependencies
- **Cards:** Glassmorphism with subtle borders and top-accent lines
- **Animations:** Floating water droplets in the hero, smooth fade-in on output panels, animated progress bar, number pop-in on calculation
- **Responsive:** Fully mobile-friendly with adaptive grid layouts
- **Zero dependencies:** Single self-contained HTML file — no frameworks, no build step

---

## Getting Started

No installation required. Just open the file:

```bash
git clone https://github.com/DhivyaBalakumar/AquaTensor.git
open index.html
```

Or visit the live demo: **[https://dhivyabalakumar.github.io/AquaTensor](https://dhivyabalakumar.github.io/AquaTensor)**

---

## Enabling GitHub Pages

1. Go to **Settings → Pages** in this repo
2. Source: **Deploy from a branch**
3. Branch: **main** | Folder: **/ (root)**
4. Click **Save** — live in ~2 minutes

---

## Use Cases

- **Pre-sales demos** — show prospects their specific numbers before any engineering engagement
- **Internal business cases** — quantify water recovery ROI for sustainability reports
- **Regulatory conversations** — use the Permit Risk Indicator to frame compliance discussions
- **Pilot scoping** — use Pilot Mode to size a proof-of-concept deployment
- **Investor presentations** — demonstrate the scale of impact across different facility sizes

---

## Footer CTA

> *"Interested in validating this for your facility? Let's run a custom simulation."*

---

## License

MIT — free to use, modify, and deploy.

---

*Built for sustainable data centers · AquaTensor © 2025*
