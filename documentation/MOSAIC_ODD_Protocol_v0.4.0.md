# MOSAIC ODD Protocol

## Mission-Oriented Self-Organization through Auctions, Incentives, and Coalitions

**Version:** 0.4.0  
**Model authors:** Katherin Molina and Lorena Holguin  
**Copyright holders:** Katherin Molina and Lorena Holguin  
**Code licence:** Apache License 2.0  
**Document licence:** Creative Commons Attribution 4.0 International  
**Implementation:** NetLogo 7.0.4  
**Model file:** `MOSAIC_v0.4.0.nlogox`  
**Document status:** Version 0.4.0 protocol derived from the frozen model source  
**Protocol structure:** Overview, Design Concepts, and Details (ODD)

---

## Document purpose

This document provides a complete ODD description of MOSAIC v0.4.0. It is
intended to support inspection, replication, extension, and critical review of
the model independently of the NetLogo Interface and Info tab.

The NetLogo source code is the authoritative implementation. This protocol
describes the source as frozen in `MOSAIC_v0.4.0.nlogox`. Experimental results
are documented separately and are not used to redefine the model mechanisms.

---

# 1. Overview

## 1.1 Purpose and patterns

### 1.1.1 Purpose

MOSAIC is an agent-based model of decentralized mission coordination among
heterogeneous robots operating in a spatially complex and partially observable
environment.

The model investigates the following central question:

> Under what conditions can incentive mechanisms transform decentralized
> competition among heterogeneous robots into self-organized mission
> coordination without central supervision?

MOSAIC represents a mission in which robots must discover, evaluate, bid for,
travel to, and execute spatially distributed tasks. Some tasks can be completed
by one robot. Other tasks require a coalition whose aggregate capabilities meet
multiple simultaneous requirements. Robots have incomplete local knowledge and
can obtain new task information only through local sensing or temporary
communication links.

The observer controls initialization, scheduling, and metric collection. It
does not construct a global task plan, select winning robots centrally, or
design coalitions through an external optimizer. Allocation emerges from robot
bids and task-level auction resolution.

The model is intended as a computational laboratory for comparing decentralized
coordination mechanisms. It is not a deployable controller for physical robots
and is not empirically calibrated to a specific platform or mission site.

### 1.1.2 Intended patterns

The model is designed to generate and compare system-level patterns in:

- mission completion and task failure;
- partial discovery and retained distributed knowledge;
- communication coverage and network isolation;
- competition for individual contracts;
- capability complementarity in cooperative tasks;
- coalition formation attempts, formation delay, release, and success;
- reward and contract inequality;
- reputation differentiation;
- strategy diversity and strategy switching;
- coordination overhead and mission coordination efficiency.

The primary qualitative patterns of interest are:

1. Communication reach should affect how quickly task knowledge spreads and
   how many feasible partners become locally accessible.
2. Capability heterogeneity should create both complementarity and matching
   complexity.
3. Cooperative-task demand should increase coordination requirements and may
   create a capacity threshold.
4. Reward regimes should affect distributional outcomes even when completion
   remains similar.
5. Reputation and adaptation should create path-dependent differences only
   when the mission provides sufficient informative experience.
6. High coordination activity may coexist with high mission completion,
   demonstrating that outcome success and process efficiency are distinct.

These are model expectations and analysis targets, not empirical validation
claims.

---

## 1.2 Entities, state variables, and scales

### 1.2.1 Spatial environment

The world is a 51 × 51 toroidal patch grid:

- `pxcor`: −25 to 25;
- `pycor`: −25 to 25;
- horizontal wrapping: enabled;
- vertical wrapping: enabled;
- total patches: 2,601.

Robots occupy continuous positions within this world. Distances are measured in
NetLogo patch units.

Each patch stores:

| Variable | Meaning |
|---|---|
| `terrain-cost` | Multiplicative contribution to movement energy cost. |
| `hazard-level` | Local hazard intensity in [0, 1]. |
| `communication-quality` | Environmental contribution to link quality. |
| `visibility-level` | Environmental contribution to task-detection probability. |
| `explored?` | Whether any robot has occupied the patch. |
| `patch-zone` | Visual category: `open`, `difficult-terrain`, `hazard`, or `communication-shadow`. |

The environment is static after setup.

### 1.2.2 Robot agents

Robots are mobile turtle agents of breed `robots`. Each robot has a unique
`robot-id`, assigned from its NetLogo `who` number.

#### Capabilities and resources

| Variable | Meaning |
|---|---|
| `sensing-capability` | Ability to detect tasks and contribute to sensing requirements. |
| `manipulation-capability` | Contribution to manipulation requirements. |
| `transport-capability` | Contribution to transport requirements. |
| `communication-capability` | Contribution to link quality and communication requirements. |
| `speed` | Maximum movement distance per tick. |
| `energy` | Current remaining energy. |
| `starting-energy` | Energy assigned during setup. |
| `risk-tolerance` | Maximum preferred hazard level during exploration. |

Capabilities are bounded to [0.10, 1.00]. Speed is bounded to [0.30, 1.20].
Risk tolerance is bounded to [0, 1].

#### Operational state

| Variable | Meaning |
|---|---|
| `state` | Current behavior: `exploring`, `travelling`, `waiting`, `waiting-for-coalition`, `executing`, or `depleted`. |
| `known-task-ids` | Persistent list of task identifiers known to the robot. |
| `next-known-task-ids` | Temporary list used for synchronous communication updates. |
| `target-task-id` | Current assigned task, or −1 when free. |
| `current-bid` | Most recent submitted bid utility. |
| `current-contribution` | Capability contribution associated with the most recent bid. |

#### Economic, reliability, and learning state

