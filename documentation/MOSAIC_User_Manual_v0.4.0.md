# MOSAIC User Manual

## Mission-Oriented Self-Organization through Auctions, Incentives, and Coalitions

**Version:** 0.4.0  
**Authors:** Katherin Molina and Lorena Holguin  
**Implementation:** NetLogo 7.0.4  
**Model file:** `MOSAIC_v0.4.0.nlogox`  
**Document language:** English  
**Document purpose:** Operational guide for interactive use, verification,
BehaviorSpace experiments, interpretation, export, and troubleshooting

---

## 1. Scope

This manual explains how to install, open, configure, run, inspect, verify, and
experiment with MOSAIC v0.4.0.

Use this document when you need to:

- run a single interactive mission;
- reproduce the documented baseline;
- inspect individual coordination processes;
- interpret the world, monitors, and plots;
- execute the automated verification suite;
- reproduce BehaviorSpace experiments BS01–BS07;
- export and organize experiment results;
- diagnose common configuration or runtime problems.

For the formal model specification, use
`MOSAIC_ODD_Protocol_v0.4.0.md`. For statistical findings, use the experimental
results package and integrated analysis workbook.

---

## 2. Software requirements

### 2.1 Required software

- NetLogo 7.0.4
- A desktop operating system supported by NetLogo
- Sufficient storage for BehaviorSpace CSV exports

The release model uses the NetLogo 7 XML format:

```text
.nlogox
```

Do not rename it with a second `.nlogo` extension.

### 2.2 Recommended working directory

Create a local project directory such as:

```text
MOSAIC/
├── model/
├── documentation/
├── experiments/
├── analysis/
├── figures/
└── report/
```

Place the model at:

```text
model/MOSAIC_v0.4.0.nlogox
```

---

## 3. Opening the model

1. Launch NetLogo 7.0.4.
2. Select `File → Open`.
3. Open `MOSAIC_v0.4.0.nlogox`.
4. Confirm that the Interface tab appears without an error dialog.
5. Confirm that the buttons `SETUP`, `GO`, `STEP`, and `RUN CHECKS` are visible.
6. Confirm that the `Verification Result` monitor initially displays `NOT RUN`
   or an equivalent reset state.

When opening the file from a public repository, do not edit the release copy
directly. Create a working copy for modifications.

---

## 4. Quick start

### 4.1 Validated manual baseline

Use the following principal settings:

```text
random-seed-value = 42
number-of-robots = 15
robot-initial-energy = 150
energy-heterogeneity = 0.30
capability-heterogeneity = 0.50
mission-time-limit = 500
movement-cost-rate = 0.10
number-of-tasks = 30
communication-radius = 8
communication-failure-rate = 0.05
communication-quality-threshold = 0.25
coalition-task-proportion = 0.40
reward-regime = "equal-share"
reputation-enabled? = true
adaptive-strategies? = true
initial-strategy = "mixed"
show-network? = false
show-robot-labels? = false
show-task-labels? = false
```

Other sliders should remain at their model defaults.

### 4.2 Run sequence

1. Set `random-seed-value` to `42`.
2. Press `SETUP`.
3. Inspect the generated world.
4. Press `GO`.
5. Wait until the model stops automatically.
6. Press `RUN CHECKS`.
7. Confirm:

```text
OVERALL RESULT: PASS
```

### 4.3 Step-by-step inspection

For detailed inspection:

1. Press `SETUP`.
2. Turn on `show-network?`.
3. Turn on robot or task labels when needed.
4. Press `STEP` repeatedly.
5. Observe task discovery, knowledge diffusion, bidding, coalition formation,
   movement, waiting, execution, and release events.

`STEP` is preferable to `GO` when investigating a specific transition.

---

## 5. Interface overview

The Interface is organized into five functional regions.

### 5.1 Inputs

Sliders, switches, and choosers define:

- environmental complexity;
- robot heterogeneity and resources;
- task composition and deadlines;
- communication reliability;
- auction feasibility;
- coalition formation;
- reward regime;
- reputation;
- adaptive strategies;
- visualization options.

