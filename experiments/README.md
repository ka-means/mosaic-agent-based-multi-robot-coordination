# MOSAIC Experimental Results

This directory contains the complete experimental evidence package for
MOSAIC v0.4.0.

## Dataset scope

- Seven BehaviorSpace experiments
- Thirty paired random seeds per condition
- 690 official runs
- Final metrics recorded once per run
- NetLogo 7.0.4 BehaviorSpace spreadsheet exports

## Directory structure

- `raw/`: immutable BehaviorSpace exports
- `processed/`: clean run-level CSV files and the harmonized master dataset
- `summaries/`: experiment summaries and integrated findings
- `Experimental_Design.md`: experimental factors, conditions, and research purpose
- `Data_Dictionary.md`: definitions of the harmonized variables
- `Validation_Report.md`: run-count and source-validation results
- `Source_File_Mapping.csv`: mapping from original uploaded names to canonical names

## Data immutability

Files under `raw/` should not be edited. Transformations must be performed on
copies and written to `processed/` or `summaries/`.

The official release evidence contains 690 runs. Any later experiment should
receive a new identifier, beginning with `BS08`, and must not overwrite the
BS01–BS07 files.

## Verification scope

The built-in `RUN CHECKS` procedure evaluates nine invariants and returned an
overall PASS in verification runs. Five verification indicators were also
recorded as run-level reporters across the official BehaviorSpace datasets:
task-state consistency, energy accounting, reward accounting, reputation
bounds, and strategy validity.

## Licence

The original data, summaries, documentation, and analytical materials in this directory are licensed under the Creative Commons Attribution 4.0 International licence. Attribution must be given to Katherin Molina and Lorena Holguin.
