# MOSAIC

## Mission-Oriented Self-Organization through Auctions, Incentives, and Coalitions

**Version:** 0.4.0  
**Release date:** 2026-07-30  
**NetLogo version:** 7.0.4

**Authors**

- Katherin Molina — Independent Researcher, Australia
- Lorena Holguin — Software Engineer, Kahlo Group

**Copyright holders:** Katherin Molina and Lorena Holguin  
**Model and code licence:** Apache License 2.0  
**Documentation and data licence:** Creative Commons Attribution 4.0 International

---

## WHAT IS IT?

MOSAIC is an agent-based model of decentralized mission coordination among
heterogeneous robots operating in a spatially complex, partially observable,
and communication-constrained environment.

The model examines whether local incentives, auctions, communication,
coalition formation, reputation, and adaptive strategies can transform
decentralized competition into coherent mission-level coordination without a
central planner.

Robots must discover tasks, exchange incomplete information, evaluate local
opportunities, compete for individual contracts, form temporary coalitions for
cooperative tasks, travel, synchronize, execute work, and manage limited
energy. Some assignments fail and may be released for reassignment.

MOSAIC separates mission outcomes from coordination processes. A run may
complete many tasks while still producing excessive bidding, delayed coalition
formation, unequal rewards, or high coordination overhead.

---

## RESEARCH QUESTION

> Under what conditions can incentive mechanisms transform decentralized
> competition among heterogeneous robots into self-organized mission
> coordination without central supervision?

---

## CORE MODEL ELEMENTS

### Environment

The world is a synthetic 51 × 51 toroidal patch grid. Patches vary in:

- terrain cost;
- hazard level;
- communication quality;
- visibility;
- explored status.

The environment remains static during a run.

### Robots

Robots differ in:

- sensing capability;
- manipulation capability;
- transport capability;
- communication capability;
- speed;
- initial energy;
- risk tolerance;
- reputation;
- bidding strategy.

A robot can hold no more than one active contract.

### Tasks

Tasks differ in:

- location;
- reward;
- deadline;
- service duration;
- risk;
- capability requirements;
- individual or cooperative execution.

Tasks begin undiscovered and invisible. Cooperative tasks require a temporary
team whose aggregate capabilities satisfy all requirements.

### Communication

Communication links are undirected, temporary, and reconstructed every tick.
Link formation depends on distance, local environmental quality, robot
communication capability, a quality threshold, and stochastic failure.

Task knowledge is exchanged synchronously across active links. Information
travels at most one network edge per tick and is not forgotten.

### Auctions and coalitions

Each discovered, active, and unassigned task opens a local auction. A free
robot evaluates only tasks in its own knowledge and submits at most one
positive-utility bid per tick.

Individual tasks select the highest bid. Cooperative tasks use a bounded greedy
heuristic to assemble a capability-feasible coalition.

No procedure computes a global mission plan.

### Rewards, reputation, and strategies

The model includes four reward regimes:

- `leader-priority`;
- `equal-share`;
- `contribution-based`;
- `mission-aligned`.

Successful participation increases reputation. Failed or released contracts
reduce it. When adaptation is enabled, robots update learned values for
`competitive`, `cooperative`, and `balanced` strategies.

---

## PROCESS SCHEDULE

Each tick performs the following sequence:

1. Evaluate task deadlines.
2. Move active robots.
3. Detect nearby tasks.
4. Reconstruct the communication network.
5. Share task knowledge.
6. Open auctions.
7. Submit robot bids.
8. Resolve individual assignments and cooperative coalitions.
9. Advance travel, waiting, synchronization, and execution.
10. Release invalid or delayed contracts.
11. Update reputation and adaptive strategies.
12. Refresh the display and advance the tick.

The model stops when:

- `mission-time-limit` is reached;
- all robots are depleted; or
- no active task remains.

---

## HOW TO USE IT

### Quick start

1. Set `random-seed-value` to `42`.
2. Press `SETUP`.
3. Press `GO`.
4. Allow the mission to stop automatically.
5. Press `RUN CHECKS`.
6. Confirm that the Command Center reports:

```text
OVERALL RESULT: PASS
```

Use `STEP` to execute one tick at a time.

### Validated manual baseline

```text
number-of-robots = 15
robot-initial-energy = 150
energy-heterogeneity = 0.30
capability-heterogeneity = 0.50

number-of-tasks = 30
mission-time-limit = 500
movement-cost-rate = 0.10

communication-radius = 8
communication-quality-threshold = 0.25
communication-failure-rate = 0.05

coalition-task-proportion = 0.40
maximum-coalition-size = 3

reward-regime = "equal-share"
reputation-enabled? = true
adaptive-strategies? = true
initial-strategy = "mixed"
```

Changing generation parameters after `SETUP` does not regenerate existing
patches, robots, or tasks. Mid-run parameter changes should be treated as
documented interventions rather than baseline replications.

---

## VISUAL LEGEND

### Patches

- Green: open environment
- Brown: difficult terrain
- Red: hazard zone
- Blue: communication-shadow zone

### Robots

- Blue shades: free or exploring; shade varies with remaining energy
- Orange: travelling to an assigned task
- Yellow: waiting at an individual task
- Magenta: waiting for coalition members
- Violet: executing
- Gray: depleted

### Tasks