Input values are read during `SETUP` and throughout the run where relevant.
Changing a generation parameter after `SETUP` does not regenerate existing
patches, robots, or tasks.

### 5.2 World view

The world displays:

- environmental patches;
- robot positions and states;
- task locations and states;
- optional communication links;
- optional labels.

### 5.3 Controls

| Button | Command | Mode | Use |
|---|---|---|---|
| `SETUP` | `` | Single action | Clears all state and generates a new environment, robot team, task set, and initial network. |
| `GO` | `` | Forever | Runs the model continuously until a stopping condition is reached or the button is pressed again. |
| `STEP` | `` | Single action | Executes exactly one model tick. Use for inspection and debugging. |
| `RUN CHECKS` | `` | Single action | Runs the complete nine-invariant automated verification suite. |

### 5.4 Monitors

Monitors report current or cumulative model outcomes. Several values are
undefined or zero at the start of a run. Interpret ratios only after the
relevant event count becomes positive.

### 5.5 Plots

The plots update during interactive runs. They are not required for
BehaviorSpace because official experiments record final reporters rather than
plot images.

---

## 6. Visual interpretation

### 6.1 Robot colors and states

Robot appearance follows its operational state and strategy. The principal
operational interpretation is:

| State | Meaning |
|---|---|
| `exploring` | The robot is free and searching for tasks. |
| `travelling` | The robot has a contract and is moving toward the task. |
| `waiting` | The robot has arrived at an individual task. |
| `waiting-for-coalition` | The robot has arrived at a cooperative task and is waiting for partners. |
| `executing` | The robot is contributing execution work. |
| `depleted` | The robot has no remaining energy and cannot act. |

Robot colors can also encode strategy. Use labels and monitors when precise
state interpretation is required.

### 6.2 Task visibility and state

Tasks begin hidden because they are undiscovered. Their visibility and
appearance change as the mission progresses.

| Task condition | Interpretation |
|---|---|
| Hidden | No robot has discovered the task. |
| Discovered and open | Known to at least one robot and available for bidding. |
| Assigned | A robot or coalition holds the contract. |
| Completed | Execution reached the required service duration. |
| Failed | The deadline expired before completion. |

### 6.3 Patch zones

Patch color represents the dominant environmental category:

| Zone | Meaning |
|---|---|
| `open` | No dominant hazard, terrain, or communication constraint. |
| `difficult-terrain` | Elevated movement cost. |
| `hazard` | Elevated local risk and movement penalty. |
| `communication-shadow` | Reduced environmental communication quality. |

The environment is synthetic and dimensionless.

### 6.4 Communication links

When `show-network?` is enabled, links display current communication
opportunities.

Links are temporary. The network is destroyed and rebuilt every tick.
Disappearance does not necessarily represent permanent network failure.

---

## 7. Buttons

| Button | Command | Mode | Use |
|---|---|---|---|
| `SETUP` | `` | Single action | Clears all state and generates a new environment, robot team, task set, and initial network. |
| `GO` | `` | Forever | Runs the model continuously until a stopping condition is reached or the button is pressed again. |
| `STEP` | `` | Single action | Executes exactly one model tick. Use for inspection and debugging. |
| `RUN CHECKS` | `` | Single action | Runs the complete nine-invariant automated verification suite. |

### 7.1 SETUP

`SETUP` must be pressed before every new mission. It:

- clears previous agents, links, plots, counters, and verification state;
- applies the random seed;
- generates the environment;
- creates robots;
- creates tasks;
- builds the initial communication network;
- resets ticks.

Pressing `SETUP` destroys the current mission.

### 7.2 GO

`GO` runs the model continuously. The model stops when:

- the mission-time limit is reached;
- no robot has positive energy; or
- no active task remains.

Pressing `GO` again while it is running stops continuous execution manually.

### 7.3 STEP

`STEP` executes one complete tick using the same scheduling logic as `GO`.
It is useful for:

