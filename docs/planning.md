# Emergency Evacuation Load Simulator — Planning Document

## 1. Project Motivation
### 1.1 Why Evacuation Modeling Matters
Evaluating strategy consequences in evacuation simulations for educated decision analysis

### 1.2 Why Abstraction is Acceptable
We are crating a comparative system, so we wish to cut out portions of realism to reduce noise in our strategy results. Reducing realism lowers ambiguity and noise inside our program which would make results unreliable. We wish to have our simulation be only effected by our desired constraints which can only be manipulated by our strategy inputs
### 1.3 Core Question Being Explored
What will be the results, including time, congestion, completion, bottlenecks, of our evacuation strategy based on this scenario

### 1.4 Success Criteria 
Can two strategies be compared meaningfully, can an outcome be explained in data, and can failures be traced back to specific constraints/inputs 

## 2. Assumptions
Explicitly state:

### What is simplified
- All agents follow the same movement and decision rules 
- All agents have access to the same information at each decision step 
### What is ignored
- This model does not represent human panic or irrational behavior
- This model will assume there is no communication loss or communication delay
### Why those omissions are acceptable
Including these factors would make it difficult to attribute differences in outcomes to evacuation strategy choices rather than uncontrolled behavioral variation.

## 3. System Model
### 3.1 Entities
#### Nodes
- origin type node
  - Population starts here 
  - Population decreases over time

- transit type node
  - No population starts here
  - No population finishes here
  - Connects paths together
  - Often where congestion will form 
- destination type node 
  - Population will end here
  - May have population intake limits
  - Represents completion/safety

#### Edges
- Path
  - Where population travels in between nodes 
  - May have population intake limits
  - Often where congestion will form 


### 3.2 State Variables
#### Per-node state
- origin type node
  - current population count
- transit type node
  - current population count
- destination type node 
  - current population count
#### Per-edge state
- Path 
  - current population on path
#### Global state
- Time
  - Time elapsed
- Population
  - Total in origin
  - Total in transit
  - Total in destination
- Flags
  - simulation complete
  - deadline exceeded
  - failure condition reached
- counters 

### 3.3 Time Model
#### Time step definition
clean snapshots
#### Decision cadence
static - for v1 
#### Termination conditions
- completion based termination (how long does evacuation take)
- deadline based termination (what happens if time is limited)
- feasability/failure determination (is evacuation possible under these constraints )

### 3.4 Population Semantics
#### Units of flow
- population is modeled as individual units 
- units are independent
- units can be distributed across multiple paths
#### Queues and waiting
- when waiting, individuals stay at current location
- waiting occurs due to capacity constraints
- congestion emerges from accumulation of waiting individuals 
#### Arrival and completion
- arrival at destination point will mark completion
- completed individuals no longer participate in movement 
- remain counted for metrics 

### 3.5 Invariants & Constraints
#### Capacity enforcement
- Capacity applies to paths, transit, and destination nodes
- If attempted flow reaches capacity, the excess stops and either waits or needs to choose another decision/direction
- The system supports per-node intake limits, and enforces them consistently.

#### Conservation rules
- people cannot be created or destroyed
- person cannot be in two places at once
- removed from system includes arrival/completion
- 
#### Validity rules
- Population count must not be negative, violation results in simulation error 
- edges must connect to valid nodes, invalid configs are rejected at load time
- simulation must terminate after x number of steps (optional)
- All runs must output a validation report of passed/failed invariants

## 4. Decision Strategies
- What information it sees 
- What decisions it makes 
- What it cannot override

## 5. Metrics
- Define each metric in plain language 
- Explain why it matters 
- Define success vs failure

## 6. Scenarios
### Scenario A — Simple Two-Route Baseline
#### 6.1 Scenario Description

This scenario represents a typical evacuation layout with multiple origin nodes connected to destination nodes via more than one viable route. The topology is intentionally uncomplicated and provides clear alternatives so that route choice can matter without introducing extreme bottlenecks.

Origins feed into a small set of transit nodes that connect to two destination nodes. The arrangement is conceptually balanced: no single origin is structurally isolated, and there is more than one reasonable path to reach safety.

This scenario exists to serve as a stable baseline for comparing strategies in a network where the system has flexibility and evacuation should be broadly feasible.

#### 6.2 Intended Stress Conditions

This scenario introduces mild, natural congestion pressure where flows merge at transit nodes and shared paths. However, the stress is not intended to be dominated by a single chokepoint. Instead, it tests whether the simulator can handle normal merging and queueing behavior while still allowing flow to redistribute.

