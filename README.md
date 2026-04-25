# 🧠 Neural Command Center
### AI Problem Solving Assignment — Interactive Web Visualizer

> **Course:** Artificial Intelligence  
> **Student ID:** RA2411026050331  
> **Deadline:** 25 April 2026  
> **Deployment:** Static Site (Vercel)

---

## 📌 Table of Contents

1. [Project Overview](#-project-overview)
2. [Live Demo](#-live-demo)
3. [Repository Structure](#-repository-structure)
4. [Tech Stack](#-tech-stack)
5. [Design Philosophy](#-design-philosophy)
6. [Problem 9 — Drone Delivery Path Finder (BFS / DFS)](#-problem-9--drone-delivery-path-finder-bfs--dfs)
   - [Problem Statement](#problem-statement)
   - [Algorithms Implemented](#algorithms-implemented)
   - [BFS — Breadth-First Search](#bfs--breadth-first-search)
   - [DFS — Depth-First Search](#dfs--depth-first-search)
   - [BFS vs DFS Comparison](#bfs-vs-dfs-comparison)
   - [How to Use the Grid](#how-to-use-the-grid)
   - [Grid Controls Reference](#grid-controls-reference)
   - [Sample Grid (Assignment PDF)](#sample-grid-assignment-pdf)
   - [Results Displayed](#results-displayed)
   - [Code Architecture — P9](#code-architecture--p9)
7. [Problem 11 — GPS-Based City Route Finder (A\*)](#-problem-11--gps-based-city-route-finder-a)
   - [Problem Statement](#problem-statement-1)
   - [Algorithm Implemented](#algorithm-implemented)
   - [A\* Search — Deep Dive](#a-search--deep-dive)
   - [Heuristic Function](#heuristic-function)
   - [Why A\* over BFS/DFS?](#why-a-over-bfsdfs)
   - [How to Use the Graph Builder](#how-to-use-the-graph-builder)
   - [Graph Controls Reference](#graph-controls-reference)
   - [Sample Graph (Assignment PDF)](#sample-graph-assignment-pdf)
   - [Results Displayed](#results-displayed-1)
   - [Code Architecture — P11](#code-architecture--p11)
8. [UI Architecture](#-ui-architecture)
9. [Color System & Theming](#-color-system--theming)
10. [Deployment Guide (Vercel)](#-deployment-guide-vercel)
11. [Running Locally](#-running-locally)
12. [Bug Fixed](#-bug-fixed)
13. [Known Limitations](#-known-limitations)

---

## 📖 Project Overview

This project is a **fully interactive, browser-based visualizer** for two AI search algorithm problems from the course assignment. Both problems are implemented from scratch in **vanilla JavaScript** — no libraries, no frameworks, no external dependencies beyond Google Fonts.

The interface is themed as a **retro-futuristic "Mission Control" terminal** — dark space aesthetic with neon accents, scanline overlays, animated particle backgrounds, and Orbitron + Space Mono typography — designed to be both academically rigorous and visually distinctive.

Both problems run entirely in the browser. There is no backend, no server, no build step. The entire application is a **single HTML file (`index.html`)** with embedded CSS and JavaScript.

---

## 🌐 Live Demo

Deploy to Vercel in under 2 minutes — see [Deployment Guide](#-deployment-guide-vercel) below.

---

## 📁 Repository Structure

```
├── index.html        ← Entire application (HTML + CSS + JS, ~1780 lines)
├── vercel.json       ← Vercel static deployment config
└── README.md         ← This file
```

That's it. No `node_modules`, no `package.json`, no build pipeline. Pure static web — open `index.html` in any browser and it works.

---

## 🛠 Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Markup | HTML5 | Semantic structure, canvas elements |
| Styling | CSS3 (vanilla, ~700 lines) | CSS variables, animations, grid layout |
| Logic | JavaScript ES2022 (vanilla) | IIFE modules, async/await, Canvas 2D API |
| Rendering | HTML5 Canvas API | Pixel-perfect grid and graph drawing |
| Fonts | Google Fonts (Orbitron + Space Mono) | Retro-futuristic aesthetic |
| Hosting | Vercel (static) | Zero-config, instant deploy, free tier |

**Zero runtime dependencies.** Everything is self-contained.

---

## 🎨 Design Philosophy

The interface is built around a **"Neural Command Center"** concept — a mission control terminal an AI operator would use to dispatch drones and route navigators.

**Aesthetic choices:**
- **Dark deep-space background** (`#030b18`) with animated floating particles and a subtle dot-grid overlay
- **Scanline overlay** (CSS `repeating-linear-gradient`) gives the CRT monitor feel
- **Color-coded algorithms** — BFS is always **cyan**, DFS is always **purple**, A\* is always **amber** — consistent across buttons, paths, stats, and result panels
- **Orbitron** (display font) for titles, labels, section headers — wide letter-spacing gives the "computer terminal" feeling
- **Space Mono** (monospace body font) for all data, coordinates, and results — perfect for tabular algorithm output
- **Neon glow effects** on active elements using layered `box-shadow` values
- **CSS custom properties** for every color — the entire palette is defined in `:root` and used consistently throughout

---

## 🚁 Problem 9 — Drone Delivery Path Finder (BFS / DFS)

### Problem Statement

A delivery drone must travel from a **warehouse (Start)** to a **customer location (Goal)** across a **15×15 city grid**. Some cells are **blocked** (no-fly zones, buildings, obstacles). The drone can only move in **4 directions** (up, down, left, right — no diagonals).

The system finds and visualizes valid paths using two graph search algorithms and compares their performance side by side.

---

### Algorithms Implemented

#### BFS — Breadth-First Search

**Core Idea:** Explore all cells at distance 1 first, then all cells at distance 2, then distance 3, and so on. This "wave expansion" guarantees the first time the goal is reached, it's via the shortest possible path.

**Data Structure:** Queue (FIFO — First In, First Out)

**Implementation:**
```javascript
function bfs() {
  const t0 = performance.now();
  const q = [[start, [start]]];                   // queue of [cell, pathSoFar]
  const vis = new Set([`${start[0]},${start[1]}`]); // visited set (O(1) lookup)
  const explored = [];

  while (q.length) {
    const [[r, c], path] = q.shift();             // dequeue from FRONT (FIFO)
    explored.push([r, c]);
    if (r === goal[0] && c === goal[1]) return { path, explored, time: performance.now() - t0 };

    for (const [dr, dc] of [[-1,0],[1,0],[0,-1],[0,1]]) {  // 4-directional
      const nr = r + dr, nc = c + dc, k = `${nr},${nc}`;
      if (nr>=0 && nr<ROWS && nc>=0 && nc<COLS &&
          grid[nr][nc] === 0 && !vis.has(k)) {
        vis.add(k);
        q.push([[nr, nc], [...path, [nr, nc]]]);
      }
    }
  }
  return { path: null, explored, time: performance.now() - t0 };
}
```

**Complexity:**
| | Value |
|--|--|
| Time | O(V + E) — visits every reachable cell at most once |
| Space | O(V) — stores all frontier cells in the queue |
| Optimality | ✅ **Guaranteed shortest path** (unweighted grid) |

**Visual:** Explored cells glow in **cyan** (`rgba(0,212,255,0.14)`). The found path is drawn as a solid **cyan line with directional arrows**.

---

#### DFS — Depth-First Search

**Core Idea:** Follow one path as deep as possible before backtracking. Uses a stack so the most recently added cell is explored first — diving into one branch before expanding laterally.

**Data Structure:** Stack (LIFO — Last In, First Out)

**Implementation:**
```javascript
function dfs() {
  const t0 = performance.now();
  const stk = [[start, [start]]];                 // stack of [cell, pathSoFar]
  const vis = new Set([`${start[0]},${start[1]}`]);
  const explored = [];

  while (stk.length) {
    const [[r, c], path] = stk.pop();             // pop from TOP (LIFO)
    explored.push([r, c]);
    if (r === goal[0] && c === goal[1]) return { path, explored, time: performance.now() - t0 };

    for (const [dr, dc] of [[-1,0],[1,0],[0,-1],[0,1]]) {
      const nr = r + dr, nc = c + dc, k = `${nr},${nc}`;
      if (nr>=0 && nr<ROWS && nc>=0 && nc<COLS &&
          grid[nr][nc] === 0 && !vis.has(k)) {
        vis.add(k);
        stk.push([[nr, nc], [...path, [nr, nc]]]);
      }
    }
  }
  return { path: null, explored, time: performance.now() - t0 };
}
```

The **only structural difference** from BFS is `q.shift()` (dequeue front) vs `stk.pop()` (pop top). This single change completely alters the traversal behavior.

**Complexity:**
| | Value |
|--|--|
| Time | O(V + E) — same as BFS in worst case |
| Space | O(V) — call stack / explicit stack depth |
| Optimality | ❌ **Not guaranteed** — may find a longer path |

**Visual:** Explored cells glow in **purple** (`rgba(192,132,252,0.14)`). The found path is drawn as a solid **purple line with directional arrows**.

---

#### BFS vs DFS Comparison

| Metric | BFS | DFS |
|--------|-----|-----|
| Data Structure | Queue (FIFO) | Stack (LIFO) |
| Traversal Pattern | Level by level (breadth-first) | One branch at a time (depth-first) |
| Path Optimality | ✅ Shortest path guaranteed | ❌ Not guaranteed |
| Nodes Explored | More (all breadths before going deep) | Fewer on sparse graphs (one branch) |
| Memory Usage | Higher (stores entire frontier) | Lower (stores one branch) |
| Time on this grid | Slightly slower (explores more) | Faster (fewer nodes explored) |
| Best Use Case | When shortest path is critical (e.g., flight efficiency) | When memory is constrained |
| Completeness | ✅ Always finds a path if one exists | ✅ Always finds a path if one exists |

When **"Run Both"** is clicked, cells on **both** paths are highlighted in **amber** (shared path segments).

---

### How to Use the Grid

The 15×15 grid is rendered on an HTML5 Canvas element (`600×600px`, each cell = 40×40px). Interaction is handled via `mousedown` and `mousemove` events with DPI-aware coordinate scaling.

**Step-by-step workflow:**
1. Select **"SET START"** mode → click any free cell → green `S` cell is placed
2. Select **"SET GOAL"** mode → click any free cell → red `G` cell is placed
3. Select **"TOGGLE WALL"** mode → click or **click-and-drag** to draw walls (obstacles)
   - Click a free cell → it becomes a wall
   - Click a wall cell → it becomes free (toggle behavior)
   - Drag continuously to paint walls in one motion
4. Click **▶ RUN BFS**, **▶ RUN DFS**, or **⚡ RUN BOTH**
5. Results appear in the right panel; stat cards update below the grid
6. Modify the grid (start/goal/walls) → results auto-clear → re-run anytime

---

### Grid Controls Reference

| Control | Mode | Action |
|---------|------|--------|
| 📍 SET START | Click | Places the Start cell (S). Clears wall at that position. |
| 🎯 SET GOAL | Click | Places the Goal cell (G). Clears wall at that position. |
| 🧱 TOGGLE WALL | Click / Drag | Toggles wall on/off. Drag to paint/erase multiple cells. Cannot overwrite S or G. |
| ▶ RUN BFS | Button | Runs BFS, renders cyan path + explored cells, updates results panel |
| ▶ RUN DFS | Button | Runs DFS, renders purple path + explored cells, updates results panel |
| ⚡ RUN BOTH | Button | Runs both simultaneously, renders both paths, shows comparison table |
| 📋 SAMPLE | Button | Loads the exact grid from the assignment PDF (walls + S at (0,0), G at (8,12)) |
| 🔄 CLEAR | Button | Resets entire grid to empty, clears all results |

---

### Sample Grid (Assignment PDF)

Clicking **📋 SAMPLE** loads this exact configuration:

```
Row 0:  . . . █ . . . . . . . . . . .   ← S at (0,0)
Row 1:  █ █ . █ . . . . . . . . . . .
Row 2:  . . . . . . . . . . . . . . .
Row 3:  . █ █ █ . . . . . . . . . . .
Row 4:  . . . . . . . . . . . . . . .
Row 5:  . . █ . . █ █ . . . . . . . .
Row 6:  . . █ . . . █ . . . . . . . .
Row 7:  . . . . █ . . . . . . . . . .
Row 8:  . . . . . . . . . . . . . . .   ← G at (8,12)
```

Legend: `.` = free cell, `█` = wall

Start `(0,0)` → Goal `(8,12)` — BFS finds the **optimal 21-step path**, DFS finds a valid but longer path (~49 steps) while exploring fewer nodes.

---

### Results Displayed

After running an algorithm, the right panel shows:

**Single algorithm run:**
- ✔ PATH FOUND / ✘ NO PATH EXISTS
- Path Length (steps)
- Nodes Explored
- Execution Time (milliseconds, 4 decimal precision)
- Full path sequence as coordinate tags: `(r,c) → (r,c) → ...`

**Run Both (comparison):**
- Side-by-side metrics table: BFS vs DFS
- Analysis: which is optimal, which was faster
- Individual path sequences for both algorithms
- Automated commentary explaining the difference

**Stat Cards (below grid):**
Dynamic colored cards showing the most important metric for each run. Cyan for BFS, purple for DFS.

---

### Code Architecture — P9

P9 is implemented as a **JavaScript IIFE (Immediately Invoked Function Expression)** — a self-contained module with private state:

```
P9 Module (IIFE)
├── State
│   ├── grid[15][15]       — 2D array, 0=free, 1=wall
│   ├── start, goal        — [row, col] tuples or null
│   └── dragValue          — current wall paint value (0 or 1)
│
├── Rendering
│   ├── draw(bfsPath, dfsPath, bfsVis, dfsVis)  — full grid redraw
│   └── drawArrows(path, color, width)          — directional arrows on path
│
├── Interaction
│   ├── onMouseDown(e)     — cell click: set start / goal / toggle wall
│   ├── onMouseMove(e)     — drag: continuous wall painting
│   └── cellFrom(e)        — converts pixel coords → [row, col] with DPI scaling
│
├── Algorithms
│   ├── bfs()              — returns { path, explored, time }
│   └── dfs()              — returns { path, explored, time }
│
└── Output
    ├── renderSingleResult(algo, result)
    ├── renderComparison(bfsResult, dfsResult)
    └── setStats(bfsResult, dfsResult)
```

---

## 🗺 Problem 11 — GPS-Based City Route Finder (A\*)

### Problem Statement

A GPS navigation system must find the **most efficient route** between two city locations. The city is represented as a **weighted undirected graph** where nodes are cities/locations and edges are roads with travel costs (distances/times). The system uses A\* to find the optimal path.

---

### Algorithm Implemented

#### A\* Search — Deep Dive

A\* (pronounced "A-star") is an **informed search algorithm** — unlike BFS and DFS which are uninformed (they know nothing about the goal's location), A\* uses a **heuristic function** to guide its search toward the goal more efficiently.

**Core Formula:**

```
f(n) = g(n) + h(n)
```

| Symbol | Name | Meaning |
|--------|------|---------|
| `f(n)` | Total estimated cost | Priority value — lower = explored first |
| `g(n)` | Actual cost | True accumulated cost from start node to node `n` |
| `h(n)` | Heuristic estimate | Estimated cost from `n` to the goal (never overestimates) |

A\* always expands the node with the **lowest f(n)** from the priority queue — this is what makes it both optimal and efficient.

**Implementation:**
```javascript
function astar() {
  const t0 = performance.now();
  function h(n) { return euclidean(n, goalNode); }   // heuristic

  // Priority queue: [f, g, nodeName, pathArray]
  const openSet = [[h(startNode), 0, startNode, [startNode]]];
  const gScore  = { [startNode]: 0 };
  const fScore  = { [startNode]: h(startNode) };
  const closed  = new Set();
  const explored = [];

  while (openSet.length) {
    openSet.sort((a, b) => a[0] - b[0] || a[1] - b[1]); // sort by f, then g
    const [f, g, node, path] = openSet.shift();

    if (closed.has(node)) continue;
    closed.add(node);
    explored.push(node);

    if (node === goalNode) {
      return { path, explored, cost: g, gScore, fScore,
               time: performance.now() - t0 };
    }

    for (const [nb, cost] of Object.entries(adj[node] || {})) {
      if (closed.has(nb)) continue;
      const tg = g + cost;
      if (tg < (gScore[nb] ?? Infinity)) {
        gScore[nb] = tg;
        fScore[nb] = tg + h(nb);
        openSet.push([fScore[nb], tg, nb, [...path, nb]]);
      }
    }
  }
  return { path: null, explored, cost: Infinity, gScore, fScore,
           time: performance.now() - t0 };
}
```

**Complexity:**
| | Value |
|--|--|
| Time | O(E log V) with a binary heap |
| Space | O(V) — open and closed sets |
| Optimality | ✅ **Guaranteed optimal** if heuristic is admissible |
| Completeness | ✅ Always finds a path if one exists |

---

### Heuristic Function

```javascript
function euclidean(a, b) {
  const [x1, y1] = nodes[a];
  const [x2, y2] = nodes[b];
  return Math.hypot(x2 - x1, y2 - y1);  // √((x2-x1)² + (y2-y1)²)
}
```

The heuristic is the **Euclidean distance** between a node's canvas position and the goal node's canvas position (in pixels).

**Is this heuristic admissible?**  
Yes — Euclidean distance is the straight-line distance between two points, which is always ≤ the actual path cost through edges (you can't travel shorter than a straight line). So `h(n) ≤ true cost` — it never overestimates. This guarantees A\* finds the optimal path.

**Is this heuristic consistent (monotone)?**  
Yes — it satisfies the triangle inequality: `h(n) ≤ cost(n, n') + h(n')` for any neighbor `n'`. Consistent heuristics mean each node is processed at most once, improving efficiency.

**Trade-off note:** The heuristic uses canvas pixel positions, not the edge weights themselves. This works well for visual exploration but in a real GPS application the heuristic would use geographic coordinates (Haversine formula).

---

### Why A\* over BFS/DFS?

| Algorithm | Handles Weights? | Finds Optimal Path? | Uses Heuristic? | Best For |
|-----------|:---:|:---:|:---:|---------|
| BFS | ❌ (treats all edges as cost 1) | ✅ (unweighted only) | ❌ | Unweighted shortest path |
| DFS | ❌ | ❌ | ❌ | Memory-constrained traversal |
| Dijkstra | ✅ | ✅ | ❌ | Weighted shortest path (no heuristic) |
| **A\*** | **✅** | **✅** | **✅** | **Weighted graphs with spatial locality** |

A\* is strictly superior to Dijkstra for single-target queries because the heuristic focuses the search toward the goal — Dijkstra expands in all directions equally, A\* expands preferentially toward the goal.

---

### How to Use the Graph Builder

**Step-by-step workflow:**

1. **Add Nodes** — Select **"➕ ADD NODE"** mode → click anywhere on the canvas → a dialog prompts for a city name (e.g., `A`, `B`, `Mumbai`, `Chennai`) → node appears as a circle at that position
2. **Add Edges** — Select **"🔗 ADD EDGE"** mode → click Node A (it gets highlighted with an amber ring) → click Node B → a dialog prompts for the travel cost → a bidirectional (undirected) edge is created with the cost displayed at the midpoint
3. **Set Start** — Select **"📍 SET START"** mode → click a node → it turns green
4. **Set Goal** — Select **"🎯 SET GOAL"** mode → click a node → it turns red
5. **Run A\*** — Click **▶ RUN A\*** → optimal path is highlighted in amber with directional arrows → results panel fills with step-by-step cost table
6. **Delete** — Select **"🗑️ DELETE"** mode → click any node to remove it and all its edges

**Edge creation tip:** If you accidentally start an edge from the wrong node, **right-click** anywhere on the canvas to cancel edge creation.

---

### Graph Controls Reference

| Control | Mode / Action |
|---------|---------------|
| ➕ ADD NODE | Click empty canvas area → enter city name in dialog → node placed at click position |
| 🔗 ADD EDGE | Click Node A → click Node B → enter cost → undirected edge created |
| 📍 SET START | Click a node → marks it as the source (turns green) |
| 🎯 SET GOAL | Click a node → marks it as the destination (turns red) |
| 🗑️ DELETE | Click a node → removes node and all connected edges; clears start/goal if applicable |
| ▶ RUN A\* | Runs A\* between start and goal → renders path + cost table |
| 📋 SAMPLE | Loads the full assignment PDF graph (nodes A–H, 10 edges) |
| 🔄 CLEAR | Removes all nodes and edges, resets everything |
| Right-click | Cancels in-progress edge creation |

---

### Sample Graph (Assignment PDF)

Clicking **📋 SAMPLE** loads this graph:

**Nodes:** A, B, C, D, E, F, G, H

**Edges:**
```
A — B  (cost: 1)     A — C  (cost: 4)
B — D  (cost: 2)     B — E  (cost: 5)
B — G  (cost: 6)     C — D  (cost: 1)
D — F  (cost: 3)     E — F  (cost: 1)
G — H  (cost: 2)     H — F  (cost: 4)
```

**Default:** Start = `A`, Goal = `F`

**Expected A\* Output:**
```
Optimal Path:  A → B → D → F
Total Cost:    6.00

Step-by-step:
  Node    g(n)    h(n)    f(n)
  A       0.00    ~460    460.00
  B       1.00    ~330    331.00
  D       3.00    ~180    183.00
  F       6.00    0.00    6.00

Nodes Explored: 5
```

The path `A→B→D→F` with cost 6 is optimal. The alternative `A→C→D→F` costs 8, and `A→B→E→F` costs 7.

---

### Results Displayed

After running A\*, the right panel shows:

- ✔ OPTIMAL PATH FOUND / ✘ NO PATH EXISTS
- Start and Goal node names
- Total Cost (sum of edge weights on the path)
- Path Length (number of nodes)
- Nodes Explored (how many nodes A\* opened before reaching goal)
- Execution Time (milliseconds, 4 decimal precision)
- **Optimal Path** as node sequence with `→` arrows
- **Step-by-step cost table** for every node on the path:

| NODE | g(n) | h(n) | f(n) |
|------|------|------|------|
| A | 0.00 | 460.00 | 460.00 |
| B | 1.00 | 330.00 | 331.00 |
| D | 3.00 | 180.00 | 183.00 |
| F | 6.00 | 0.00 | 6.00 |

- **Exploration Order** — the sequence in which A\* visited nodes (shows the algorithm's decision-making)

On the canvas, after running:
- **Explored nodes** → green tinted fill
- **Path nodes** → solid cyan fill
- **Optimal edges** → thick amber line with directional arrows
- **g(n) and f(n) scores** → displayed below each node as small labels

---

### Code Architecture — P11

```
P11 Module (IIFE)
├── State
│   ├── nodes { name: [x, y] }        — node positions (canvas px)
│   ├── adj   { name: { nb: cost } }  — adjacency list (undirected)
│   ├── startNode, goalNode           — selected node names or null
│   ├── edgeFrom                      — first node in edge-add flow
│   └── resPath, resExplored,         — last A* result
│       resGScore, resFScore
│
├── Rendering
│   ├── draw()                 — full canvas redraw (bg → edges → arrows → nodes)
│   ├── drawEdges()            — draws all edges with cost labels
│   └── drawNodes()            — draws all node circles with labels + scores
│
├── Interaction
│   ├── onClick(e) [async]     — handles all 5 modes via showModal() await
│   ├── nodeAt(x, y)           — hit-test: returns node name at pixel coords
│   └── invalidate()           — clears results + redraws
│
├── Algorithm
│   └── astar()                — returns { path, explored, cost, gScore, fScore, time }
│
└── Output
    ├── renderResults(result)  — builds result HTML including cost table
    └── setStats(result)       — updates stat cards below canvas
```

---

## 🏗 UI Architecture

The page is divided into three logical layers:

```
┌─────────────────────────────────────────────────────────┐
│  LAYER 3: Fixed Background (z-index: 0)                 │
│  • Animated particle canvas (bg-canvas)                 │
│  • Dot-grid pattern                                     │
│  • CSS scanlines overlay (z-index: 9999)                │
├─────────────────────────────────────────────────────────┤
│  LAYER 2: App Content (z-index: 1)                      │
│  ┌──────────────────────────────────────────────────┐   │
│  │ HEADER — Title + Student ID badge                │   │
│  ├──────────────────────────────────────────────────┤   │
│  │ TAB NAV — Problem 9 | Problem 11                 │   │
│  ├──────────────────────────────────────────────────┤   │
│  │ CONTROL BAR — mode radios + action buttons       │   │
│  ├─────────────────────────┬────────────────────────┤   │
│  │ CANVAS (600×600 / 700×520)│ RESULTS PANEL        │   │
│  │ Interactive drawing     │ Algorithm output       │   │
│  ├─────────────────────────┴────────────────────────┤   │
│  │ STATUS BAR — live feedback text                  │   │
│  ├──────────────────────────────────────────────────┤   │
│  │ STAT CARDS — 4 dynamic metric cards              │   │
│  ├──────────────────────────────────────────────────┤   │
│  │ LEGEND — color swatch reference                  │   │
│  ├──────────────────────────────────────────────────┤   │
│  │ ALGO CARDS — 3 theory reference cards            │   │
│  └──────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────┤
│  LAYER 3: Modal Overlay (z-index: 8000)                 │
│  • Used for P11 node name / edge cost input             │
│  • Promise-based async flow                             │
└─────────────────────────────────────────────────────────┘
```

**Tab switching** is done by toggling `.active` classes — CSS `display: none` / `display: block` with a `fadeIn` keyframe animation (0.3s ease, translateY 6px → 0).

**Responsive layout:** The workspace uses CSS Grid (`grid-template-columns: auto 1fr`) which stacks vertically on viewports below 900px (`@media (max-width: 900px)`).

---

## 🎨 Color System & Theming

All colors are defined as CSS custom properties in `:root`:

```css
:root {
  --bg:        #030b18;   /* Deep space background */
  --bg2:       #050e1e;   /* Slightly lighter bg */
  --panel:     #071528;   /* Panel/card background */
  --border:    rgba(0, 212, 255, 0.12);  /* Subtle cyan border */
  --border2:   rgba(0, 212, 255, 0.25);  /* Stronger cyan border */
  --cyan:      #00d4ff;   /* BFS — primary color */
  --purple:    #c084fc;   /* DFS — primary color */
  --amber:     #f59e0b;   /* A* — primary color */
  --green:     #10b981;   /* Start nodes, success states */
  --red:       #ef4444;   /* Goal nodes, error states */
  --text:      #c8d8f0;   /* Primary body text */
  --text-dim:  #6080a0;   /* Secondary/muted text */
}
```

**Algorithm → Color mapping (consistent everywhere):**

| Algorithm | Color | Hex | Applied To |
|-----------|-------|-----|-----------|
| BFS | Cyan | `#00d4ff` | Button, path cells, stat cards, result text |
| DFS | Purple | `#c084fc` | Button, path cells, stat cards, result text |
| A\* | Amber | `#f59e0b` | Button, path edges, stat cards, result text |
| Start | Green | `#10b981` | Start cell (S), start node circle |
| Goal | Red | `#ef4444` | Goal cell (G), goal node circle |
| Shared Path | Amber | `#f59e0b` | Cells on both BFS and DFS paths |

---

## 🚀 Deployment Guide (Vercel)

### Option A — Drag & Drop (Easiest)

1. Go to [vercel.com](https://vercel.com) → sign in
2. Click **"Add New Project"**
3. Choose **"Deploy without Git repository"**
4. Drag the folder containing `index.html` + `vercel.json` onto the upload zone
5. Click **Deploy** → live in ~30 seconds

### Option B — Via GitHub (Recommended for ongoing updates)

1. Push this repository to GitHub
2. Go to [vercel.com](https://vercel.com) → **"Add New Project"** → **"Import Git Repository"**
3. Select your repo → Vercel auto-detects static site (no framework, no build command)
4. Click **Deploy**
5. Every `git push` to `main` will auto-redeploy

### `vercel.json` explained:

```json
{
  "version": 2,
  "builds": [
    {
      "src": "index.html",
      "use": "@vercel/static"   // tells Vercel: serve this as static HTML, no build needed
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"     // all routes → index.html (single-page app behavior)
    }
  ]
}
```

---

## 💻 Running Locally

No installation required. Just open the file:

```bash
# Option 1: Direct browser open
open index.html           # macOS
start index.html          # Windows
xdg-open index.html       # Linux

# Option 2: Local HTTP server (avoids any browser security restrictions)
python3 -m http.server 8080
# Then open http://localhost:8080 in your browser

# Option 3: VS Code Live Server extension
# Right-click index.html → "Open with Live Server"
```

---

## 🐛 Bug Fixed

During development, **Problem 11 was completely non-interactive** (clicking the canvas did nothing, no nodes could be added, no drawing appeared).

**Root Cause:** The `P11` JavaScript module was structured as an IIFE that defined an `init()` function but only *exposed* it in the return object — it never *called* it. Without `init()` being invoked, the canvas element never received its event listener (`mousedown`), and the initial `draw()` call never ran.

```javascript
// ❌ BROKEN — init() exposed but never called
const P11 = (() => {
  function init() { /* setup canvas, event listeners, draw */ }
  return { init, draw, runAstar, loadSample, clear };  // init sits unused
})();

// ✅ FIXED — init() called inside the IIFE before return
const P11 = (() => {
  function init() { /* setup canvas, event listeners, draw */ }
  init();  // ← This one line was the entire fix
  return { draw, runAstar, loadSample, clear };
})();
```

Problem 9 had `init()` correctly called inside its IIFE at line 1434. Problem 11 missed this.

---

## ⚠ Known Limitations

- **Mobile / Touch:** The canvas interaction is mouse-only (`mousedown`, `mousemove`). Touch events on mobile are not fully supported. The UI is readable on mobile but grid/graph editing works best on desktop.
- **A\* Priority Queue:** The open set uses `Array.sort()` on each iteration — O(n log n) per step. For large graphs a proper binary min-heap (e.g., `@datastructures-js/priority-queue`) would be faster, but for assignment-scale graphs (under 50 nodes) this is imperceptible.
- **Graph Persistence:** There is no save/load functionality. Refreshing the browser clears all custom nodes and edges. Use **📋 SAMPLE** to reload the assignment graph.
- **Edge Deletion:** Individual edges cannot be deleted — only nodes (which removes all their edges). To remove a single edge, delete both endpoint nodes and re-add them.
- **No Diagonal Movement (P9):** The drone grid uses 4-directional movement only (up/down/left/right). Diagonal movement is not implemented.

---

## 📄 License

This project was created as an academic assignment submission. All code is original.

---

*Built with vanilla HTML, CSS, and JavaScript. No frameworks. No libraries. No build step.*  
*Student: RA2411026050331 — Artificial Intelligence Course*