- debugging;
- teaching;
- verifying order of events;
- inspecting a release or failure;
- taking screenshots.

### 7.4 RUN CHECKS

`RUN CHECKS` evaluates nine internal invariants:

1. task state consistency;
2. contract consistency;
3. energy accounting;
4. reward accounting;
5. coalition membership;
6. failure traceability;
7. knowledge integrity;
8. reputation bounds;
9. strategy validity.

A correct run should end with:

```text
OVERALL RESULT: PASS
```

A failed check indicates an internal consistency problem, not simply poor
mission performance.

---

## 8. Input reference

### Reproducibility and environment

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `random-seed-value` | 0 | 999999 | 1 | 42 | Controls reproducibility. The final manual baseline is 42. |
| `terrain-heterogeneity` | 0 | 1 | 0.05 | 0.5 | Controls spatial variation in terrain cost, communication quality, and visibility. |
| `hazard-intensity` | 0 | 1 | 0.05 | 0.5 | Scales the initial distribution of environmental hazard. |

### Robot team

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `number-of-robots` | 5 | 20 | 1 | 15.0 | Number of robot agents created at setup. |
| `robot-initial-energy` | 50 | 200 | 10 | 150.0 | Central initial energy value before heterogeneity is applied. |
| `energy-heterogeneity` | 0 | 1 | 0.05 | 0.3 | Controls dispersion around the central robot energy. |
| `capability-heterogeneity` | 0 | 1 | 0.05 | 0.5 | Controls dispersion in robot capabilities, speed, and risk tolerance. |

### Mission timing and energy

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `mission-time-limit` | 50 | 1000 | 50 | 500.0 | Maximum mission duration in ticks. |
| `movement-cost-rate` | 0.05 | 1 | 0.05 | 0.1 | Base movement energy expenditure per distance unit. |
| `hazard-cost-rate` | 0 | 2 | 0.10 | 0.2 | Additional movement cost generated by destination hazard. |

### Task generation and execution

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `number-of-tasks` | 5 | 100 | 1 | 30.0 | Requested number of mission tasks. |
| `task-deadline-base` | 50 | 500 | 10 | 250.0 | Central deadline used to generate task-specific deadlines. |
| `detection-radius` | 1 | 10 | 0.5 | 2.5 | Base sensing radius before capability adjustment. |
| `base-task-reward` | 20 | 500 | 10 | 100.0 | Central reward value used to generate task rewards. |
| `task-deadline-heterogeneity` | 0 | 1 | 0.05 | 0.4 | Controls deadline dispersion. |
| `task-reward-heterogeneity` | 0 | 1 | 0.05 | 0.5 | Controls reward dispersion. |
| `task-execution-cost-rate` | 0.01 | 1 | 0.01 | 0.15 | Energy spent by each assigned member per execution tick. |
| `task-service-duration-base` | 1 | 20 | 1 | 5.0 | Central number of execution ticks required by a task. |
| `task-arrival-radius` | 0.25 | 2 | 0.25 | 0.75 | Distance at which a robot is considered present at an assigned task. |

### Communication and information

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `communication-radius` | 1 | 20 | 0.05 | 8.0 | Maximum distance for possible communication-link formation. |
| `communication-quality-threshold` | 0 | 1 | 0.05 | 0.25 | Minimum effective link quality required for a communication link. |
| `information-sharing-probability` | 0 | 1 | 0.05 | 0.9 | Base probability that an active link transmits knowledge. |
| `communication-failure-rate` | 0 | 1 | 0.05 | 0.05 | Probability that a feasible link fails during network reconstruction. |

