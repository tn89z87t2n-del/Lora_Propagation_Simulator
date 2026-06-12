# LoRa Propagation Simulator

Interactive LoRa radio link planning & signal propagation simulator in a **single self-contained `index.html`** — vanilla JS + Canvas, no build step, no dependencies.

**▶ Try it live:** https://raw.githack.com/tn89z87t2n-del/Lora_Propagation_Simulator/main/index.html
(or download `index.html` and open it in any browser)

![status](https://img.shields.io/badge/dependencies-none-success) ![file](https://img.shields.io/badge/single%20file-index.html-blue)

## What it does

A 2D side-view terrain profile between a transmitter (TX) and receiver (RX). Drag the towers around, tune the link budget, and see in real time whether the LoRa link works — with correct RF physics, not eyeballed guesses.

## Features

### Terrain
- Procedurally generated 1D heightmap (fractal value noise, seeded & reproducible)
- Presets: **Flat plain**, **Rolling hills**, **Mountain valley**, **Urban** (building blocks)
- Adjustable roughness, seed input, regenerate button
- Profile span adjustable from **100 m to 50 km** (log slider)
- Optional **Earth curvature** with effective radius k = 4/3

### RF physics (implemented exactly)
- **FSPL**: `FSPL(dB) = 20·log₁₀(d_km) + 20·log₁₀(f_MHz) + 32.45`
- **First Fresnel zone**: `r = 17.32·√(d₁·d₂ / (d·f_GHz))` m — drawn as the ellipsoid between antenna tips, colored **green** (≥80 % clear), **yellow** (60–80 %), **red** (obstructed)
- **Line-of-sight** ray with terrain intersection detection (red ✕ at the obstruction)
- **Single knife-edge diffraction** for the dominant obstacle: Fresnel parameter `v = h·√(2d/(λ·d₁·d₂))`, loss `J(v) = 6.9 + 20·log₁₀(√((v−0.1)²+1) + v − 0.1)` dB (ITU-R P.526 approximation)
- Frequencies: 433 / 868 (EU default) / 915 MHz

### Link budget
- TX power (default **+22 dBm**, like EBYTE E220-900T22D), TX/RX antenna gains (default 2.1 dBi whip), cable losses
- RX sensitivity by spreading factor (BW 125 kHz): SF7 ≈ −124 dBm … SF12 ≈ −137 dBm (clickable table with indicative data rates)
- `RX power = TXpower + gains − FSPL − diffraction loss − cable losses`
- Big color-coded **link margin** readout (green / yellow / red)

### Visualization
- Dark theme, glassmorphism panels, monospace numbers
- Terrain silhouette with gradient fill, sky gradient, glowing TX/RX towers
- Animated signal pulse along the path when the link is up
- Optional **signal heat overlay**: the direct path colored by cumulative loss
- Live chart: **RX power vs distance** (RX swept along the current terrain) with sensitivity threshold lines for every SF

## Controls

| Input | Action |
|---|---|
| Drag tower (mouse/touch) | Move TX/RX position; the antenna tip follows the pointer (height too) |
| `← →` | Nudge selected antenna position |
| `↑ ↓` | Antenna height ±1 m (`Shift` = ±5 m) |
| `Tab` | Switch TX/RX selection |
| `R` | Regenerate terrain with a random seed |
| Sliders & panels | Terrain, span, frequency, SF, link budget — everything updates live |

## Running

No server needed — just open `index.html`. (Or `python3 -m http.server` if you prefer.)

## Code layout

Everything lives in `index.html` with clear sections (comments in Slovak, identifiers in English):

1. **Generovanie terénu** — seeded fBm value noise + presets
2. **RF matematika** — FSPL, Fresnel zone, knife-edge diffraction, earth bulge
3. **Analýza spoja** — path profile sampling, dominant obstacle, link budget, distance sweep
4. **Vykresľovanie** — Canvas scene + chart rendering
5. **UI** — inputs, pointer dragging, keyboard

## Disclaimer

This is a planning aid using standard first-order models (FSPL + single knife-edge). Real-world propagation also involves multipath, vegetation, ground reflections and fading — keep a healthy margin (≥10 dB).