Congestion is expected to be localized and intermittent rather than persistent or system-wide.

#### 6.3 Expected Qualitative Outcomes

If the model is functioning correctly, some queueing should appear at shared transit nodes or shared paths, but the system should continue to make progress as evacuees redistribute across available routes. Different strategies should exhibit observable differences in how they split flow across routes and how quickly they react to emerging congestion.

Evacuation is expected to complete without persistent stalling. A surprising outcome would be indefinite gridlock in a network that has multiple viable routes.

#### 6.4 Scenario Classification

Baseline scenario — intended as a comparison reference under a flexible, broadly feasible topology.

### Scenario B — Balanced Merge Baseline (Converging Neighborhoods)
#### 6.1 Scenario Description

This scenario represents several separate origin areas evacuating into a shared arterial network before reaching a destination. It models a common “neighborhoods into arterials into shelters” structure where merging flow is the main feature.

Multiple origin nodes connect into separate feeder paths that converge into a central transit hub (or small set of hubs). From the hub, routes continue to one or more destination nodes. The scenario is distinct from Scenario A because convergence is more central and unavoidable, even though the overall network remains feasible.

This scenario exists to test merging behavior and the simulator’s handling of queue propagation through shared nodes and edges.

#### 6.2 Intended Stress Conditions

The stress is primarily localized at the convergence point(s) where multiple upstream feeders meet. Queues are expected to form due to competing inflow from several origins attempting to enter the same downstream capacity.

This is not intended to be a hard failure case; it is intended to create clear congestion patterns that reveal whether strategies coordinate merges or unintentionally starve certain origins.

#### 6.3 Expected Qualitative Outcomes

If the model is working correctly, congestion should consistently form at the merge location, with backpressure visible upstream on feeder paths. Strategies should diverge in observable ways: some may balance inflow across feeders, while others may produce uneven waiting times for certain origin areas.

Evacuation should still progress, but delays should be concentrated around the merge and may ripple upstream. A surprising outcome would be congestion forming far from the merge without a structural reason.

#### 6.4 Scenario Classification

Baseline scenario — intended for comparison, but with a clear and interpretable congestion structure.

### Scenario C — Asymmetric Intake Stress (Bridge to Limited Shelter)
#### 6.1 Scenario Description

This scenario represents a constrained-flow evacuation where most evacuees must pass through a narrow access segment (conceptually a bridge, tunnel, gate, or checkpoint) and then enter a destination with restricted intake. The layout is intentionally asymmetric: the upstream network can supply evacuees faster than the downstream system can accept them.

One or more origin nodes feed into a small number of transit nodes that funnel into a single critical path segment. Downstream, evacuees reach one primary destination node that enforces a strict intake limit. Optionally, a secondary destination exists but is less accessible or requires a longer route, creating a meaningful tradeoff in routing without making the scenario impossible.

This scenario exists in the test suite to expose limit behavior under stacked constraints (a chokepoint plus restricted destination intake).

#### 6.2 Intended Stress Conditions

Congestion is expected to form at two layers:

At the constrained access segment (the unavoidable chokepoint).

At the destination intake boundary, where acceptance is slower than arrivals.

This stress is partly localized (at the chokepoint and destination) and partly systemic because backpressure can propagate upstream into transit nodes and even origins, causing widespread waiting.

#### 6.3 Expected Qualitative Outcomes

If the model is functioning correctly, queues should form persistently near the constrained access segment and/or near the destination, with backpressure waves moving upstream. Strategies should diverge in how they respond: some may attempt rerouting to the secondary destination or distribute flow to reduce buildup, while others may continue feeding the chokepoint and amplify upstream waiting.

Evacuation may complete slowly or may partially stall if upstream continues to push flow into saturated constraints. A surprising outcome would be smooth flow into the destination despite restricted intake, or disappearance of congestion without structural relief.

#### 6.4 Scenario Classification

Stress scenario — intended to expose system limits and show strategy differences under compounded bottlenecks.

## 7. Reproducibility Rules
- All simulations must be deterministic by default
- If randomness is introduced (e.g., tie-breaking), it must be seeded
- Given the same scenario config and strategy config, outputs must be identical
- Each run is identified by a run ID derived from inputs
- Outputs are stored without mutation

## 8. Risks & Limitations
- Where the model could mislead 
- What would be needed for realism 
- Where results should not be over-interpreted