### Auctions, coalitions, and incentives

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `minimum-capability-fit` | 0 | 1 | 0.05 | 0.6 | Minimum mean normalized capability contribution for individual-task bidding. |
| `risk-penalty-weight` | 0 | 1 | 0.05 | 0.35 | Weight applied to task risk in bid utility. |
| `deadline-urgency-weight` | 0 | 1 | 0.05 | 0.2 | Weight applied to deadline pressure in bid utility. |
| `coalition-task-proportion` | 0 | 1 | 0.05 | 0.4 | Probability that a newly generated task requires a coalition. |
| `minimum-coalition-contribution` | 0 | 1 | 0.05 | 0.15 | Minimum mean capability contribution for a cooperative-task bid. |
| `maximum-coalition-size` | 2 | 3 | 1 | 3 | Maximum number of robots permitted in one coalition. |
| `coalition-reward-multiplier` | 1 | 3 | 0.10 | 1.5 | Reward multiplier for cooperative tasks. |
| `coalition-wait-limit` | 5 | 100 | 5 | 30.0 | Maximum time assigned members may wait for coalition synchronization. |
| `leader-reward-share` | 0.50 | 1 | 0.05 | 0.7 | Leader payment share under the leader-priority reward regime. |
| `mission-incentive-weight` | 0 | 1 | 0.05 | 0.25 | Additional reward fraction under mission-aligned rewards. |

### Reputation

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `reputation-weight` | 0 | 1 | 0.05 | 0.25 | Weight of reputation in bid utility and coalition leadership. |
| `reputation-learning-rate` | 0 | 1 | 0.05 | 0.2 | Rate of reputation adjustment after success or failure. |
| `completion-reputation-reward` | 0 | 0.25 | 0.01 | 0.05 | Success coefficient used in reputation updates. |
| `contract-failure-reputation-penalty` | 0 | 0.50 | 0.01 | 0.1 | Failure coefficient used in reputation updates. |
| `reputation-decay-rate` | 0 | 0.05 | 0.005 | 0.005 | Rate at which reputation returns toward the neutral value 0.50. |

### Adaptive strategies

| Control | Minimum | Maximum | Step | Default | Operational meaning |
|---|---:|---:|---:|---:|---|
| `strategy-learning-rate` | 0 | 1 | 0.05 | 0.2 | Incremental learning rate for strategy values. |
| `strategy-exploration-rate` | 0 | 1 | 0.05 | 0.1 | Probability of selecting a random strategy during adaptation. |
| `strategy-update-interval` | 1 | 100 | 1 | 20.0 | Number of ticks between strategy updates. |


### Switches and choosers

| Control | Type | Default | Meaning or values |
|---|---|---|---|
| `show-network?` | Switch | false | Shows or hides current communication links. |
| `reputation-enabled?` | Switch | true | Enables reputation updates and reputation-sensitive decisions. |
| `adaptive-strategies?` | Switch | true | Enables periodic strategy-value learning and strategy switching. |
| `show-robot-labels?` | Switch | false | Shows or hides robot identifiers. |
| `show-task-labels?` | Switch | false | Shows or hides task identifiers and task-state prefixes. |
| `reward-regime` | Chooser | `equal-share` | `leader-priority`, `equal-share`, `contribution-based`, `mission-aligned` |
| `initial-strategy` | Chooser | `mixed` | `competitive`, `cooperative`, `balanced`, `mixed` |


### 8.1 Parameters that require SETUP

The following categories are generated during setup and should not be changed
mid-run when a controlled experiment is intended:

- terrain heterogeneity;
- hazard intensity;
- robot count;
- initial energy;
- capability and energy heterogeneity;
- task count;
- task reward, deadline, and service distributions;
- cooperative-task proportion;
- initial strategy.

### 8.2 Parameters with continuing effects

The following controls are read during the mission and can affect subsequent
ticks if changed interactively:

- mission time limit;
- movement and hazard cost rates;
- communication radius and reliability;
- information-sharing probability;
- auction thresholds and utility weights;
- coalition waiting limit;
- reward-regime parameters;
- reputation parameters;
- adaptive-strategy parameters;
- visualization switches.

Changing them mid-run creates an intervention scenario. Do not do this during a
baseline replication unless the intervention is deliberately documented.

---

## 9. Output monitors

