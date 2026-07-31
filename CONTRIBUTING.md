# Contributing to MOSAIC

Thank you for your interest in MOSAIC.

## Current project status

MOSAIC v0.4.0 is a research release. Contributions should preserve
reproducibility, traceability, and compatibility with the documented model
architecture.

## Before proposing a change

1. Read `README.md`.
2. Read the relevant section of the ODD Protocol.
3. Open the model in NetLogo 7.0.4.
4. Run the validated baseline.
5. Run `RUN CHECKS`.
6. Confirm `OVERALL RESULT: PASS`.

## Contribution types

Useful contributions include:

- bug reports;
- reproducibility reports;
- documentation corrections;
- additional verification checks;
- new BehaviorSpace experiments;
- carefully scoped model extensions;
- analysis improvements;
- accessibility and usability improvements.

## Issues

A useful issue should include:

- model version;
- NetLogo version;
- operating system;
- random seed;
- parameter values;
- expected behaviour;
- observed behaviour;
- reproduction steps;
- Command Center output;
- screenshots or exported diagnostics when relevant.

## Pull requests

A pull request should:

- describe the research or engineering purpose;
- identify changed procedures and variables;
- preserve English naming and documentation;
- update the ODD Protocol when model mechanisms change;
- update the User Manual when operation changes;
- update the changelog;
- include regression evidence;
- pass the nine model checks;
- avoid modifying official raw data.

## Experimental data

Files under `experiments/raw/` are immutable evidence.

Do not:

- edit or re-save official raw BehaviorSpace exports;
- remove NetLogo metadata;
- change raw headers;
- overwrite official BS01–BS07 files;
- reuse an existing experiment identifier for a different design.

New experiments should begin with `BS08` and document:

- research question;
- manipulated parameters;
- fixed parameters;
- random-seed design;
- number of runs;
- reporters;
- stopping condition;
- analysis plan.

## Licensing of contributions

By submitting a contribution, you agree that it may be distributed under the
licence applicable to the contributed material:

- Apache-2.0 for model and source-code contributions;
- CC-BY-4.0 for original documentation, figures, datasets, and analytical
  materials.

Do not submit material that you do not have the right to license.

## Attribution

The project authors are:

- Katherin Molina;
- Lorena Holguin.

Additional contributors should be acknowledged according to the nature and
substance of their contribution. Authorship changes require explicit agreement
among the project authors.