| Variable | Meaning |
|---|---|
| `credits` | Cumulative reward received. |
| `last-payoff` | Payment received from the most recent successful task. |
| `reputation` | Scalar reliability score in [0, 1], initialized at 0.50. |
| `contract-successes` | Number of successful contract participations. |
| `contract-failures` | Number of failed or released contract participations. |
| `contract-release-events` | Number of releases experienced by the robot. |
| `strategy` | `competitive`, `cooperative`, or `balanced`. |
| `competitive-value` | Learned value of the competitive strategy. |
| `cooperative-value` | Learned value of the cooperative strategy. |
| `balanced-value` | Learned value of the balanced strategy. |
| `strategy-period-payoff` | Reward minus energy expenditure since the last strategy update. |
| `strategy-switches` | Robot-level strategy changes. |
| `last-strategy-update` | Tick of the last strategy update. |

#### Performance history

| Variable | Meaning |
|---|---|
| `contracts-won` | Contracts awarded to the robot. |
| `contracts-lost` | Submitted bids that did not result in membership. |
| `competition-events` | Participation in contested auction rounds. |
| `cooperation-events` | Cooperative contracts joined. |
| `tasks-completed` | Successful task participations. |
| `distance-travelled` | Cumulative spatial distance moved. |
| `energy-used` | Cumulative movement and execution energy expenditure. |

A robot can hold at most one active contract.

### 1.2.3 Task agents

Tasks are stationary turtle agents of breed `tasks`. A task receives a unique
`task-id` from its NetLogo `who` number.

#### Task definition

| Variable | Meaning |
|---|---|
| `base-reward` | Base economic value paid after completion. |
| `deadline` | Tick at which the task expires if incomplete. |
| `service-duration` | Number of successful execution ticks required. |
| `risk-level` | Task-level risk in [0, 1]. |
| `cooperative?` | Whether a coalition is required. |
| `required-team-size` | Minimum number of assigned robots. |
| `required-sensing` | Required individual or aggregate sensing capability. |
| `required-manipulation` | Required individual or aggregate manipulation capability. |
| `required-transport` | Required individual or aggregate transport capability. |
| `required-communication` | Required individual or aggregate communication capability. |

#### Discovery and auction state

| Variable | Meaning |
|---|---|
| `discovered?` | Whether at least one robot has detected the task. |
| `discovered-at` | First discovery tick. |
| `discovered-by` | Identifier of the first detecting robot. |
| `auction-status` | `closed`, `open`, `assigned`, `completed`, or `failed`. |
| `bid-list` | List of `[robot-id, bid-utility, capability-contribution]` records. |
| `ever-received-bid?` | Whether the task received at least one bid. |
| `ever-assigned?` | Whether the task was assigned at least once. |

#### Assignment and execution state

| Variable | Meaning |
|---|---|
| `assigned-robot-id` | Lead robot identifier, or −1. |
| `assigned-robot-ids` | Current contract-member identifiers. |
| `winning-bid` | Winning bid or aggregate coalition bid. |
| `assignment-tick` | Tick of the current assignment. |
| `formation-delay` | Most recently recorded discovery-to-assignment delay. |
| `execution-start-tick` | First tick at which all current members were present. |
| `total-waiting-ticks` | Sum of arrived-member waiting time. |
| `work-progress` | Completed execution ticks under the current assignment. |

#### Completion and failure state

| Variable | Meaning |
|---|---|
| `completed?` | Whether the task completed successfully. |
| `completed-at` | Completion tick. |
| `completed-by-robot-ids` | Final successful participants. |
| `distributed-reward` | Total reward paid for the task. |
| `failed?` | Whether the deadline expired before completion. |
| `failure-reason` | Terminal failure category. |
| `last-release-reason` | Most recent pre-deadline contract-release reason. |
| `release-count` | Number of assignment releases. |

### 1.2.4 Communication links

`communication-links` are undirected and temporary. They are destroyed and
reconstructed every tick.

Each link stores:

| Variable | Meaning |
|---|---|
| `link-quality` | Product of mean environmental communication quality and mean robot communication capability. |

A link represents a current opportunity to exchange task identifiers. It does
not represent a persistent social relation, message queue, channel capacity, or
continuous radio connection.

### 1.2.5 Global state

| Variable | Meaning |
|---|---|
| `model-version` | Internal model-version string. |
| `information-transmissions` | Cumulative task-information items transmitted across links. |
| `unique-information-acquisitions` | Cumulative task identifiers newly added through communication. |
| `total-bids` | Cumulative bids processed by task auctions. |
| `contested-auction-rounds` | Auction rounds with more bids than available positions. |
| `unresolved-auction-rounds` | Cooperative auction rounds without a feasible coalition. |
| `total-contracts-awarded` | Task-assignment events. |
| `total-contract-memberships-awarded` | Robot memberships awarded across contracts. |
| `coalition-formation-attempts` | Cooperative auction-resolution attempts. |
| `coalitions-formed` | Feasible coalitions successfully assigned. |
| `coalition-release-events` | Releases involving cooperative contracts. |
| `coalition-tasks-completed` | Cooperative tasks completed. |
| `total-task-value` | Sum of base reward for completed tasks. |
| `total-rewards-distributed` | Actual reward payments made to robots. |
| `total-mission-bonuses` | Reward paid above base task value under mission-aligned incentives. |
| `total-contract-releases` | All individual and cooperative contract releases. |
| `strategy-switches-total` | Total strategy changes across robots. |
| `verification-last-result` | `NOT RUN`, `PASS`, or `FAIL`. |

### 1.2.6 Temporal and measurement scales

One tick represents one complete coordination and action cycle. It is not
mapped to a fixed number of real-world seconds.

The sequence within a tick matters. In particular:

- deadlines are checked before movement and execution;
- robot movement occurs before communication-network reconstruction;
- information sharing uses the newly reconstructed network;
- auctions use knowledge available after that tick's communication stage;
- task execution occurs after auction resolution;
- reputation decay and strategy updating occur after execution.