| Monitor | Reporter | Interpretation |
|---|---|---|
| `Ticks` | `` | Elapsed mission cycles. |
| `Coalitions Formed` | `` | Feasible cooperative teams assigned. |
| `Coalition Formation Efficiency (%)` | `` | Coalitions formed divided by formation attempts. |
| `Total Bids` | `` | Cumulative bids submitted. |
| `Coordination Overhead` | `` | Coordination activity relative to completed tasks. |
| `Coalition Success Rate (%)` | `` | Completed cooperative tasks divided by coalitions formed. |
| `Current Links` | `` | Communication links active in the current tick. |
| `Contract Gini` | `` | Inequality of contracts won. |
| `Top 20% Reward (%)` | `` | See the ODD Protocol for the exact reporter definition. |
| `Information Coverage (%)` | `` | Task identifiers known across the robot population. |
| `Reward Gini` | `` | Inequality of robot credit outcomes. |
| `Mission Efficiency` | `` | Task value relative to coordination and energy expenditure. |
| `Verification Result` | `` | Latest automated verification status. |
| `Strategy Switches` | `` | See the ODD Protocol for the exact reporter definition. |
| `Mean Reputation` | `` | See the ODD Protocol for the exact reporter definition. |
| `Contracts Awarded` | `` | Assignment events. |
| `Total Task Value` | `` | See the ODD Protocol for the exact reporter definition. |
| `Remaining Energy` | `` | See the ODD Protocol for the exact reporter definition. |
| `Completion Rate (%)` | `` | Completed tasks divided by all tasks. |
| `Completed Tasks` | `` | Tasks completed successfully. |
| `Failed Tasks` | `` | Tasks that reached a terminal failure. |
| `Contract Releases` | `` | Assignments released before successful completion. |

### 9.1 Outcome versus process metrics

Do not evaluate the model using completion rate alone.

Outcome metrics include:

- completion rate;
- failed tasks;
- task value;
- coalition success.

Process metrics include:

- total bids;
- unresolved auctions;
- coalition formation efficiency;
- formation delay;
- releases;
- information transmissions;
- coordination overhead.

A condition can complete many tasks while using an inefficient coordination
process.

### 9.2 Formation efficiency versus coalition success

These two monitors answer different questions.

```text
Coalition Formation Efficiency (%) =
coalitions formed / formation attempts
```

It measures the ability to construct feasible coalitions.

```text
Coalition Success Rate (%) =
completed cooperative tasks / coalitions formed
```

It measures the ability of formed coalitions to finish their assignments.

Low formation efficiency with high coalition success indicates a matching
bottleneck before assignment rather than an execution bottleneck after
assignment.

### 9.3 Reward and contract inequality

- `Reward Gini` measures inequality in final robot credits.
- `Contract Gini` measures inequality in contracts won.

A lower Gini value indicates a more even distribution. Zero represents complete
equality. Compare Gini values only across runs with the same team size and
similar mission design.

---

## 10. Plots

| Plot | Axes | Pens | Interpretation |
|---|---|---|---|
| `Mission Progress` | x: ``; y: `` | `Completed`, `Active`, `Failed` | Shows time-dependent model behavior during an interactive run. |
| `Strategy Distribution` | x: ``; y: `` | `Competitive`, `Cooperative`, `Balanced` | Shows time-dependent model behavior during an interactive run. |

### 10.1 Plot reset behavior

Plots reset when `SETUP` is pressed.

If a plot appears empty:

- confirm that `SETUP` has been pressed;
- execute at least one tick;
- check that the relevant event has occurred;
- verify that the plot pen is enabled.

### 10.2 Exporting plot images

For documentation:

1. Run the desired mission.
2. Open the plot's context menu.
3. Export or copy the plot.
4. Save the figure under `figures/`.
5. Use an informative file name, such as:

```text
MOSAIC_Baseline_Strategy_Distribution_Seed42.png
```

Do not use screenshots as substitutes for the numerical experiment data.

---

## 11. Diagnostic use of the Command Center

The Command Center can inspect model state during or after a run.

