# MOSAIC v0.4.0 Model Inclusion and Static Validation

## Included model

`model/MOSAIC_v0.4.0.nlogox`

## Static validation result

**PASS**

The model is valid NetLogo 7 XML and has been screened for release-level
version, interface-default, label, and English-language consistency.

## Corrections incorporated before the v0.4.0 public release

- Updated all remaining internal version references from 0.3.0 to 0.4.0.
- Restored communication-quality-threshold default to 0.25.
- Set maximum-coalition-size default to 3 with an integer step of 1.



## Validation checks

| Check | Result | Observed value |
|---|---|---|
| Declared NetLogo version | PASS | NetLogo 7.0.4 |
| No remaining 0.3.0 references | PASS | 0 |
| Code and Info use 0.4.0 | PASS | 4 |
| Random seed default | PASS | 42 |
| Communication threshold default | PASS | 0.25 |
| Communication threshold step | PASS | 0.05 |
| Leader reward share step | PASS | 0.05 |
| Maximum coalition size default | PASS | 3 |
| Maximum coalition size integer step | PASS | 1 |
| Contract Releases monitor | PASS | Present |
| Information Coverage capitalization | PASS | Present |
| Repository placeholder is explicit | PASS | Present |

## Runtime release gate

Static validation does not replace a final NetLogo runtime test. Before the
public GitHub release, open the included file in NetLogo 7.0.4 from a clean
session and run:

1. `random-seed-value = 42`
2. `SETUP`
3. `GO`
4. `RUN CHECKS`

The frozen v0.4.0 model completed the documented runtime verification and
reported `OVERALL RESULT: PASS`.