---
layout: default
title: Manual
---

<main style="padding: 6rem 0; background: linear-gradient(180deg, var(--bg-color) 0%, var(--card-bg) 100%); color: var(--text-color);">
  <div class="container" style="max-width: 1000px; margin: 0 auto; padding: 0 2rem;" markdown="1">

# QVAL Manual

Complete user documentation for QVAL - GPU-accelerated function evaluator and optimizer.

## Table of Contents

- [Overview](#overview)
- [Installation](#installation)
- [Quick Start](#quick-start)  
- [Configuration](#configuration)
- [Command Line Options](#command-line-options)
- [Parameter Types](#parameter-types)
- [Expressions](#expressions)
- [Sampling Strategies](#sampling-strategies)
- [Optimization Algorithms](#optimization-algorithms)
- [Multi-Objective Optimization](#multi-objective-optimization)
- [Input/Output](#inputoutput)
- [Report Generation](#report-generation)
- [Performance Tuning](#performance-tuning)
- [Troubleshooting](#troubleshooting)

## Overview

QVAL is a GPU-accelerated function evaluator and optimizer that runs thousands to millions of parameter variants in parallel on the GPU. It supports multiple samplers, probability distributions, multi-dimensional parameters, and optimizers (CEM, DE).

### Why GPU Parallelism Matters

- **Evaluate many parameter combinations simultaneously** (e.g., 100K–1M+ samples) to explore search spaces quickly
- **Keep wall-clock time low** by mapping each variant to a GPU thread
- **Scale sampling/optimization algorithms** (LHS, Halton, Sobol, CEM/DE) with high throughput

## Installation

### Pre-built Binaries

Download the appropriate package for your platform:

- **macOS**: Universal binary supporting both Intel and Apple Silicon
- **Linux**: x64 binary for modern Linux distributions
- **Windows**: x64 binary for Windows 10+

### System Requirements

- **GPU**: OpenCL 1.2+ compatible device (NVIDIA, AMD, Intel, Apple Silicon)
- **Memory**: 4GB+ RAM recommended for large-scale evaluations
- **Storage**: 500MB+ available space

## Quick Start

### 1. Check GPU Availability

```bash
# macOS/Linux
./qval --list-devices

# Windows (PowerShell)  
.\qval.exe --list-devices
```

Look for GPU devices in the output. QVAL performs best on dedicated GPUs.

### 2. Run Basic Example

```bash
# macOS/Linux
./qval --config config/tutorial/00_minimal/00_minimal.yaml

# Windows
.\qval.exe --config config\tutorial\00_minimal\00_minimal.yaml
```

### 3. Generate Reports

```bash
# Create comprehensive reports
./qval --config config/tutorial/00_minimal/00_minimal.yaml --report txt,md,html
```

## Configuration

QVAL uses YAML configuration files to define optimization problems. Here's the basic structure:

```yaml
evaluate:
  expr: "X^2 + Y^2"        # Math expression to evaluate
  samples: 10000           # Number of parallel evaluations
  top_k: 10               # Best results to return  
  goal: min               # Minimize or maximize
  sampler: lhs            # Sampling strategy
  
  params:
    - name: X
      type: float
      dist: uniform
      min: -5.0
      max: 5.0
      per_variation: true
    - name: Y
      type: float  
      dist: uniform
      min: -5.0
      max: 5.0
      per_variation: true
```

### Required Fields

- **`expr` or `expr_file`**: Math expression or OpenCL file
- **`samples`**: Number of parallel evaluations
- **`top_k`**: Number of best results to return
- **`goal`**: `min` or `max`
- **`params`**: List of parameter definitions

### Optional Fields

- **`sampler`**: `rng`, `lhs`, `halton`, `sobol` (default: `rng`)
- **`seed`**: Random seed for reproducibility
- **`platform_id`**, **`device_id`**: Specific OpenCL device selection
- **`out_csv`**, **`out_xlsx`**: Output file paths
- **`search`**: Optimization algorithm configuration

## Command Line Options

### Basic Options

- **`--config PATH`** or **`-cf PATH`**: Configuration file path
- **`--list-devices`**: Show available OpenCL devices
- **`--device-auto`**: Automatically select best GPU device
- **`--help`**: Show help information
- **`--version`**: Show version information

### Device Selection

- **`--platform ID`**: Select OpenCL platform by ID
- **`--device ID`**: Select OpenCL device by ID  
- **`--device-auto`**: Auto-select best available GPU

### Precision Control

- **`--precision TYPE`**: Set compute precision (`float`, `double`, `half`)
- **`--no-fallback`**: Strict precision (fail if unsupported)
- **`--compute int`**: Integer compute mode
- **`--int-precision TYPE`**: Integer precision (`int8`, `int16`, `int32`, `int64`)

### OpenCL Compilation

- **`--cl-define NAME=VALUE`**: Add OpenCL preprocessor define
- **`--cl-flag FLAG`**: Add OpenCL compiler flag
- **`--cl-include PATH`**: Add OpenCL include directory

### Output and Reports

- **`--report FORMATS`**: Generate reports (`txt`, `md`, `html`)
- **`--report-out PATH`**: Output path for reports
- **`--report-topk N`**: Number of results in report
- **`--perf`**: Include performance metrics
- **`--perf-txt PATH`**: Save performance to text file

### Verbosity

- **`--quiet`**: Suppress non-essential output
- **`--verbose`**: Show detailed information

### Test Suite

- **`--run-suite PATH`**: Run test suite from directory
- **`--include-slow`**: Include slow tests in suite

## Parameter Types

### Scalar Parameters

```yaml
params:
  # Float uniform distribution
  - name: X
    type: float
    dist: uniform
    min: -1.0
    max: 1.0
    per_variation: true
    
  # Integer constant
  - name: COUNT
    type: int
    mode: const
    value: 100
    
  # Normal distribution
  - name: NOISE
    type: float
    dist: normal
    mean: 0.0
    std: 0.1
```

### Multi-Dimensional Parameters

```yaml
params:
  # 2D array parameter
  - name: MATRIX
    type: float
    shape: [3, 3]
    dist: uniform
    min: -1.0
    max: 1.0
    
  # 1D vector parameter  
  - name: COEFFS
    type: float
    shape: [8]
    dist: normal
    mean: 0.0
    std: 1.0
```

### Supported Distributions

- **`uniform`**: `min`, `max`
- **`normal`**: `mean`, `std` 
- **`trunc_normal`**: `mean`, `std`, `min`, `max`
- **`triangular`**: `min`, `mode`, `max`
- **`beta`**: `alpha`, `beta`, optional `min`/`max`
- **`exponential`**: `lambda`, optional `min`/`max`
- **`lognormal`**: `log_mu`, `log_sigma`
- **`gamma`**: `k`, `theta`
- **`cauchy`**: `x0`, `gamma`
- **`categorical`**: `values`, optional `weights`

### External Data Input

```yaml
params:
  # CSV input
  - name: DATA_POINT
    type: float
    csv:
      path: "data/input.csv" 
      col: 1
      header: true
    per_variation: true
    
  # Excel input
  - name: PARAMETER
    type: float
    xlsx:
      path: "data/params.xlsx"
      sheet: "Sheet1"
      range: "A1:A100"
      col: 1
      header: true
    per_variation: true
```

## Expressions

### Simple Math Expressions

```yaml
evaluate:
  expr: "X^2 + Y^2"           # Basic arithmetic
  expr: "sin(X) + cos(Y)"     # Trigonometric functions
  expr: "sqrt(X^2 + Y^2)"     # Square root
  expr: "abs(X - Y)"          # Absolute value
  expr: "max(X, 0) + min(Y, 1)" # Min/max functions
```

### Built-in Functions

#### Reduction Functions
- **`X_sum()`**: Sum of all elements in parameter X
- **`X_mean()`**: Mean of all elements
- **`X_min()`, `X_max()`**: Min/max values
- **`X_var()`, `X_std()`**: Variance and standard deviation
- **`X_norm2()`**: L2 norm
- **`X_argmin()`, `X_argmax()`**: Index of min/max element

#### Pairwise Functions
- **`dot_X_Y()`**: Dot product of X and Y
- **`corr_X_Y()`**: Pearson correlation of X and Y

#### Multi-Dimensional Accessors
- **`X_at1(i)`**: Access 1D array element
- **`X_at2(i,j)`**: Access 2D array element  
- **`X_at3(i,j,k)`**: Access 3D array element

#### Utility Functions
- **`saturate(x)`**: Clamp value to [0,1] range

### Advanced OpenCL Expressions

For complex logic, use OpenCL expression files:

```yaml
evaluate:
  expr_file: "custom_logic.cl"
```

**Example `custom_logic.cl`:**
```c
// Custom OpenCL kernel code
FNEV_T result = 0.0f;
for(int i = 0; i < 8; i++) {
    result += COEFFS_at1(i) * sin(i * X * M_PI / 4.0f);
}
return result;
```

## Sampling Strategies

### Uniform Random Sampling (`rng`)

Standard pseudorandom sampling with uniform distribution.

```yaml
evaluate:
  sampler: rng
  seed: 12345  # Optional: for reproducibility
```

**Best for**: General-purpose sampling, Monte Carlo simulations

### Latin Hypercube Sampling (`lhs`)

Space-filling design that ensures even coverage across parameter ranges.

```yaml
evaluate:
  sampler: lhs
```

**Best for**: Design of experiments, optimization with limited samples

### Halton Sequences (`halton`)

Low-discrepancy quasi-random sequences for better space coverage.

```yaml
evaluate:
  sampler: halton
```

**Best for**: Quasi-Monte Carlo methods, systematic space exploration

### Sobol Sequences (`sobol`)

Multi-dimensional quasi-random sequences (up to 8 dimensions).

```yaml
evaluate:
  sampler: sobol
```

**Best for**: High-dimensional problems, improved convergence over random sampling

**Note**: Sobol sequences work with up to 8 scalar float parameters with `per_variation: true`.

## Optimization Algorithms

### Cross-Entropy Method (CEM)

Iteratively improves sampling distribution based on elite samples.

```yaml
evaluate:
  search:
    algo: cem
    iters: 50           # Number of iterations
    batch: 5000         # Samples per iteration  
    elite_frac: 0.1     # Top 10% as elite samples
```

**Best for**: Continuous optimization, noisy objective functions

### Differential Evolution (DE)

Population-based evolutionary algorithm with mutation and crossover.

```yaml
evaluate:
  search:
    algo: de
    iters: 100          # Generations
    batch: 1000         # Population size
    elite_frac: 0.3     # Selection pressure
```

**Best for**: Global optimization, multimodal functions

## Multi-Objective Optimization

Optimize multiple conflicting objectives using weighted sum aggregation.

```yaml
evaluate:
  multi:
    type: weighted_sum
    exprs:
      - expr: "X^2 + Y^2"           # Objective 1
        weight: 0.6
        goal: min
      - expr: "abs(X - Y)"          # Objective 2  
        weight: 0.4
        goal: min
```

**Features**:
- Automatically combines objectives with user-defined weights
- Converts minimization terms and sets overall goal
- Works with all sampling strategies and optimizers

## Input/Output

### CSV Output

```yaml
evaluate:
  out_csv: "results/optimization_results.csv"
```

Generates CSV with columns: `rank`, `index`, `score`, and each parameter.

### Excel Output

```yaml
evaluate:
  out_xlsx: "results/optimization_results.xlsx"
```

Creates Excel file with formatted results including metadata.

### Default Output Paths

If no output path specified, results are saved to:
```
bin/output/<config_subdir>/report/<config_name>.csv
```

## Report Generation

### Command Line Reports

```bash
# Generate text report
./qval --config config.yaml --report txt

# Multiple formats
./qval --config config.yaml --report txt,md,html

# Custom output location
./qval --config config.yaml --report md --report-out ./reports/
```

### Report Contents

Reports include:
- **Configuration summary**: File path, device info, parameters
- **Performance metrics**: Wall time, GPU time, memory usage (with `--perf`)
- **Top-K results table**: Best parameter combinations and scores
- **Parameter statistics**: For multi-dimensional parameters

### Performance Reports

```bash
# Include performance metrics
./qval --config config.yaml --perf --report txt

# Save performance to separate file
./qval --config config.yaml --perf-txt performance.log
```

## Performance Tuning

### GPU Selection

1. **List available devices**:
   ```bash
   ./qval --list-devices
   ```

2. **Auto-select best GPU**:
   ```bash
   ./qval --config config.yaml --device-auto
   ```

3. **Manual device selection**:
   ```bash
   ./qval --config config.yaml --platform 0 --device 1
   ```

### Sample Count Optimization

- **Start small** (1K-10K samples) for testing
- **Scale up gradually** based on problem complexity
- **Monitor GPU memory usage** for very large sample counts
- **Consider batch processing** for memory-constrained scenarios

### Precision Selection

```bash
# Default float32 (recommended)
./qval --config config.yaml

# Double precision (if supported)
./qval --config config.yaml --precision double

# Half precision (faster, less accurate)  
./qval --config config.yaml --precision half
```

### Integer Compute Mode

```bash
# Integer arithmetic
./qval --config config.yaml --compute int --int-precision int32
```

**Note**: Limited to basic arithmetic operations (+, -, *, /, %, abs, min, max).

## Troubleshooting

### Common Issues

#### GPU Not Found
```
Error: No OpenCL devices found
```
**Solution**: Install GPU drivers, check OpenCL runtime installation.

#### Out of Memory
```
Error: CL_MEM_OBJECT_ALLOCATION_FAILURE
```
**Solution**: Reduce sample count, use smaller batch sizes, or switch to CPU device.

#### Kernel Build Failure
```
Error: OpenCL kernel compilation failed
```
**Solution**: Check expression syntax, review `build_error.log` in output directory.

#### Precision Not Supported
```
Warning: Double precision not supported, falling back to float32
```
**Solution**: Use `--no-fallback` to enforce strict precision, or accept fallback.

### Performance Issues

#### Slow Execution
- Check if running on CPU instead of GPU
- Verify sufficient GPU memory available
- Reduce expression complexity
- Use appropriate sampler for problem size

#### Poor Optimization Results  
- Increase iteration count for CEM/DE
- Adjust parameter ranges to cover feasible space
- Try different sampling strategies
- Verify objective function correctness

### Debug Options

```bash
# Verbose output with device details
./qval --config config.yaml --verbose

# Quiet output for batch processing
./qval --config config.yaml --quiet

# Performance profiling
./qval --config config.yaml --perf --perf-txt debug.log
```

### Getting Help

- **Check example configurations** in `config/example/` and `config/tutorial/`
- **Run test suite** to verify installation: `./qval --run-suite config/test`
- **Review manual sections** for specific features
- **Examine generated `build_error.log`** for compilation issues

  </div>
</main>