### 11.1 Mission status

```netlogo
show ticks
show completion-rate
show count tasks with [completed?]
show count tasks with [failed?]
show explored-area-percentage
```

### 11.2 Active robot states

```netlogo
show [(list robot-id state energy target-task-id strategy reputation)] of robots
```

### 11.3 Task states

```netlogo
show [(list task-id cooperative? discovered? auction-status assigned-robot-ids work-progress completed? failed? failure-reason)] of tasks
```

### 11.4 Failed-task diagnosis

```netlogo
show [(list task-id cooperative? discovered-at ever-received-bid? ever-assigned? release-count failure-reason)] of tasks with [failed?]
```

### 11.5 Coalition diagnosis

```netlogo
show [(list task-id assigned-robot-ids required-team-size formation-delay work-progress)] of tasks with [cooperative? and auction-status = "assigned"]
```

### 11.6 Network diagnosis

```netlogo
show count communication-links
show mean-network-degree
show count robots with [not any? my-communication-links]
show information-coverage
```

### 11.7 Economic diagnosis

```netlogo
show total-task-value
show total-rewards-distributed
show total-mission-bonuses
show reward-gini
show contract-gini
```

### 11.8 Verification

```netlogo
run-verification-checks
```

Use Command Center changes cautiously. Directly modifying agent state can
invalidate reproducibility and verification.

---

## 12. BehaviorSpace experiments

### 12.1 Opening BehaviorSpace

Select:

```text
Tools → BehaviorSpace
```

The model includes seven experiments:

| ID | Experiment | Conditions | Planned runs |
|---|---|---:|---:|
| BS01 | Baseline Replication | 1 | 30 |
| BS02 | Reward Regime Comparison | 4 | 120 |
| BS03 | Communication Degradation | 4 | 120 |
| BS04 | Capability Heterogeneity | 3 | 90 |
| BS05 | Cooperative Task Demand | 3 | 90 |
| BS06 | Reputation × Adaptation | 4 | 120 |
| BS07 | Mission Incentive Strength | 4 | 120 |

Total official experimental runs:

```text
690
```

### 12.2 Common experiment configuration

All official experiments use:

```text
Setup commands: setup
Go commands: go
Repetitions: 1
Random seeds: 1–30
Run metrics every step: Off
Update view: Off
Time limit: 500
```

Stopping condition:

```netlogo
not any? tasks with [not completed? and not failed?]
or ticks >= mission-time-limit
or not any? robots with [energy > 0]
```

### 12.3 Running an experiment

1. Open BehaviorSpace.
2. Select the experiment.
3. Confirm the planned run count.
4. Confirm `Run metrics every step` is disabled.
5. Disable view updates for speed unless visual inspection is required.
6. Choose the spreadsheet CSV export format.
7. Select an output directory outside the model file directory.
8. Run the experiment.
9. Do not edit the raw export.
10. Copy the export to the correct `experiments/raw/` directory.

### 12.4 Raw file naming

Use:

```text
MOSAIC_<Experiment_ID>_<Short_Name>_Raw.csv
```

Examples:

```text
MOSAIC_BS01_Baseline_Replication_30Seeds_Raw.csv
MOSAIC_BS06_Reputation_Adaptation_30Seeds_Raw.csv
```

### 12.5 Re-running an official experiment

Do not overwrite the official raw dataset.

For a reproduction run, use:

```text
MOSAIC_BS01_Baseline_Replication_30Seeds_Reproduction_<YYYYMMDD>.csv
```

Compare checksums and processed outputs separately.

### 12.6 Creating a new experiment

New experiments should begin with:

```text
BS08
```

Document:

- research question;
- varied parameters;
- fixed parameters;
- seed design;
- run count;
- reporters;
- stopping condition;
- expected interpretation.

---

## 13. Export and data management

### 13.1 Official directory structure

```text
experiments/
├── raw/
├── processed/
├── summaries/
├── Experimental_Design.md
├── Data_Dictionary.md
└── Validation_Report.md
```