Energy, reward, capability, reputation, overhead, and efficiency values are
dimensionless model quantities. Absolute metric values should not be interpreted
as physical measurements unless an external calibration is introduced.

---

## 1.3 Process overview and scheduling

### 1.3.1 Setup schedule

`setup` performs the following sequence:

1. Clear all model state.
2. Apply `random-seed-value`.
3. Set `model-version` to `0.4.0`.
4. Reset all global counters.
5. Generate the synthetic environment.
6. Create heterogeneous robots.
7. Create heterogeneous tasks.
8. Construct the initial communication network.
9. Refresh robot appearance.
10. Refresh task appearance.
11. Reset the tick counter.

### 1.3.2 Tick schedule

Each call to `go` performs the following sequence:

1. Stop if `ticks >= mission-time-limit`.
2. Stop if no robot has positive energy.
3. Evaluate task deadlines and mark expired tasks as failed.
4. Stop if no active task remains.
5. Ask each active robot to:
   - explore when free; or
   - travel toward or wait at its current contract;
   - detect nearby tasks after movement.
6. Destroy and rebuild the communication network.
7. Share task information synchronously over active links.
8. Open auctions for discovered, active, unassigned tasks.
9. Ask each free active robot to submit at most one bid.
10. Resolve individual and cooperative auctions.
11. Advance assigned tasks:
    - validate membership;
    - check arrival synchronization;
    - execute when all members are present;
    - release contracts when required.
12. Apply reputation decay toward 0.50.
13. Update adaptive strategies when the configured interval is reached.
14. Refresh robot and task appearance.
15. Advance the tick counter.

The stopping conditions are therefore:

- mission time limit reached;
- all robots depleted; or
- no incomplete, non-failed task remains.

### 1.3.3 Within-agent ordering

NetLogo randomizes the order of agents inside each `ask` block unless an
explicit ordered list is used. MOSAIC therefore contains stochastic agent-order
effects in addition to explicitly sampled random events.

Knowledge transmission is handled differently: new task identifiers are
written to `next-known-task-ids` and committed only after all communication
links have been processed. This prevents multi-hop cascades within one tick.

---

# 2. Design concepts

## 2.1 Basic principles

MOSAIC combines mechanisms from:

- agent-based modelling;
- contract-net-style decentralized task allocation;
- distributed auctions;
- coalition formation;
- local information diffusion;
- reputation systems;
- reinforcement-style value updating;
- complex adaptive systems.

The model assumes bounded local knowledge. Robots know their own state and
capabilities, retain identifiers of discovered tasks, and can use globally
readable reputation values when reputation is enabled. They do not possess a
complete mission plan or complete task list at initialization.

Tasks act as local auctioneers. Robots decide whether and where to bid. The
observer sequences these processes but does not select winners directly.

## 2.2 Emergence

Mission-level coordination emerges from repeated local interactions:

- stochastic exploration;
- probabilistic task detection;
- temporary network formation;
- one-hop information exchange;
- utility-based bid selection;
- competition among bidders;
- greedy capability aggregation;
- synchronization at cooperative tasks;
- payment and reputation feedback;
- periodic strategy adaptation.

No procedure directly maximizes global completion, minimizes energy, equalizes
rewards, or minimizes coalition delay.

Emergent outputs include:

- uneven task knowledge;
- spatially dependent contract access;
- contract concentration;
- persistent strategy diversity;
- reward inequality;
- variable coalition delay;
- coordination congestion;
- mission success or failure.

## 2.3 Adaptation

Adaptation occurs at three levels.

### Movement adaptation

Free robots follow a stochastic correlated walk. When the proposed patch has a
hazard level above the robot's risk tolerance, the robot changes direction
before attempting movement.

### Reputation adaptation

Successful participation moves reputation upward. Contract failure or release
moves it downward. A small continuous decay pulls reputation toward 0.50.

### Strategy adaptation

Robots maintain learned values for competitive, cooperative, and balanced
strategies. Realized rewards increase period payoff; movement and execution
energy decrease it. At fixed intervals, each robot updates the value of its
current strategy and selects its next strategy through epsilon-greedy choice.

## 2.4 Objectives

A robot submits a bid only when the highest locally evaluated task has positive
expected utility.

For a candidate task, expected utility is:

```text
expected reward
+ urgency bonus
+ reputation adjustment
+ strategy adjustment
− estimated travel and execution energy
− risk penalty
```

The robot does not optimize over joint mission assignments. It evaluates only
tasks currently present in its local knowledge and submits at most one bid per
tick.

The task-level auction objective differs by task type:

- an individual task selects the highest bid;
- a cooperative task greedily builds a feasible capability coalition from
  ranked bid records.

## 2.5 Learning

Strategy-value learning uses an incremental update:

```text
new value =
old value
+ strategy-learning-rate × (period payoff − old value)
```

Only the value of the currently used strategy is updated. There is no explicit
state representation, discount factor, model-based planning, or counterfactual
update for strategies not selected.

The next strategy is:

- a random strategy with probability `strategy-exploration-rate`; or
- a random member of the set of strategies tied for highest learned value.

## 2.6 Prediction

Robots use simplified internal estimates of:

- Euclidean travel distance;
- travel time from current speed;
- service duration;
- remaining deadline time;
- movement energy;
- execution energy;
- reward share;
- risk exposure;
- capability contribution.

The travel-energy estimate uses the terrain and hazard values at the task patch,
rather than integrating conditions along the complete future route. Prediction
is therefore intentionally approximate.

For contribution-based rewards, a cooperative bidder estimates the other
members' mean contribution as 0.50. For leader-priority, a bidder expects the
discoverer to receive the leader share. Actual coalition leadership may differ
when the discoverer is not selected.

