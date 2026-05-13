# CityMind AI Simulation

An AI-driven smart city simulation that combines constraint satisfaction, genetic algorithms, and machine learning to optimize urban planning, emergency dispatch, and public safety.

## Features

| Module | Algorithm | Purpose |
|--------|-----------|---------|
| City Layout | CSP (Backtracking + AC-3) | Place hospitals, schools, industrial zones, power plants, and depots subject to zoning constraints |
| Ambulance Dispatch | Genetic Algorithm | Minimize worst-case travel distance from ambulances to all residential nodes |
| Crime Risk | K-Means + 5-classifier ML pipeline | Cluster nodes by risk, predict LOW/MEDIUM/HIGH labels, allocate police officers proportionally |
| Emergency Routing | Dijkstra + greedy ordering | Route ambulances through civilian targets with risk-weighted road costs |
| Road Network | Random spanning graph | Generate connected grid-based city graph with blocked/accessible nodes |

## Project Structure

```
.
├── algorithms/
│   ├── ambulance_ga.py     # Genetic algorithm for ambulance placement
│   ├── crime_risk.py       # Crime risk pipeline (KMeans + RF/GB/DT/KNN/LR)
│   ├── csp_layout.py       # CSP city layout solver
│   ├── road_network.py     # Road network generation
│   └── routing.py          # Emergency routing (Dijkstra)
├── city/
│   ├── graph.py            # CityGraph with Dijkstra, Manhattan distance
│   ├── node.py             # Node model (type, risk, population density)
│   └── constants.py        # Enums: LocationType, RiskLevel
├── simulation/
│   ├── simulator.py        # Main simulation orchestrator
│   └── event_log.py        # Tick-based event logging
├── gui/
│   └── main_window.py      # PySide6 GUI with interactive map
├── config.py               # Simulation parameters
├── run_gui.py              # Launch GUI
└── run_console.py          # Launch console mode
```

## Setup

```bash
pip install -r requirements.txt
```

**Requirements:** Python 3.10+, PySide6, NumPy, scikit-learn (for reference), pytest

## Usage

**GUI mode:**
```bash
python run_gui.py
```

**Console mode:**
```bash
python run_console.py
```

## Configuration

Edit `config.py` to tune simulation parameters:

```python
defaultRows = 20          # Grid height
defaultCols = 20          # Grid width
numAmbulances = 3         # Ambulances to place via GA
numPoliceOfficers = 10    # Officers allocated by crime risk model
numHospitals = 3
numResidential = 30
simulationTicks = 20
```

## Algorithms in Detail

### CSP City Layout
Assigns facility types to grid nodes using backtracking search with Arc Consistency (AC-3) propagation. Constraints enforce minimum separation distances between facility types and zoning rules.

### Ambulance Placement (Genetic Algorithm)
- **Chromosome:** set of `numAmbulances` node IDs
- **Fitness:** negative worst-case Dijkstra distance from any ambulance to any residential node
- **Selection:** tournament (size 3), **Crossover:** uniform, **Mutation:** random gene replacement (10%)
- Runs for 100 generations with population size 60

### Crime Risk Pipeline
1. Build feature matrix: population density, industrial proximity, location type
2. K-Means++ clustering (3 clusters) to rank zones by base risk
3. Generate synthetic labels (LOW / MEDIUM / HIGH) from cluster rank + noise
4. Train and evaluate 5 classifiers: Random Forest, Gradient Boosting, Decision Tree, KNN, Logistic Regression
5. Select best by macro-F1; predict labels on full city; allocate police proportionally

### Emergency Routing
Dijkstra shortest path with risk-weighted edge costs (`riskCostMultiplier`). Greedy nearest-neighbor ordering over civilian target nodes from the ambulance depot.

## Team

| Student ID |
|------------|
| i240525    |
| i240806    |
| i240653    |