### 13.2 Raw data policy

Files under `raw/` are immutable evidence.

Do not:

- open and re-save them in spreadsheet software;
- remove NetLogo metadata;
- change headers;
- reorder columns;
- overwrite them with processed data.

### 13.3 Processed data

Processed files should:

- contain one row per run;
- use stable English column names;
- preserve seed and condition identifiers;
- document every transformation;
- remain traceable to one raw export.

### 13.4 Summaries and analyses

Statistical workbooks and summary tables are derived products. They can be
regenerated from processed data, but they should not silently replace the
official evidence.

### 13.5 Checksums

Use the package `SHA256SUMS.txt` file to detect accidental changes.

A changed checksum means the file is no longer byte-for-byte identical to the
frozen package.

---

## 14. Reproducibility

Exact reproduction requires:

- the same model file;
- NetLogo 7.0.4;
- identical parameter values;
- identical random seed;
- identical BehaviorSpace configuration;
- identical stopping conditions;
- no manual state intervention.

For a manual baseline reproduction:

```text
random-seed-value = 42
```

For the official experiments:

```text
random-seed-value = 1 through 30
```

A different NetLogo version may change stochastic ordering or file behavior and
must be reported.

---

## 15. Verification workflow

### 15.1 Interactive verification

After a completed run:

1. Press `RUN CHECKS`.
2. Read the Command Center output.
3. Confirm all nine checks are `PASS`.
4. Confirm `Verification Result = PASS`.

### 15.2 Verification under stress

Poor mission performance is not a verification failure.

A stress configuration may produce:

- lower completion;
- more unresolved auctions;
- more releases;
- more failed tasks;
- greater inequality.

The model remains internally correct when the nine invariants pass.

### 15.3 BehaviorSpace verification scope

Official BehaviorSpace exports record five run-level verification indicators:

- task state;
- energy accounting;
- reward accounting;
- reputation bounds;
- strategy validity.

The complete `RUN CHECKS` procedure additionally evaluates:

- contract consistency;
- coalition membership;
- failure traceability;
- knowledge integrity.

---

## 16. Troubleshooting

### 16.1 The model does not start

Check:

- the file extension is `.nlogox`;
- NetLogo 7.0.4 is installed;
- `SETUP` was pressed before `GO`;
- the model file was not corrupted during download.

### 16.2 The model stops immediately

Possible causes:

- `mission-time-limit` is zero or very low;
- `number-of-robots` is zero;
- robots have no usable energy;
- no tasks were created;
- all tasks are already terminal because an invalid state was introduced.

Press `SETUP` with the validated baseline.

### 16.3 No tasks are visible

This is expected at the beginning. Tasks are hidden until discovered.

Turn on task labels only after discovery if detailed inspection is required.

### 16.4 No communication links are visible

Check:

- `show-network? = true`;
- robots are within `communication-radius`;
- effective link quality exceeds the threshold;
- the link did not fail probabilistically.

The network is temporary and may change every tick.

### 16.5 Completion is low

Low completion is not necessarily a bug.

Inspect:

- failed-task reasons;
- information coverage;
- isolated robots;
- unresolved auction rounds;
- coalition formation efficiency;
- contract releases;
- remaining energy;
- cooperative-task proportion.

### 16.6 Coalition success is high but completion is low

This often indicates that formed coalitions execute successfully, but too few
feasible coalitions form before deadlines.

Inspect:

- coalition formation efficiency;
- formation delay;
- unresolved auctions;
- no-feasible-coalition failures;
- communication radius;
- capability heterogeneity.

### 16.7 Strategy switches remain zero

Check:

- `adaptive-strategies? = true`;
- `strategy-update-interval` is lower than the mission duration;
- the model has passed at least one update interval.

When adaptation is disabled, zero switches are correct.

### 16.8 Mean reputation remains 0.50

Check:

- `reputation-enabled? = true`;
- successful or failed contract events occurred;
- learning coefficients are non-zero.