- Hidden: undiscovered
- Yellow: discovered individual task, open for bidding
- Violet: discovered cooperative task, open for coalition bidding
- Orange: assigned individual task
- Magenta: assigned cooperative task
- Green: completed
- Red: discovered and failed

Optional task labels use:

- `T`: individual task;
- `M`: open multi-robot task;
- `A`: assigned individual task;
- `C`: assigned cooperative task;
- `X`: failed task.

Communication links are visible only when `show-network?` is enabled.

---

## PRINCIPAL OUTPUTS

### Mission outcomes

- completed and failed tasks;
- completion rate;
- explored area;
- task value;
- energy used and remaining.

### Coordination outcomes

- total bids;
- unresolved auction rounds;
- contracts awarded;
- coalition attempts;
- coalitions formed;
- coalition formation efficiency;
- coalition success rate;
- contract releases;
- coordination overhead;
- mission efficiency.

### Information and network outcomes

- current links;
- mean network degree;
- isolated robots;
- information transmissions;
- unique acquisitions;
- information coverage.

### Distribution and adaptation

- Reward Gini;
- Contract Gini;
- top-reward share;
- mean reputation;
- strategy composition;
- strategy switches.

Coalition formation efficiency and coalition success measure different stages:

```text
Coalition Formation Efficiency =
coalitions formed / formation attempts
```

```text
Coalition Success Rate =
completed cooperative tasks / coalitions formed
```

---

## EXPERIMENTAL EVIDENCE

The official v0.4.0 programme contains seven BehaviorSpace experiments and 690
run-level records using 30 paired random seeds per condition.

The principal findings are:

1. Communication reach matters more than moderate temporary link failure.
2. Moderate capability heterogeneity outperforms both homogeneous and highly
   heterogeneous teams under the tested conditions.
3. High cooperative-task demand produces a nonlinear coalition-capacity
   threshold.
4. Leader-priority rewards increase inequality and auction friction without a
   completion advantage.
5. Reputation and adaptation do not produce robust baseline completion gains,
   although their interaction can reduce coalition delay.
6. Stronger mission incentives increase payments more than mission
   performance.
7. The principal bottleneck is feasible partner discovery and coalition
   formation before deadlines.

These findings are conditional on the implemented mechanisms and tested
parameter ranges.

---

## AUTOMATED VERIFICATION

`RUN CHECKS` evaluates nine invariants:

1. Task State Consistency
2. Contract Consistency
3. Energy Accounting
4. Reward Accounting
5. Coalition Membership
6. Failure Traceability
7. Knowledge Integrity
8. Reputation Bounds
9. Strategy Validity

A passing result demonstrates internal consistency for the tested state. It
does not establish empirical validity, field readiness, or physical safety.

---

## DOCUMENTATION

The repository contains:

- `documentation/MOSAIC_ODD_Protocol_v0.4.0.md`
- `documentation/MOSAIC_User_Manual_v0.4.0.md`
- `documentation/MOSAIC_Experimental_Design_and_Results_v0.4.0.md`
- `experiments/Data_Dictionary.md`
- `experiments/Validation_Report.md`
- `analysis/MOSAIC_BS01_BS07_Integrated_Analysis.xlsx`
- `report/MOSAIC_Technical_Report_v0.4.0.pdf`

The ODD Protocol is the formal model specification. The User Manual contains
complete operational instructions. The Experimental Design and Results
document contains the statistical evidence from BS01–BS07. The Technical
Report provides the publication-oriented synthesis of the model and results.

---

## PROJECT ORIGIN AND INSTITUTIONAL CONTEXT

MOSAIC was initially developed by Katherin Molina as a final project for the
*Introduction to Agent-Based Modeling* course offered through Complexity
Explorer.

The model was subsequently extended and further developed in collaboration
with Lorena Holguin, Software Engineer at Kahlo Group.

This is an independent project by the authors. Complexity Explorer and the
Santa Fe Institute provided the academic context for the initial course project
but do not own, sponsor, validate, or endorse the model, its results, or its
conclusions.

Lorena Holguin's affiliation with Kahlo Group is provided for identification
purposes only. Kahlo Group did not fund, sponsor, supervise, validate, or
endorse this project.

---

## LIMITATIONS

MOSAIC is a research simulation, not a deployable robot controller.

The model does not include:

- low-level vehicle dynamics;
- collision avoidance;
- physical interference;
- detailed bandwidth or latency;
- dynamic task arrival;
- strategic defection;
- adversarial behaviour;
- human intervention;
- empirical calibration to a physical mission.

The world, capabilities, costs, rewards, and timing are synthetic and
dimensionless. Results must not be interpreted as field predictions or safety
certification.

---

## CITATION AND REUSE

Provisional citation:

```text
Molina, K., & Holguin, L. (2026). MOSAIC: Mission-Oriented
Self-Organization through Auctions, Incentives, and Coalitions
(Version 0.4.0) [Agent-based model and experimental dataset].
```

The NetLogo model and source code are licensed under Apache-2.0. The original
documentation, figures, datasets, and analyses are licensed under
CC-BY-4.0. Reuse must preserve attribution to Katherin Molina and Lorena
Holguin.

The v0.4.0 release date is 2026-07-30. The public repository URL and DOI
will be added during publication. Machine-readable citation metadata are provided in
`CITATION.cff`.