## 2.7 Sensing

The effective detection radius of a robot is:

```text
detection-radius × (0.50 + sensing-capability)
```

For an unknown active task inside this radius, detection probability is:

```text
bounded(sensing-capability × local visibility, 0.05, 1.00)
```

Detection is evaluated after movement. The first successful detector records
the discovery tick and discoverer identifier.

Undiscovered tasks are hidden, cannot receive bids, and may expire with the
failure reason `undiscovered`.

## 2.8 Interaction

### Direct interaction

Robots exchange task identifiers through temporary undirected communication
links.

### Indirect interaction

Robots interact indirectly through:

- competition for contracts;
- coalition membership;
- shared access to task information;
- energy depletion;
- reward allocation;
- reputation-sensitive bid utility;
- strategy-value feedback.

Tasks mediate auction interaction by collecting bid records and assigning
contracts.

## 2.9 Stochasticity

Stochasticity affects:

- initial patch attributes;
- robot placement;
- capability, speed, energy, and risk-tolerance sampling;
- task placement;
- task type;
- task reward, deadline, duration, risk, and requirements;
- exploration turns;
- task detection;
- communication failure;
- information transmission;
- individual-auction tie-breaking;
- mixed initial strategy;
- epsilon-greedy strategy exploration;
- NetLogo agent ordering.

`random-seed-value` supports exact reproducibility when the model version,
Interface parameters, and NetLogo version are held constant.

## 2.10 Collectives

The principal collective is a temporary coalition assigned to one cooperative
task.

A candidate coalition must:

1. include at least `required-team-size` robots;
2. contain no more than `maximum-coalition-size`;
3. meet aggregate sensing requirements;
4. meet aggregate manipulation requirements;
5. meet aggregate transport requirements;
6. meet aggregate communication requirements.

Coalitions are constructed greedily from bid records ranked by capability
contribution and then bid utility. The model does not enumerate all possible
combinations.

A coalition persists until:

- task completion;
- contract release;
- task deadline failure.

## 2.11 Observation

The model observes four broad categories.

### Mission outcomes

- completed and failed tasks;
- completion rate;
- discovered-task completion rate;
- explored area;
- task value;
- energy consumption.

### Coordination processes

- bids;
- contested and unresolved auction rounds;
- contracts and memberships;
- coalition attempts and formation;
- releases;
- formation delay;
- coordination overhead.

### Information and network structure

- active links;
- mean degree;
- density;
- isolated robots;
- information transmissions;
- unique acquisitions;
- information coverage;
- redundancy ratio.

### Distribution and adaptation

- reward Gini;
- contract Gini;
- top-reward share;
- reputation distribution;
- strategy composition;
- strategy switches;
- learned strategy values.

The model also exposes an automated nine-invariant verification procedure.

---

# 3. Details

## 3.1 Initialization

### 3.1.1 Environment generation

Each patch receives independent initial values:

```text
terrain-cost = 1 + terrain-heterogeneity × U(0, 2)
hazard-level = hazard-intensity × U(0, 1)
communication-quality = 1 − terrain-heterogeneity × U(0, 0.65)
visibility-level = 1 − terrain-heterogeneity × U(0, 0.55)
```

Four diffusion passes are then applied to all four fields with diffusion
coefficient 0.35. Values are bounded to:

- terrain cost: [0.50, 3.00];
- hazard: [0, 1];
- communication quality: [0.05, 1];
- visibility: [0.20, 1].

Visual zone classification uses the following priority:

1. `hazard` when hazard > 0.62;
2. `communication-shadow` when communication quality < 0.45;
3. `difficult-terrain` when terrain cost > 1.65;
4. `open` otherwise.

### 3.1.2 Robot generation

Robots are placed on patches with hazard < 0.75 when available.

Each capability is sampled as:

```text
bounded(0.50 + Normal(0, 0.22 × capability-heterogeneity), 0.10, 1.00)
```

Speed is sampled as:

```text
bounded(0.75 + Normal(0, 0.20 × capability-heterogeneity), 0.30, 1.20)
```

Starting energy is sampled as:

```text
bounded(
  robot-initial-energy
  + Normal(0, robot-initial-energy × 0.25 × energy-heterogeneity),
  0.50 × robot-initial-energy,
  1.50 × robot-initial-energy
)
```

Risk tolerance is sampled as:

```text
bounded(0.50 + Normal(0, 0.25 × capability-heterogeneity), 0, 1)
```

Robots start:

- in state `exploring`;
- with empty task knowledge;
- without a contract;
- with zero credits;
- with reputation 0.50;
- with zero performance counters;
- with strategy values equal to zero.

If `initial-strategy = "mixed"`, each robot independently selects one of the
three strategies with equal probability.

### 3.1.3 Task generation

Tasks are created on distinct eligible patches that:

- contain no robot;
- have hazard < 0.95.

The actual task count is the smaller of `number-of-tasks` and the number of
eligible patches.

A task is cooperative with probability `coalition-task-proportion`.

For cooperative tasks:

```text
required-team-size = 2 + random(maximum-coalition-size − 1)
```

The reward multiplier is `coalition-reward-multiplier`; otherwise it is 1.

Base reward is sampled around the multiplied reward and bounded to 50–150% of
that central value.

Deadlines are sampled around `task-deadline-base`, then rounded and bounded
between 20 and `mission-time-limit`.

Cooperative service duration increases by 25% for each required member above
one before stochastic variation is added.

Task risk combines local patch hazard and an independent random component:

```text
risk-level = bounded(0.60 × local hazard + 0.40 × U(0, 1), 0, 1)
```

For cooperative tasks, each aggregate capability requirement is sampled around:

```text
0.45 × required-team-size
```

and bounded between 0.60 and the team size.

For individual tasks, each requirement is sampled around 0.50 and bounded to
[0.10, 1.00].