When reputation is disabled, a stable mean of 0.50 is correct.

### 16.9 A verification check fails

1. Stop the model.
2. Record the random seed and all parameter values.
3. Save the Command Center output.
4. Reproduce the failure from a clean `SETUP`.
5. Use `STEP` near the failure tick.
6. Inspect contracts, memberships, task state, energy, and failure reasons.
7. Do not publish or merge a changed model until the failure is explained.

### 16.10 BehaviorSpace run count is incorrect

Check discrete-value syntax.

Correct:

```netlogo
["communication-radius" 4 8]
["communication-failure-rate" 0.05 0.35]
```

Seed increment syntax:

```netlogo
["random-seed-value" [1 1 30]]
```

Do not wrap enumerated discrete values in an additional list.

---

## 17. Safe interpretation

MOSAIC is a research simulation.

Do not treat its outputs as:

- field-ready robot-control instructions;
- safety certification;
- empirical predictions for mining, defence, agriculture, or environmental
  missions;
- proof that a reward regime will perform identically on physical platforms;
- evidence that a real communication network will behave like the model.

Interpret findings conditionally:

> Under the implemented mechanisms and tested parameter ranges, the model
> produced the reported comparative outcomes.

---

## 18. Recommended user workflows

### 18.1 Demonstration workflow

Use:

```text
seed = 42
show-network? = true
show-robot-labels? = true
show-task-labels? = true
STEP
```

This is appropriate for teaching and process explanation.

### 18.2 Baseline analysis workflow

Use the validated baseline, labels hidden, and `GO`. After completion:

- run checks;
- record final monitors;
- inspect failed-task reasons;
- export plots if needed.

### 18.3 Experimental workflow

Use BehaviorSpace with:

- fixed seed pairing;
- view updates disabled;
- final metrics only;
- immutable raw exports;
- documented processing.

### 18.4 Development workflow

When modifying code:

1. copy the release model;
2. change the version identifier;
3. run the nine checks;
4. reproduce the baseline;
5. execute targeted regression experiments;
6. document the change in `CHANGELOG.md`.

---

## 19. Glossary

| Term | Definition |
|---|---|
| Auction round | One task-level evaluation of currently submitted bids. |
| Bid utility | Robot estimate of reward, urgency, reputation, strategy, energy, and risk value. |
| Coalition | Temporary team assigned to one cooperative task. |
| Contract | Assignment connecting one task with one robot or coalition. |
| Contract release | Removal of an assignment before successful completion. |
| Discovery | First successful local sensing of a task. |
| Formation delay | Time from task discovery to the most recent successful assignment. |
| Information coverage | Share of possible robot–task knowledge pairs currently present. |
| Mission incentive | Additional reward under the mission-aligned regime. |
| Reputation | Scalar reliability state used in learning and decision terms. |
| Strategy | Competitive, cooperative, or balanced bidding orientation. |
| Unresolved auction | Cooperative auction without a feasible coalition. |
| Verification | Internal consistency testing of implemented invariants. |
| Validation | Evidence that a model adequately represents an external target system. |

---

## 20. Related project documents

- `MOSAIC_ODD_Protocol_v0.4.0.md`
- `Experimental_Design.md`
- `Data_Dictionary.md`
- `Validation_Report.md`
- `MOSAIC_BS01_BS07_Integrated_Analysis.xlsx`
- `MOSAIC_Integrated_Findings.md`
- forthcoming `MOSAIC_Technical_Report_v0.4.0.pdf`

---

## 21. Authorship and use

MOSAIC was developed collaboratively by Katherin Molina and Lorena Holguin. Both authors must be credited in the public repository, model documentation, technical report, citation metadata, and releases.


MOSAIC, its original source code, interface design, documentation, experiment
design, and associated research materials were developed collaboratively by Katherin Molina and Lorena Holguin.

The NetLogo model and source code are licensed under Apache-2.0. This User
Manual is licensed under CC-BY-4.0. Reuse must preserve attribution to
Katherin Molina and Lorena Holguin.
