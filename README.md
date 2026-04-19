# AquaTensor — Water Recovery Simulator

> A decision simulator for data center operators to estimate water recovery potential from waste heat using the AquaTensor system.

**Live Demo:** [https://dhivyabalakumar.github.io/AquaTensor](https://dhivyabalakumar.github.io/AquaTensor)

---

## Overview

Data centers consume enormous volumes of water for cooling — and simultaneously waste vast amounts of heat. AquaTensor turns that wasted heat into recovered water, reducing a facility's net water consumption and improving its regulatory standing.

This simulator lets data center operators, sustainability leads, and infrastructure planners model exactly how much water their facility could recover — before committing to a full deployment. Enter your facility parameters, run the simulation, and get a detailed breakdown of heat recovery, water production, energy savings, and permit risk — all in real time.

---

## Live Screens

### Screen 1 — Hero
The landing view introduces the simulator with a professional dark interface featuring an animated floating water droplet background. A single call-to-action button scrolls directly into the configuration panel.

### Screen 2 — Facility Parameters (Input Panel)
Three configuration sections let you describe your data center:

**Section A — Facility Basics**
- **IT Load (MW):** Slider from 5 MW to 100 MW. This is the primary driver of all calculations.
- **Location:** Dropdown covering major Indian data center hubs — Bangalore, Chennai, Hyderabad, Mumbai, Delhi, Pune, Kolkata.
- **Cooling Type:** Select from Air Cooling, Evaporative Cooling, or Liquid Cooling.

**Section B — Water Usage**
- **Water Consumption (litres/day):** Auto-filled based on IT Load using the formula `IT Load × 150,000 L/MW/day`. Fully editable — override with your actual facility data for more accurate results.

**Section C — Heat Recovery Assumptions**
- **Waste Heat Available (%):** Slider from 20% to 50%. Represents the fraction of total waste heat that can be captured.
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
Shown as both a percentage and an animated progress bar. Tells you what fraction of your total water consumption is being offset by recovery.

#### 5. Energy Savings
```
Cooling Load (MW) = IT Load × 0.3
Energy Savings (MW) = Cooling Load × 0.3
```
Quantifies the reduction in cooling energy demand when waste heat is actively recovered and reused.

#### 6. Permit Risk Indicator
A regulatory risk signal based on your offset percentage:

| Offset % | Risk Level | Indicator |
|---|---|---|
| Below 20% | High Risk | Red |
| 20% – 60% | Medium Risk | Amber |
| Above 60% | Low Risk | Green |

This is the metric that matters most in conversations with regulators and permit authorities.

---

### Screen 4 — Before vs. After Comparison

A side-by-side panel showing your facility's profile with and without AquaTensor deployed:

| Metric | Without AquaTensor | With AquaTensor |
|---|---|---|
| Water Usage | Full consumption | Reduced |
| Heat Wasted | High | Optimised |
| Water Recovered | 0 L/day | Calculated value |
| Permit Risk | High | Calculated risk level |
| Sustainability Score | Low | High |

All values are populated from your simulation run — no static placeholders.

---

### Screen 5 — Pilot Simulation Mode

Designed for pre-deployment validation and sales conversations. Configure a smaller pilot deployment and see projected impact:

- **Pilot Size (MW):** Slider from 1 MW to 20 MW (default 5 MW)
- **Water Recovered in Pilot:** L/day at pilot scale
- **Offset in Pilot:** % of pilot-scale water consumption offset
- **Estimated Impact — 90 Days:** Total litres recovered over a 90-day pilot window

> *"This pilot can validate water neutrality before full-scale deployment."*

This screen is intentionally designed as a conversation starter — giving operators a low-risk entry point to validate the technology before committing to full-scale rollout.

---

## Calculation Logic

All formulas are transparent and consistent throughout the simulator:

```javascript
const totalHeat      = IT_Load * 0.8;
const usableHeat     = totalHeat * (wasteHeatPercent / 100);
const recoveredHeat  = usableHeat * (recoveryEfficiency / 100);
const waterProduced  = recoveredHeat * 15000;          // 1 MW thermal ≈ 15,000 L/day
const waterConsumed  = IT_Load * 150000;               // baseline: 150,000 L/MW/day
const netWater       = waterProduced - waterConsumed;
const offsetPercent  = (waterProduced / waterConsumed) * 100;
const coolingLoad    = IT_Load * 0.3;
const energySavings  = coolingLoad * 0.3;
```

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
# Clone the repo
git clone https://github.com/DhivyaBalakumar/AquaTensor.git

# Open in browser
open index.html
```

Or visit the live demo directly:
**[https://dhivyabalakumar.github.io/AquaTensor](https://dhivyabalakumar.github.io/AquaTensor)**

---

## Enabling GitHub Pages

To host this yourself via GitHub Pages:

1. Go to your repo on GitHub
2. Navigate to **Settings → Pages**
3. Under **Source**, select **Deploy from a branch**
4. Choose **main** branch, **/ (root)** folder
5. Click **Save** — your site will be live at `https://<your-username>.github.io/AquaTensor`

---

## Use Cases

- **Pre-sales demos** — show prospects their specific numbers before any engineering engagement
- **Internal business cases** — quantify water recovery ROI for sustainability reports
- **Regulatory conversations** — use the Permit Risk Indicator to frame compliance discussions
- **Pilot scoping** — use Pilot Mode to size a proof-of-concept deployment
- **Investor presentations** — demonstrate the scale of impact across different facility sizes

---

## Footer CTA

At the bottom of every simulation, a persistent call-to-action invites operators to request a custom simulation — turning the tool into a lead generation asset.

> *"Interested in validating this for your facility? Let's run a custom simulation."*

---

## License

MIT — free to use, modify, and deploy.

---

*Built for sustainable data centers · AquaTensor © 2025*