All tasks begin undiscovered, unassigned, hidden, incomplete, and non-failed.

---

## 3.2 Input data

MOSAIC uses no external input dataset.

All patch, robot, and task properties are generated synthetically from
Interface parameters and the selected random seed. No GIS extension, external
mission file, robot log, or platform calibration is required.

The absence of external data provides portability and deterministic
reproducibility, but it also limits empirical interpretation.

---

## 3.3 Submodels

### 3.3.1 Exploration and movement

A free robot:

1. turns right by a random angle in [0, 45);
2. turns left by another random angle in [0, 45);
3. proposes movement by its current speed;
4. compares the proposed patch hazard with its risk tolerance;
5. when the hazard is too high, turns by 90–270 degrees and proposes again.

Movement cost is:

```text
distance × movement-cost-rate × destination terrain cost
+ hazard-cost-rate × destination hazard
```

If the robot can pay the cost, it moves, updates distance and energy accounts,
and marks the destination patch explored.

If it cannot pay, all remaining energy is consumed, the robot becomes depleted,
and any active contract is released.

### 3.3.2 Contracted movement

An assigned robot:

- clears its contract when the task no longer exists, has terminated, or no
  longer lists the robot as a member;
- waits when it is within `task-arrival-radius`;
- otherwise faces the task and moves by the smaller of speed and remaining
  distance.

Cooperative members waiting at the task use state
`waiting-for-coalition`; individual contractors use `waiting`.

### 3.3.3 Task discovery

Each active robot evaluates unknown active tasks inside its effective detection
radius. A task may be detected probabilistically based on sensing capability and
local visibility.

The task's global discovery flag changes only on first detection. The detecting
robot adds the identifier to persistent local knowledge.

### 3.3.4 Dynamic communication network

All existing links die at the start of each communication update.

For each active robot pair within `communication-radius`:

```text
environmental quality =
(mean communication quality of the two occupied patches)

capability quality =
(mean communication capability of the two robots)

effective link quality =
environmental quality × capability quality
```

A link forms when:

- effective quality ≥ `communication-quality-threshold`; and
- an independent random draw survives `communication-failure-rate`.

Link direction is symmetric.

### 3.3.5 Information sharing

Each active link attempts transmission with probability:

```text
information-sharing-probability × link-quality
```

When transmission succeeds, each endpoint offers all task identifiers unknown
to the other endpoint.

Updates are synchronous:

1. copy current knowledge to `next-known-task-ids`;
2. process all links;
3. commit the next lists.

Information therefore travels at most one network edge per tick. Knowledge is
never forgotten.

`information-transmissions` counts items offered across successful links.
`unique-information-acquisitions` counts identifiers actually added to robot
knowledge.

### 3.3.6 Auction opening and bid submission

Every discovered, active, unassigned task opens an auction and clears its
current bid list.

Each free active robot:

1. filters its known identifiers to active unassigned tasks;
2. evaluates bid utility for each candidate;
3. selects the highest positive utility;
4. submits one bid record.

The bid record contains:

```text
[robot-id, bid-utility, capability-contribution]
```

No bid is submitted when all candidate utilities are non-positive or infeasible.

### 3.3.7 Capability contribution

For each capability dimension:

```text
dimension contribution =
min(1, robot capability / task requirement)
```

Overall contribution is the mean of the four dimension contributions.

For individual tasks, this mean must be at least
`minimum-capability-fit`.

For cooperative tasks, it must be at least
`minimum-coalition-contribution`.

### 3.3.8 Feasibility and bid utility

A bid is rejected when:

- the contribution threshold is not met;
- no deadline time remains;
- estimated travel time plus service duration exceeds remaining time;
- estimated energy exceeds current energy.

Estimated travel energy is:

```text
distance × movement-cost-rate × task-patch terrain cost
+ distance × hazard-cost-rate × task-patch hazard
```

Estimated execution energy is:

```text
service duration × task-execution-cost-rate × (1 + task risk)
```

Risk penalty is:

```text
base reward
× risk-penalty-weight
× task risk
× (1 − robot risk tolerance)
```

Deadline pressure is bounded to [0, 1]:

```text
1 − remaining time / mission-time-limit
```

Urgency bonus is:

```text
base reward × deadline-urgency-weight × deadline pressure
```

When reputation is enabled, reputation adjustment is:

```text
base reward × reputation-weight × (reputation − 0.50)
```

Strategy adjustment is:

| Strategy | Individual task | Cooperative task |
|---|---:|---:|
| `competitive` | +0.08 × reward | −0.10 × reward |
| `cooperative` | −0.02 × reward | +0.15 × reward |
| `balanced` | 0 | 0 |

### 3.3.9 Expected reward

For individual tasks:

- all non-mission-aligned regimes: full base reward;
- mission-aligned: base reward × (1 + mission-incentive-weight).

For cooperative tasks:

- `leader-priority`: discoverer expects the leader share; other bidders expect
  an equal division of the remainder;
- `equal-share`: base reward divided by required team size;
- `contribution-based`: bidder contribution divided by expected total
  contribution, where each unknown partner is estimated at 0.50;
- `mission-aligned`: equal base share plus equal mission-bonus share.

Expected reward is a bidding estimate and may differ from final payment.

### 3.3.10 Individual auction resolution

The task selects the highest bid. Exact ties are resolved randomly.

The winner receives a one-member contract. Losing bidders record a lost
contract. Contested participation is recorded when bids exceed the available
positions.

### 3.3.11 Cooperative auction resolution

Bid records are sorted by:

1. descending capability contribution;
2. descending bid utility when contribution is tied.

Robots are appended greedily until:

- the required team size has been reached;
- all aggregate capability requirements are satisfied; or
- the ranked list or maximum coalition size is exhausted.

