---
layout: page
title: Manual
permalink: /manual/
---

# QVAL Manual (User)

## Contents

1. [Overview](#1-overview)
2. [Running](#2-running)
3. [Config layout](#3-config-layout-config)
4. [YAML schema](#4-yaml-schema-brief)
5. [Parameter fields](#5-parameter-fields)
6. [Expression helpers](#6-expression-helpers)
7. [Compilation options](#7-compilation-options-opencl)
8. [I/O](#8-io)
9. [Report generation](#8-report-generation)
10. [Examples](#9-examples)
11. [Tests](#10-tests-small)
12. [Suite runner](#11-suite-runner)
13. [Multi-objective optimization](#12-multi-objective-weighted-sum)
14. [Precision control](#13-precision-control)
15. [Platform notes](#13-platform-notes-macos-fp64)
16. [Integer compute mode](#14-integer-compute-mode)
17. [Console output and verbosity](#15-console-output-and-verbosity)

## 1. Overview
QVAL is a GPU-accelerated function evaluator and optimizer.
It runs thousands to millions of parameter variants in parallel on the GPU and writes results (CSV/XLSX) and human-readable reports (txt/md/html/json). It supports multiple samplers, probability distributions, multi-dimensional parameters, and optimizers (CEM, DE).

You define your objective as a simple math expression string (expr), or, for advanced logic, as a C-like expression file (OpenCL code) via expr_file. QVAL compiles this into fast device code under the hood; you typically don’t need to write low-level kernels unless you want to.

Why GPU parallelism matters:
- Evaluate many parameter combinations at once (e.g., 1e5–1e6 samples) to explore search spaces quickly
- Keep wall-clock low by mapping each variant to a GPU thread
- Scale sampling/optimizers (LHS, Halton, CEM/DE) with high throughput

## 2. Running

List devices (look for GPU):
- macOS/Linux:
  ```bash
  ./qval --list-devices
  ```
- Windows:
  ```cmd
  qval.exe --list-devices
  ```

## 3. Config layout (config)
```
config/
├── example/                    # Real-world usage examples
│   ├── basic/                  # Basic optimization example
│   ├── batch/                  # Batch processing examples
│   ├── engineering/            # Engineering optimization
│   ├── finance/                # Financial modeling
│   ├── functions/              # Mathematical functions
│   ├── md/                     # Multi-dimensional examples
│   ├── multi/                  # Multi-objective optimization
│   ├── optimizers/             # Optimization algorithms
│   └── samplers/               # Sampling strategies
├── test/                       # Fast validation tests
│   ├── basic/                  # Basic functionality tests
│   ├── batch/                  # Batch processing tests
│   ├── builtins/               # Built-in function tests
│   ├── compilation/            # Compilation tests
│   ├── config/                 # Configuration tests
│   ├── dists/                  # Distribution tests
│   ├── expr/                   # Expression tests
│   ├── functions/              # Function tests
│   ├── gpu_verification/       # GPU verification tests
│   ├── int/                    # Integer parameter tests
│   ├── io/                     # Input/output tests
│   ├── md/                     # Multi-dimensional tests
│   ├── optimizers/             # Optimizer tests
│   ├── samplers/               # Sampler tests
│   └── verify/                 # Verification tests
├── test_slow/                  # Slow/GPU load tests
│   └── gpu_load/               # GPU stress tests
├── tutorial/                   # Step-by-step tutorials
│   ├── 00_minimal/             # Minimal example
│   ├── 01_expr_file/           # Expression files
│   ├── 02_csv_by_index/        # CSV input by index
│   ├── 03_csv_by_name/         # CSV input by name
│   ├── 04_categorical_enum/    # Categorical parameters
│   ├── 05_md_tensor/           # Multi-dimensional tensors
│   ├── 06_sampler_lhs/         # Latin Hypercube Sampling
│   ├── 07_verify_outputs/      # Output verification
│   ├── 08_cem_optimize/        # Cross-Entropy Method
│   ├── 09_de_optimize/         # Differential Evolution
│   ├── 10_precision_cosine/    # Precision control
│   ├── 11_builtins/            # Built-in functions
│   ├── data/                   # Tutorial data files
│   ├── expr/                   # Tutorial expressions
│   └── lay_people/             # Non-technical examples
└── user/                       # User-defined configurations
    └── README.md               # User guide
```

Example paths:
- `config/example/basic/basic.yaml` - Basic optimization example
- `config/tutorial/00_minimal/00_minimal.yaml` - Minimal tutorial
- `config/test/gpu_verification/comprehensive_gpu_test.yaml` - GPU verification

## 4. YAML schema (brief)
  expr | expr_file
  samples, top_k, goal, seed
  platform_id, device_id (optional)
- sampler: rng|lhs|halton|sobol (Sobol is multi-dimensional across eligible scalar per-variation float params; up to 8 dims)
  out_csv, out_xlsx (optional)
  search: { algo: cem|de, iters, batch, elite_frac }
  verify:                                   # optional, used by the test suite
    top_score: { ge: <float>, le: <float> } # bounds for the best score
    param_ranges:                           # check scalar parameters at best index
      - { name: P, min: <float>, max: <float> }
    outputs: { csv: true|false, xlsx: true|false }
  params: list of parameter definitions

Example:
```yaml
evaluate:
  expr: "X^2 + Y^2"
  samples: 1000
  top_k: 5
  goal: min
  sampler: lhs
  out_csv: "report/example/functions/quadratic/min.csv"
  params:
    - { name: X, type: float, dist: uniform, min: -2.0, max: 2.0, per_variation: true }
    - { name: Y, type: float, dist: uniform, min: -2.0, max: 2.0, per_variation: true }
```

## 5. Parameter fields
- name, type: float|int
- mode: range|const
- dist (range): uniform|min/max; normal|mean/std; trunc_normal|mean/std/min/max; triangular|min/mode/max; beta|alpha/beta/(min/max); exponential|lambda/(min/max); lognormal|log_mu/log_sigma; gamma|k/theta; cauchy|x0/gamma; categorical|values(/weights)
- shape: [d1, d2, d3] (optional)
- per_variation: true|false (defaults to true for scalars)
- enum/enum_values for ints (optional)
- csv: { path, col, header, col_name }
- xlsx: { path, sheet, range, col, header } (.xlsx only)

Examples:
- Float uniform range:
  - { name: A, type: float, dist: uniform, min: -1.0, max: 1.0, per_variation: true }
- Int constant:
  - { name: SPEED, type: int, mode: const, value: 2 }
- Int categorical enum:
  - name: MODE, type: int, dist: categorical, enum: [slow, med, fast]

## 6. Expression helpers
- Reductions: X_sum(), X_mean()
- Additional built-ins:
  - X_min(), X_max() — min/max over all elements (int and float compute)
  - X_var(), X_std(), X_norm2() — variance, standard deviation, L2 norm (float compute)
  - X_argmin(), X_argmax() — index of min/max (returned as a scalar; index base 0)
- Pairwise built-ins for float tensors with matching shapes:
  - dot_X_Y() — dot product of X and Y over elements
  - corr_X_Y() — Pearson correlation of X and Y over elements
- Global helpers: saturate(x) clamps to [0,1]
- MD accessors: X_at1(i), X_at2(i,j), X_at3(i,j,k)
- Rewrites: x^k => multiplies; abs/min/max => fabs/fmin/fmax

Examples:
- X^3 becomes X*X*X (float mode)
- abs(A) + max(B,C) valid in float; in integer compute mode use abs/min/max, %, and ^ (XOR) for ints
- Built-ins:
  - C_mean() + C_std() + C_min() + C_max()
  - A_argmin() (index of min in A)
  - dot_U_V() + corr_U_V() + saturate(A)

## 7. Compilation options (OpenCL)
- CLI:
  - --cl-define NAME=VAL (repeatable; if no =VAL, defaults to 1)
  - --cl-flag "-cl-opt-disable" (repeatable; raw flags appended to the OpenCL build options)
  - --cl-include <dir> (repeatable; adds -I <dir>)
- YAML (under evaluate.compile):
  - defines: [FOO=1, USE_BAR]
  - flags: ["-cl-opt-disable", ...]
  - includes: ["include", "/absolute/include"]
- Merging rules: CLI augments/overrides YAML; YAML includes are resolved relative to the YAML file when not absolute.

Examples:
- macOS/Linux:
  ./qval -cf config/example/functions/rosenbrock/min.yaml --cl-define FOO=1 --cl-flag -cl-opt-disable --cl-include include
- Windows (PowerShell):
  .\\qval.exe -cf config\\example\\functions\\rosenbrock\\min.yaml --cl-define FOO=1 --cl-flag -cl-opt-disable --cl-include include

See also: doc/advanced.md for more details.

## 8. I/O
- CSV read: one column => per-variation scalar parameter
- XLSX read (.xlsx): sheet+range+column => per-variation scalar parameter
- CSV/XLSX outputs (Top-K): if out_csv/out_xlsx is set in YAML, those paths are used. If out_csv is not set, QVAL writes a default CSV under output/<mirrored config subdir>/report/<yaml_basename>.csv.

Examples:
- CSV param:
  params:
    - name: VALUE
      type: float
      csv: { path: "config/test/io/csv/by_index/test.csv", col: 1, header: false }
      per_variation: true
- XLSX param:
  params:
    - name: X
      type: float
      xlsx: { path: "config/test/io/xlsx/simple/test.xlsx", sheet: "Sheet1", range: "A1:A11", col: 1, header: true }
      per_variation: true

## 8. Report generation
- Flags:
  - --report [fmt[,fmt2,...]] where fmt in {txt,md,html}
  - --report-out path (file for single fmt, or directory for multiple fmts)
  - --report-topk K (optional; defaults to top_k)
- Defaults: if you pass --report with no value, QVAL writes both txt and md to output/<mirrored config subdir>/report/report.{txt,md}.
- Content: header (config path, device, samples, goal, seed, compute type); if --perf is set, perf summary is included (wall, gpu, ratio, dev_mem, host_maxrss when available). Followed by a Top-K table with parameters (MD params reported as name_mean).

Examples:
- macOS/Linux:
  ```bash
  ./qval -cf config/tutorial/00_minimal/00_minimal.yaml --report
  
  ./qval -cf config/test/int/arith_small.yaml --compute int --report md --report-out report/my_report.md
  
  ./qval -cf config/tutorial/00_minimal/00_minimal.yaml --perf --report html
  ```
- Windows:
  ```cmd
  qval.exe -cf config\tutorial\00_minimal\00_minimal.yaml --report
  
  qval.exe -cf config\test\int\arith_small.yaml --compute int --report md --report-out report\my_report.md
  
  qval.exe -cf config\tutorial\00_minimal\00_minimal.yaml --perf --report html
  ```

## 9. Examples
- Functions: config/example/functions/rosenbrock/min.yaml
  - macOS/Linux:
    ```bash
    ./qval -cf config/example/functions/rosenbrock/min.yaml
    ```
  - Windows:
    ```cmd
    qval.exe -cf config\example\functions\rosenbrock\min.yaml
    ```
- Samplers: config/example/samplers/sphere8/{lhs,halton,sobol}.yaml
  - macOS/Linux:
    ```bash
    ./qval -cf config/example/samplers/sphere8/lhs.yaml
    ```
  - Windows:
    ```cmd
    qval.exe -cf config\example\samplers\sphere8\lhs.yaml
    ```
  - macOS/Linux:
    ```bash
    ./qval -cf config/example/samplers/sphere8/sobol.yaml
    ```
  - Windows:
    ```cmd
    qval.exe -cf config\example\samplers\sphere8\sobol.yaml
    ```
- Optimizers: config/example/optimizers/rosenbrock/{cem,de}.yaml
  - macOS/Linux:
    ```bash
    ./qval -cf config/example/optimizers/rosenbrock/cem.yaml
    ```
  - Windows:
    ```cmd
    qval.exe -cf config\example\optimizers\rosenbrock\cem.yaml
    ```
- MD: config/example/md/sphere_md.yaml
  - macOS/Linux:
    ```bash
    ./qval -cf config/example/md/sphere_md.yaml
    ```
  - Windows:
    ```cmd
    qval.exe -cf config\example\md\sphere_md.yaml
    ```
- Real: config/example/finance/opt/price_target.yaml
  - macOS/Linux:
    ```bash
    ./qval -cf config/example/finance/opt/price_target.yaml
    ```
  - Windows:
    ```cmd
    qval.exe -cf config\example\finance\opt\price_target.yaml
    ```

## 10. Tests (small)
- Samplers: config/test/samplers/sphere8/*_small.yaml
- Optimizers: config/test/optimizers/rosenbrock/*_small.yaml
- Dists: config/test/dists/*.yaml
- IO: config/test/io/csv/... and io/xlsx/simple/simple.yaml
- MD: config/test/md/*.yaml
- Expr file: config/test/expr/expr_file/expr_file.yaml

Example run:
- macOS/Linux:
  ```bash
  ./qval -cf config/test/io/csv/by_index/by_index.yaml
  ```
- Windows:
  ```cmd
  qval.exe -cf config\test\io\csv\by_index\by_index.yaml
  ```

## 11. Suite runner

### Quick Sanity Checks (in main directory)
- `run_basic_example.sh` (or `.bat`) - Basic example with multiple parameters (A, B, C, SPEED)
- `run_gpu_verification.sh` (or `.bat`) - Comprehensive GPU verification tests

### Comprehensive Testing (in util directory)
- `util/run_all_test.sh` (or `.bat`) - Run all test configurations (fast)
- `util/run_all_example.sh` (or `.bat`) - Run all example configurations
- `util/run_all_tutorial.sh` (or `.bat`) - Run all tutorial configurations
- `util/run_all_slow_test.sh` (or `.bat`) - Run all slow/GPU load tests
- `util/run_all_user.sh` (or `.bat`) - Run all user configurations
- `util/run_gpu_verification.sh` (or `.bat`) - Comprehensive GPU verification

### Legacy Suite Commands
- macOS/Linux:
  ```bash
  ./qval --run-suite config/test
  
  ./qval --run-suite config/test --include-slow
  ```
- Windows:
  ```cmd
  qval.exe --run-suite config\test
  
  qval.exe --run-suite config\test --include-slow
  ```

### GPU Verification
The GPU verification script runs two comprehensive tests:
1. **Comprehensive Test**: 50K samples with multiple parameter types and distributions
2. **Stress Test**: 100K samples with complex mathematical operations

Both tests verify that your GPU is working correctly with QVAL and provide pass/fail feedback.

- PASS: Tests must include a verify block under evaluate.verify; the program exits non-zero on failure.

## 12. Multi-objective (weighted sum)
- YAML block under evaluate.multi:
```yaml
evaluate:
  multi:
    type: weighted_sum
    exprs:
      - { expr: "(X-1)^2 + (Y-1)^2", weight: 0.5, goal: min }
      - { expr: "(X+1)^2 + (Y+1)^2", weight: 0.5, goal: min }
```
- Behavior: synthesizes a single combined expression, converts min terms to -expr, sets overall goal to max, and optimizes with the usual modes (base/CEM/DE).
- Example:
  - macOS/Linux:
    ```bash
    ./qval -cf config/example/multi/weighted_sum.yaml --report md --report-out report/example/multi
    ```
  - Windows:
    ```cmd
    qval.exe -cf config\example\multi\weighted_sum.yaml --report md --report-out report\example\multi
    ```

## 13. Precision control

Examples:
- macOS/Linux (default float32 with perf):
  ```bash
  ./qval -cf config/tutorial/00_minimal/00_minimal.yaml --perf --perf-txt report/perf/minimal_f32.txt
  ```
- Windows:
  ```cmd
  qval.exe -cf config\tutorial\00_minimal\00_minimal.yaml --perf --perf-txt report\perf\minimal_f32.txt
  ```
- Force double precision (error if unsupported):
  ```bash
  ./qval -cf config/tutorial/00_minimal/00_minimal.yaml --precision double --no-fallback --perf
  ```
- Flag: --precision {float|float32|fp32|half|float16|fp16|double|float64|fp64}
  - Default: float32
  - half/float16 requires cl_khr_fp16 on the selected device
  - double/float64 requires cl_khr_fp64 on the selected device
- Fallback vs strict
  - By default, if the device lacks the requested precision, QVAL falls back to float32 and prints a warning.
- To enforce strict precision, add --no-fallback. If unsupported, kernel build will likely fail (by design) so the run exits with an error.
- Compute vs I/O
  - Inputs remain float buffers; the kernel casts to the requested precision for computation (via an internal typedef)
  - The output buffer remains float; kernel casts back to float when storing the result
- Examples
  - Default (float32):
    ```bash
    ./qval -cf config/tutorial/00_minimal/00_minimal.yaml --perf --perf-txt report/perf/minimal_f32.txt
    ```
  - Force double precision (error if unsupported):
    ```bash
    ./qval -cf config/tutorial/00_minimal/00_minimal.yaml --precision double --no-fallback --perf
    ```
  - Try half precision:
    ```bash
    ./qval -cf config/tutorial/00_minimal/00_minimal.yaml --precision half --perf
    ```

## 13. Platform notes (macOS fp64)
- Apple Silicon GPU OpenCL does not expose cl_khr_fp64. Using --precision double will fall back to float32 unless you add --no-fallback (which may cause kernel build or runtime failures depending on the driver).
- If an OpenCL CPU device is present and supports cl_khr_fp64, select it with --platform/--device and run with --precision double [--no-fallback].

## 14. Integer compute mode
- Flags: --compute int [--int-precision {int8,int16,int32,int64,int4}]
- Default: int32; int4 is emulated via int8 with a warning.
- Behavior:
  - Inputs remain float buffers; the kernel casts to FNEV_T (the chosen integer type) via typedef for computation.
  - Output remains float; the kernel casts back from FNEV_T to float for results.
- Supported ops: +, -, *, integer /, %, abs, min/max, comparisons, ternary ?:
- Pow/exp/trig are not supported in integer mode unless you provide explicit integer-safe logic. The pow(a,b) inline optimization (for small integer b) applies to float mode only.

Examples:
- macOS/Linux:
  ```bash
  ./qval -cf config/test/dists/categorical_enum.yaml --compute int --int-precision int32
  ```
- Windows:
  ```cmd
  qval.exe -cf config\test\dists\categorical_enum.yaml --compute int --int-precision int32
  ```

## 15. Console output and verbosity
- Top-K display: QVAL prints a compact, aligned table of the top results (rank, index, score, and each parameter; MD params show name_mean).
- --quiet: suppresses less-useful chatter (e.g., the "kernel saved to:" line) to keep output focused.
- --verbose: enables extra OpenCL device detail; notably it shows the full OpenCL extension string. By default, extensions are hidden.

Examples:
- macOS/Linux (quiet run):
  ```bash
  ./qval -cf config/test/dists/normal.yaml --quiet
  ```
- macOS/Linux (verbose device details including OpenCL extensions):
  ```bash
  ./qval -cf config/test/dists/normal.yaml --verbose
  ```
- Windows:
  ```cmd
  qval.exe -cf config\test\dists\normal.yaml --quiet
  
  qval.exe -cf config\test\dists\normal.yaml --verbose
  ```

