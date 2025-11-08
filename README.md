## Greenhouse Climate Control System: PDDL+ Planning

**Course:** CISC 813 – Automated Planning  
**Topic:** Continuous and Hybrid Planning (Week 9)  
**Planner Used:** ENHSP-2020 (PDDL+ compatible hybrid heuristic planner)  

**Files Included:**
- `domain.1.pddl`
- `problem.1.pddl`
- `problem.2.pddl`
- `problem.3.pddl`
- `problem.4.pddl`
- `problem.5.pddl`
- `README.md`

---

## 1. Overview of the Domain

This project models an **automated greenhouse** that regulates temperature and humidity for plant growth using a combination of **continuous processes**, **discrete control actions**, and **autonomous environmental events**.

The domain captures the interaction between:
- **Devices:** heaters, vents, humidifiers, lights.
- **Environmental factors:** day/night cycles and weather fronts.
- **Biological growth:** plants mature continuously when conditions are optimal.

The planner must coordinate actions and processes to achieve a final goal state — a successful **harvest** without system failure.

---

### Core Design Principles

| Concept | Implementation Detail |
|----------|-----------------------|
| **Hybrid dynamics (PDDL+)** | Domain integrates both continuous processes and discrete events acting on numeric fluents like temperature, humidity, and growth. |
| **Continuous change** | Numeric functions (e.g., `(temp ?z)`, `(humidity ?z)`) evolve through differential effects defined by processes. |
| **Exogenous events** | Weather fronts, daybreak, and nightfall occur autonomously, driven by countdown timers. |
| **Safety and fault logic** | Overheating triggers `overheat-trip` and forces system recovery via `reset-heater`. |
| **Growth modeling** | Growth occurs only when environmental conditions meet ideal temperature and humidity thresholds. |
| **Resource limits** | Shared `(power-capacity)` enforces mutual exclusion between high-draw devices. |

This domain demonstrates how **temporal and numeric reasoning** combine in a hybrid control system where the planner must maintain balance between environmental dynamics and hardware constraints.

---

## 2. Domain Structure

The domain defines:
- **Predicates:** Boolean device states, mode indicators, and failure/harvest conditions.  
- **Functions:** Continuous state variables for temperature, humidity, and energy.  
- **Processes:** Continuous environmental and biological dynamics.  
- **Events:** Autonomous mode shifts (e.g., day/night, weather arrival).  
- **Actions:** Manual control interventions available to the planner.

### Key Actions

| Action | Description |
|---------|--------------|
| `heater-on` / `heater-off` | Toggles a zone’s heating process within power capacity limits. |
| `humidifier-on` / `humidifier-off` | Activates humidity control to increase ambient moisture. |
| `open-vent` / `close-vent` | Exchanges air to cool or dry the environment. |
| `reset-heater` | Resets a tripped heater once the temperature falls below safe thresholds. |
| `harvest` | Marks a zone as successfully grown and harvested once maturity ≥ 1.0. |

### Key Processes

| Process | Description |
|----------|--------------|
| `heat` | Increases `(temp ?z)` while `(heater-on ?z)` and power is available. |
| `cool-to-ambient` / `warm-to-ambient` | Drives temperatures toward `(ambient-temp)`. |
| `humidify` | Gradually increases humidity levels while humidifier is active. |
| `vent-dry-down` / `vent-dry-up` | Reduces or raises humidity through vent airflow. |
| `grow` | Increases `(maturity ?z)` when both `(temp ?z)` and `(humidity ?z)` are within optimal ranges. |

---

## 3. Problem Set Descriptions

### **Problem 1 — Baseline Heating and Growth**

A single zone (`z1`) starts slightly below its optimal temperature and humidity.  
The planner activates the heater and humidifier to enter the growth window and waits until maturity reaches the harvest threshold.

**Purpose:**  
Establishes fundamental continuous control using heating and humidity regulation.

---

### **Problem 2 — Power-Constrained Control**

Introduces a shared power capacity that prevents simultaneous heating and humidifying.  
The planner must alternate between devices to achieve growth efficiently.

**Purpose:**  
Tests numeric resource reasoning and sequencing under power limits.

---

### **Problem 3 — Weather Front Disturbance**

A simulated weather front autonomously modifies ambient temperature and humidity mid-cycle.  
The planner must stabilize internal conditions and continue growth through the perturbation.

**Purpose:**  
Demonstrates adaptive reasoning under exogenous environmental events.

---

### **Problem 4 — Day and Night Cycle**

Models autonomous day/night transitions using `(daybreak)` and `(nightfall)` events.  
The system begins at night; solar heat is applied only during the daytime interval.

**Purpose:**  
Tests event synchronization, mode-dependent processes, and continuous transitions.

---

### **Problem 5 — Shared Power Across Zones**

Adds a second zone (`z2`) sharing the same energy capacity:
- Only `z1` can draw heat (its power draw fits within limits).  
- `z2` starts fully mature and ready for harvest.  
- The planner heats `z1` briefly, then harvests both zones.

**Representative Plan:**