A feasible coalition receives the contract. Otherwise, the task remains open
and `unresolved-auction-rounds` increases.

This greedy heuristic may miss a feasible combination that would be found by
combinatorial search.

### 3.3.12 Assignment and coalition leadership

Assignment records:

- member identifiers;
- aggregate winning bid;
- assignment tick;
- current discovery-to-assignment delay;
- lead identifier.

The discoverer becomes lead when included in the selected coalition. Otherwise,
the lead is the selected member maximizing:

```text
capability contribution
+ reputation-weight × reputation
```

when reputation is enabled. Without reputation, only capability contribution is
used.

### 3.3.13 Synchronization and task execution

An assignment is valid only while every expected member:

- exists;
- targets the task;
- has positive energy.

All members must be within `task-arrival-radius` before execution begins.

When only some members have arrived:

- arrived members wait;
- waiting time accumulates;
- the assignment is released when
  `ticks − assignment-tick >= coalition-wait-limit`.

Execution energy per member per tick is:

```text
task-execution-cost-rate × (1 + task risk)
```

When every member can pay, each member pays the cost and work progress increases
by one.

If any member cannot pay, depleted members lose remaining energy and the entire
assignment is released.

A task completes when:

```text
work-progress >= service-duration
```

All members contribute equally to progress regardless of capability.

### 3.3.14 Reward distribution

#### Individual tasks

The assigned robot receives:

- base reward; or
- base reward × (1 + mission-incentive-weight) under mission-aligned rewards.

#### Cooperative tasks

`leader-priority`

```text
lead payment = base reward × leader-reward-share
supporter payment =
remaining reward / number of supporters
```

`equal-share`

```text
payment = base reward / actual member count
```

`contribution-based`

```text
payment =
base reward
× member contribution
/ sum of member contributions
```

`mission-aligned`

```text
base payment = base reward / actual member count
bonus payment =
base reward × mission-incentive-weight / actual member count
```

Payment is made only after completion.

`total-task-value` increases by base reward.  
`total-rewards-distributed` increases by actual payments.  
`total-mission-bonuses` records the amount distributed above base reward.

### 3.3.15 Contract release

An assignment is released when:

- a member depletes during movement;
- an expected member becomes unavailable;
- arrival synchronization exceeds the waiting limit;
- a member cannot pay execution cost.

Release:

1. increments robot failure and release counters;
2. applies reputation penalties;
3. clears robot contracts;
4. clears task assignment and progress;
5. records the reason;
6. increments global release counters;
7. reopens the task for future bidding.

Work progress resets to zero after release.

### 3.3.16 Deadline failure and traceability

At the beginning of each tick, every incomplete task with
`ticks >= deadline` fails.

Terminal reasons include:

- `undiscovered`;
- `no-feasible-bid`;
- `no-feasible-coalition`;
- `auction-unresolved`;
- `deadline-after-assignment`;
- `coalition-deadline-after-assignment`;
- `contract-released-before-deadline`;
- the most recent release reason, including depletion, unavailability, or
  arrival timeout.

Completed tasks always retain `failure-reason = "none"`.

### 3.3.17 Reputation

Success update:

```text
reputation :=
reputation
+ reputation-learning-rate
× completion-reputation-reward
× (1 − reputation)
```

Failure update:

```text
reputation :=
reputation
− reputation-learning-rate
× contract-failure-reputation-penalty
× max(0.10, reputation)
```

Tick-level decay:

```text
reputation :=
reputation
+ reputation-decay-rate × (0.50 − reputation)
```

All values are bounded to [0, 1].

When reputation is disabled, these updates and bid adjustments are skipped.

### 3.3.18 Adaptive strategies

When adaptation is enabled and:

```text
ticks > 0
and ticks mod strategy-update-interval = 0
```

each robot:

1. updates the value of its current strategy;
2. chooses the next strategy through epsilon-greedy selection;
3. records a switch when the strategy changes;
4. resets period payoff;
5. records the update tick.

When adaptation is disabled, initial strategies remain fixed.

### 3.3.19 Output metrics

#### Mission performance

```text
completion-rate =
100 × completed tasks / all tasks
```

```text
discovered-task-completion-rate =
100 × completed tasks / discovered tasks
```

```text
explored-area-percentage =
100 × explored patches / all patches
```

#### Communication and information

```text
mean-network-degree =
mean current link count per robot
```

```text
current-network-density =
2 × links / [robots × (robots − 1)]
```

```text
information-coverage =
100 × total known identifiers
/ [robots × tasks]
```

```text
information-redundancy-ratio =
(transmissions − unique acquisitions) / transmissions
```

#### Coalition outcomes

```text
coalition-success-rate =
100 × completed cooperative tasks / coalitions formed
```

```text
coalition-efficiency =
100 × coalitions formed / coalition-formation attempts
```

```text
mean-coalition-size =
mean final participant count of completed cooperative tasks
```

```text
mean-coalition-formation-delay =
mean recorded formation-delay among cooperative tasks ever assigned
```

#### Distribution

`reward-gini` is the Gini coefficient of robot credits.

`contract-gini` is the Gini coefficient of contracts won.

```text
top-reward-share =
100 × credits held by the highest ceiling 20% of robots
/ total credits
```

#### Coordination indices

```text
coordination-overhead =
(
  total bids
  + information transmissions
  + coalition-formation attempts
  + total contract releases
)
/ completed tasks
```

When no task is complete, the numerator is reported without division.

```text
mission-coordination-efficiency =
total task value
/
(
  energy used
  + total bids
  + unique information acquisitions
  + coalition-formation attempts
  + total contract releases
)
```

These are comparative model indices, not physical units.

### 3.3.20 Automated verification

`run-verification-checks` evaluates nine invariants:

