# Autonomous Delivery Intelligence Platform

**By Dean Guardanapo**

An interactive single-page web app exploring how NVIDIA's robotics stack solves the compute and latency limitations that hold back autonomous sidewalk delivery robots — using Starship Technologies as the reference platform.

> **Live demo:** [https://deanguardanapo.github.io/adip-nvidia/](https://deanguardanapo.github.io/adip-nvidia/)

---

## Screenshots

> **To add screenshots:** open `index.html` in Chrome, visit each tab, and press `Cmd+Shift+4` to capture. Save files into the `screenshots/` folder with the names below.

### About
<!-- Replace the line below once you have the screenshot -->
![About tab](screenshots/tab-about.png)

The **About** tab is the landing page. It establishes the problem: sidewalk delivery robots stall, reroute, and time out in the real world because their onboard compute cannot process camera + LiDAR + mapping data fast enough. Two cards summarize the challenge and NVIDIA's solution (Isaac ROS, Jetson Orin, Isaac Sim digital twins). A "Launch Simulation" call-to-action jumps directly to the Live Simulation tab.

---

### Live Simulation
<!-- Replace the line below once you have the screenshot -->
![Live Simulation tab](screenshots/tab-simulation.png)

The **Live Simulation** tab is the core of the app. Two bots race down parallel sidewalk paths with identical obstacles:

| Element | What it shows |
|---|---|
| **Legacy bot (grey)** | Slower obstacle detection (~14 px radius), higher latency badge (ms), prone to compute stalls |
| **NVIDIA bot (green)** | Wider detection aura (~30 px radius), lower latency badge, never stalls |
| **Dashed detection ring** | Visual radius at which each bot reacts — NVIDIA's ring is visibly larger |
| **Latency badge** | Floating ms pill above each bot; updates live with scenario load |
| **Obstacle types** | Pedestrians (blue), cars (red), crossers walking across the path (yellow) |
| **Speed slider** | 0.25× to 3× simulation speed |
| **Scenario selector** | Urban Sidewalk → Dense Intersection → Highway Crossing → Multi-Sensor Fusion |
| **New City Launch toggle** | Simulates an unmapped city — Legacy stalls more; NVIDIA maps digitally via Isaac Sim |
| **Error log** | Timestamped stall events with the specific compute failure reason |

When Legacy stalls, a **modal popup** pauses the simulation and explains what failed and how NVIDIA's stack would have handled it.

---

### Stall Modal (appears mid-simulation)
<!-- Replace the line below once you have the screenshot -->
![Stall modal](screenshots/tab-stall-modal.png)

The stall modal freezes the Legacy bot and surfaces the root cause — e.g., "Sensor fusion pipeline exceeded 120 ms deadline" — alongside NVIDIA's response ("Isaac ROS processes the same pipeline in 18 ms on Jetson Orin"). Click **Resume** to continue the simulation.

---

### Stack Comparison
<!-- Replace the line below once you have the screenshot -->
![Stack Comparison tab](screenshots/tab-stack.png)

The **Stack Comparison** tab is a side-by-side feature grid covering eight dimensions:

| Dimension | Legacy | NVIDIA |
|---|---|---|
| Compute Platform | ARM Cortex-A53 | Jetson Orin NX |
| Sensor Fusion Latency | 120–180 ms | 12–18 ms |
| Simultaneous Mapping | No (pre-survey required) | Yes (Isaac Sim digital twin) |
| SLAM Performance | Limited | Full GPU-accelerated SLAM |
| Obstacle Detection Range | 10–20 px equivalent | 28–36 px equivalent |
| New City Deployment | 4–8 weeks human survey | Days via Isaac Sim |
| OTA Model Updates | Manual | Continuous via NVIDIA cloud |
| Stall Rate | 15–25% in complex scenes | <1% |

---

### Library Reference
<!-- Replace the line below once you have the screenshot -->
![Library Reference tab](screenshots/tab-library.png)

The **Library Reference** tab links to the NVIDIA SDKs and tools referenced throughout the app:

- **Isaac ROS** — GPU-accelerated ROS 2 packages for real-time sensor fusion and perception
- **Isaac Sim** — Photorealistic simulation and digital twin platform built on Omniverse
- **Jetson Orin** — Edge AI compute module powering the NVIDIA bot in the simulation
- **CUDA Toolkit** — Parallel compute foundation underlying all NVIDIA robotics inference
- **cuDNN** — Deep neural network library optimized for Jetson hardware
- **TAO Toolkit** — Transfer-learning framework for fine-tuning perception models on new city data

---

## Architecture

The entire app is a **single HTML file** (`index.html`) with no external dependencies, build steps, or frameworks. Everything — HTML structure, CSS styles, and JavaScript simulation engine — lives in one file that opens directly in any modern browser.

### Simulation engine highlights

```
Dual-path layout
  PATH1 (y=160) — Legacy bot lane
  PATH2 (y=320) — NVIDIA bot lane

Obstacles generated once near PATH1, then mirrored
to PATH2 by drawing at (o.y + PATHGAP) — identical
challenge for both bots.

Latency-based detection radius
  Legacy:  detect = max(10, 22 − legLat × 0.09)
  NVIDIA:  detect = max(28, 36 − nvLat)

Avoidance: lateral-only Y push (never backward on X)
  lat = (ddy / |ddy|) × pushForce
  ny += lat

Path snap pulls each bot back to its lane every frame
  ny += (pathY − ny) × snapStrength
```

### Scenario parameters

| Scenario | Legacy load | NV load | Legacy latency | NV latency | Stall threshold |
|---|---|---|---|---|---|
| Urban Sidewalk | 68% | 22% | 95 ms | 14 ms | 72% |
| Dense Intersection | 82% | 31% | 138 ms | 18 ms | 80% |
| Highway Crossing | 91% | 38% | 162 ms | 22 ms | 85% |
| Multi-Sensor Fusion | 97% | 44% | 185 ms | 28 ms | 88% |

---

## Running locally

No server required — just open the file:

```bash
# Option 1: double-click index.html in Finder
# Option 2: drag index.html into Chrome
# Option 3: python quick-server
cd adip-nvidia
python3 -m http.server 8080
# then open http://localhost:8080
```

---

## Tech stack

| Layer | Choice |
|---|---|
| UI | Vanilla HTML5 + CSS3 (no frameworks) |
| Simulation | HTML5 Canvas 2D API |
| Fonts | System font stack |
| Hosting | GitHub Pages (main branch root) |
| Version control | Git + GitHub CLI |

---

## License

MIT — free to use, modify, and distribute.