1. no task is simultaneously completed and failed;
2. every robot contract corresponds to a task that lists that robot;
3. starting energy equals used energy plus remaining energy within tolerance;
4. robot credits equal total rewards distributed within tolerance;
5. every assigned task member targets that task;
6. every failed task has a recorded failure reason;
7. every known task identifier refers to an existing task;
8. all reputations remain in [0, 1];
9. every robot strategy is valid.

The procedure reports `PASS` only when all nine checks pass.

Verification establishes internal consistency under the tested state. It does
not establish empirical validity.

---

# 4. Interface parameterization

### Environment and reproducibility

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `random-seed-value` | 0 | 999999 | 1 | 42 | Random seed used to reproduce initialization and stochastic events. |
| `terrain-heterogeneity` | 0 | 1 | 0.05 | 0.5 | Controls the dispersion of terrain cost, communication quality, and visibility before spatial diffusion. |
| `hazard-intensity` | 0 | 1 | 0.05 | 0.5 | Scales the initial patch hazard distribution. |

### Robot team

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `number-of-robots` | 5 | 20 | 1 | 15.0 | Number of robot agents created at setup. |
| `robot-initial-energy` | 50 | 200 | 10 | 150.0 | Central value used to generate each robot's initial energy. |
| `energy-heterogeneity` | 0 | 1 | 0.05 | 0.3 | Controls dispersion around robot-initial-energy. |
| `capability-heterogeneity` | 0 | 1 | 0.05 | 0.5 | Controls dispersion of robot capabilities, speed, and risk tolerance. |

### Mission timing and movement

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `mission-time-limit` | 50 | 1000 | 50 | 500.0 | Maximum number of ticks in a mission. |
| `movement-cost-rate` | 0.05 | 1 | 0.05 | 0.1 | Base energy cost per unit of travelled distance. |
| `hazard-cost-rate` | 0 | 2 | 0.10 | 0.2 | Additional movement cost generated by local hazard. |

### Tasks

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `number-of-tasks` | 5 | 100 | 1 | 30.0 | Requested number of mission tasks created at setup. |
| `task-deadline-base` | 50 | 500 | 10 | 250.0 | Central value used to generate task deadlines. |
| `detection-radius` | 1 | 10 | 0.5 | 2.5 | Base radius used in capability-adjusted task detection. |
| `base-task-reward` | 20 | 500 | 10 | 100.0 | Central reward value for individual tasks. |
| `task-deadline-heterogeneity` | 0 | 1 | 0.05 | 0.4 | Controls deadline dispersion. |
| `task-reward-heterogeneity` | 0 | 1 | 0.05 | 0.5 | Controls task-reward dispersion. |
| `task-execution-cost-rate` | 0.01 | 1 | 0.01 | 0.15 | Base energy cost per execution tick. |
| `task-service-duration-base` | 1 | 20 | 1 | 5.0 | Central value used to generate task service duration. |
| `task-arrival-radius` | 0.25 | 2 | 0.25 | 0.75 | Distance within which an assigned robot is considered present at a task. |

### Communication

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `communication-radius` | 1 | 20 | 0.05 | 8.0 | Maximum spatial distance at which a communication link may form. |
| `communication-quality-threshold` | 0 | 1 | 0.05 | 0.25 | Minimum effective link quality required for link creation. |
| `information-sharing-probability` | 0 | 1 | 0.05 | 0.9 | Base probability of knowledge transmission over an active link. |
| `communication-failure-rate` | 0 | 1 | 0.05 | 0.05 | Independent probability that an otherwise feasible link fails during reconstruction. |

### Auctions and coalitions

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `minimum-capability-fit` | 0 | 1 | 0.05 | 0.6 | Minimum mean normalized capability contribution required for an individual-task bid. |
| `risk-penalty-weight` | 0 | 1 | 0.05 | 0.35 | Weight applied to risk exposure in bid utility. |
| `deadline-urgency-weight` | 0 | 1 | 0.05 | 0.2 | Weight applied to urgency in bid utility. |
| `coalition-task-proportion` | 0 | 1 | 0.05 | 0.4 | Probability that a newly created task requires a coalition. |
| `minimum-coalition-contribution` | 0 | 1 | 0.05 | 0.15 | Minimum mean normalized capability contribution required for a cooperative-task bid. |
| `maximum-coalition-size` | 2 | 3 | 1 | 3 | Maximum number of robots allowed in a coalition. |
| `coalition-reward-multiplier` | 1 | 3 | 0.10 | 1.5 | Multiplier applied to the reward of cooperative tasks. |
| `coalition-wait-limit` | 5 | 100 | 5 | 30.0 | Maximum ticks allowed for all assigned members to arrive after assignment. |
| `leader-reward-share` | 0.50 | 1 | 0.05 | 0.7 | Share of base reward paid to the coalition lead under leader-priority. |
| `mission-incentive-weight` | 0 | 1 | 0.05 | 0.25 | Additional reward fraction under mission-aligned payments. |

### Reputation

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `reputation-weight` | 0 | 1 | 0.05 | 0.25 | Weight of reputation deviation from 0.50 in bid utility and coalition-lead scoring. |
| `reputation-learning-rate` | 0 | 1 | 0.05 | 0.2 | Step size for reputation changes after success or failure. |
| `completion-reputation-reward` | 0 | 0.25 | 0.01 | 0.05 | Success increment coefficient used in reputation updating. |
| `contract-failure-reputation-penalty` | 0 | 0.50 | 0.01 | 0.1 | Failure decrement coefficient used in reputation updating. |
| `reputation-decay-rate` | 0 | 0.05 | 0.005 | 0.005 | Rate at which reputation returns toward the neutral value 0.50. |

### Adaptive strategies

| Parameter | Minimum | Maximum | Step | Default | Description |
|---|---:|---:|---:|---:|---|
| `strategy-learning-rate` | 0 | 1 | 0.05 | 0.2 | Step size used to update learned strategy values. |
| `strategy-exploration-rate` | 0 | 1 | 0.05 | 0.1 | Probability of random strategy selection during an update. |
| `strategy-update-interval` | 1 | 100 | 1 | 20.0 | Number of ticks between adaptive-strategy updates. |

### Switches and choosers

| Control | Type | Default | Available values or meaning |
|---|---|---|---|
| `show-network?` | Switch | false | Shows or hides current communication links. |
| `reputation-enabled?` | Switch | true | Enables reputation updates and reputation-dependent decision terms. |
| `adaptive-strategies?` | Switch | true | Enables periodic strategy-value updating and epsilon-greedy strategy selection. |
| `show-robot-labels?` | Switch | false | Shows or hides robot identifiers. |
| `show-task-labels?` | Switch | false | Shows or hides task identifiers and state prefixes. |
| `reward-regime` | Chooser | `equal-share` | `leader-priority`, `equal-share`, `contribution-based`, `mission-aligned` |
| `initial-strategy` | Chooser | `mixed` | `competitive`, `cooperative`, `balanced`, `mixed` |


---

# 5. Assumptions

1. Robots know their own capabilities, energy, risk tolerance, reputation, and
   current strategy.
2. Learned task identifiers are retained permanently.
3. Communication links are symmetric.
4. Communication exchanges task identifiers rather than full messages.
5. Robots can hold only one active contract.
6. Free robots submit at most one bid per tick.
7. Tasks act as decentralized auctioneers.
8. Reputation is globally readable when enabled.
9. Travel, service, risk, and energy estimates are available to bidders.
10. Coalition search is greedy.
11. Coalition members contribute equally to work progress.
12. Partial work is lost after contract release.
13. Payment occurs only after task completion.
14. Robots do not strategically defect after assignment.
15. No tasks arrive after setup.
16. The environment does not change during a run.
17. Robots do not collide, block, or physically interfere.
18. Communication has no bandwidth, latency, congestion, or message corruption
    beyond link and transmission probabilities.

---

# 6. Limitations

1. The environment is synthetic and dimensionless.
2. The world is toroidal, which may not represent bounded field sites.
3. Low-level motion, vehicle dynamics, and collision avoidance are omitted.
4. Exploration uses a simple stochastic walk.
5. Route-energy prediction uses task-patch conditions instead of full
   path-integrated conditions.
6. Communication represents contact opportunities rather than a detailed
   network protocol.
7. Knowledge contains only task identifiers.
8. Coalition formation may miss feasible combinations.
9. Robots do not negotiate coalition composition directly.
10. Reward expectations can differ from final payments.
11. Reputation is represented by one scalar.
12. Learning is limited to three predefined strategies.
13. Strategy preferences contain fixed task-type bonuses and penalties.
14. Work contribution does not depend on member capability.
15. The model does not include deliberate deception, defection, or adversarial
    behavior.
16. Mission coordination efficiency combines heterogeneous model quantities.
17. Internal verification does not provide empirical validation.
18. The model has not been calibrated against physical mission data.
19. Human supervision and intervention are not represented.
20. Findings are conditional on the implemented mechanisms, parameter ranges,
    and experimental design.

---

# 7. Reproducibility and experimental use

Exact reproduction requires:

- the same `MOSAIC_v0.4.0.nlogox` file;
- NetLogo 7.0.4;
- identical Interface parameters;
- identical random seed;
- identical BehaviorSpace configuration;
- final metrics recorded under the same stopping condition.

The official v0.4.0 experimental evidence contains seven BehaviorSpace
experiments, 30 paired seeds per condition, and 690 official runs. Those
results are documented in the experimental-results package and are not part of
the ODD mechanism specification.

---

# 8. References

Bonabeau, E. (2002). Agent-based modeling: Methods and techniques for
simulating human systems. *Proceedings of the National Academy of Sciences*,
99(Suppl. 3), 7280–7287.

Grimm, V., Berger, U., Bastiansen, F., Eliassen, S., Ginot, V., Giske, J.,
Goss-Custard, J., Grand, T., Heinz, S. K., Huse, G., Huth, A., Jepsen, J. U.,
Jørgensen, C., Mooij, W. M., Müller, B., Pe’er, G., Piou, C., Railsback, S. F.,
Robbins, A. M., Robbins, M. M., Rossmanith, E., Rüger, N., Strand, E., Souissi,
S., Stillman, R. A., Vabø, R., Visser, U., and DeAngelis, D. L. (2006). A
standard protocol for describing individual-based and agent-based models.
*Ecological Modelling*, 198(1–2), 115–126.

Grimm, V., Berger, U., DeAngelis, D. L., Polhill, J. G., Giske, J., and
Railsback, S. F. (2010). The ODD protocol: A review and first update.
*Ecological Modelling*, 221(23), 2760–2768.

Smith, R. G. (1980). The Contract Net Protocol: High-level communication and
control in a distributed problem solver. *IEEE Transactions on Computers*,
C-29(12), 1104–1113.

Wilensky, U. (1999). NetLogo. Center for Connected Learning and Computer-Based
Modeling, Northwestern University, Evanston, Illinois.

Wooldridge, M. (2009). *An Introduction to MultiAgent Systems* (2nd ed.).
Wiley.

---

# 9. Authorship and use

MOSAIC was developed collaboratively by Katherin Molina and Lorena Holguin. Both authors must be credited in the public repository, model documentation, technical report, citation metadata, and releases.


MOSAIC, its original source code, model design, documentation, experimental
design, and associated research materials were developed collaboratively by Katherin Molina and Lorena Holguin.

The NetLogo model and source code are licensed under Apache-2.0. This ODD
Protocol is licensed under CC-BY-4.0. Reuse must preserve attribution to
Katherin Molina and Lorena Holguin.
