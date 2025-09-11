---
layout: default
title: Examples
---

# Real-world Examples

Explore QVAL's capabilities through practical examples demonstrating various optimization scenarios, sampling strategies, and advanced features.

---

## Function Optimization

### Rosenbrock Function Minimization
Optimize the classic Rosenbrock function, a challenging non-convex optimization problem commonly used to test optimization algorithms.

```yaml
evaluate:
  expr: "100*(Y-X^2)^2 + (1-X)^2"
  samples: 50000
  top_k: 10
  goal: min
  sampler: lhs
  
  params:
    - name: X
      type: float
      dist: uniform
      min: -2.0
      max: 2.0
      per_variation: true
    - name: Y
      type: float
      dist: uniform
      min: -1.0
      max: 3.0
      per_variation: true
```

**Use Case**: Engineering design optimization, algorithm benchmarking
**Expected Result**: Global minimum near (1.0, 1.0) with score ≈ 0.0

---

## Multi-Dimensional Sampling

### 8D Sphere Sampling with Sobol Sequences
Demonstrate high-dimensional parameter space exploration using quasi-random Sobol sequences for better coverage than random sampling.

```yaml
evaluate:
  expr: "X1^2 + X2^2 + X3^2 + X4^2 + X5^2 + X6^2 + X7^2 + X8^2"
  samples: 100000
  top_k: 20
  goal: min
  sampler: sobol
  
  params:
    - name: X1
      type: float
      dist: uniform
      min: -1.0
      max: 1.0
    - name: X2
      type: float
      dist: uniform
      min: -1.0
      max: 1.0
    # ... continues for X3-X8 ...
```

**Use Case**: High-dimensional optimization, Monte Carlo simulations
**Key Feature**: Sobol sequences provide better space-filling properties than random sampling

---

## Cross-Entropy Method Optimization

### Portfolio Risk Optimization
Use the Cross-Entropy Method to optimize a financial portfolio with risk constraints and target returns.

```yaml
evaluate:
  expr: "W1*R1 + W2*R2 + W3*R3 - LAMBDA*(W1*W1*V1 + W2*W2*V2 + W3*W3*V3 + 2*W1*W2*C12 + 2*W1*W3*C13 + 2*W2*W3*C23)"
  samples: 10000
  top_k: 5
  goal: max
  sampler: lhs
  
  search:
    algo: cem
    iters: 25
    batch: 2000
    elite_frac: 0.2
  
  params:
    - name: W1
      type: float
      dist: uniform
      min: 0.0
      max: 1.0
    - name: W2
      type: float
      dist: uniform
      min: 0.0
      max: 1.0
    - name: W3
      type: float
      dist: uniform
      min: 0.0
      max: 1.0
    - name: LAMBDA
      type: float
      mode: const
      value: 0.5
    # Market parameters as constants
    - name: R1
      type: float
      mode: const
      value: 0.12
    - name: R2
      type: float
      mode: const
      value: 0.08
    - name: R3
      type: float
      mode: const
      value: 0.15
```

**Use Case**: Financial portfolio optimization, risk management
**Algorithm**: Cross-Entropy Method iteratively improves the sampling distribution

---

## Multi-Objective Optimization

### Engineering Design Trade-off
Balance multiple conflicting objectives using weighted sum aggregation for engineering design optimization.

```yaml
evaluate:
  multi:
    type: weighted_sum
    exprs:
      - expr: "THICKNESS^2 * LENGTH"  # Minimize material cost
        weight: 0.4
        goal: min
      - expr: "1000 / (THICKNESS * WIDTH^2)"  # Minimize deflection (maximize stiffness)
        weight: 0.6
        goal: min
  
  samples: 25000
  top_k: 15
  goal: max  # Automatically set by multi-objective
  sampler: lhs
  
  params:
    - name: THICKNESS
      type: float
      dist: uniform
      min: 0.5
      max: 5.0
    - name: WIDTH
      type: float
      dist: uniform
      min: 10.0
      max: 50.0
    - name: LENGTH
      type: float
      mode: const
      value: 100.0
```

**Use Case**: Structural engineering, multi-criteria decision making
**Feature**: Automatically combines multiple objectives with user-defined weights

---

## Advanced Parameter Types

### Signal Processing Filter Design
Optimize FIR filter coefficients using multi-dimensional parameters and CSV data input.

```yaml
evaluate:
  expr_file: "filter_response.cl"
  samples: 20000
  top_k: 5
  goal: min
  sampler: halton
  
  params:
    - name: COEFFS
      type: float
      shape: [8]
      dist: uniform
      min: -1.0
      max: 1.0
    - name: TARGET_FREQ
      type: float
      csv:
        path: "data/target_frequencies.csv"
        col: 1
        header: true
      per_variation: true
    - name: SAMPLE_RATE
      type: float
      mode: const
      value: 44100.0
```

**Use Case**: Digital signal processing, filter design
**Features**: Multi-dimensional parameters, external data input, custom OpenCL expressions

---

## Statistical Distribution Examples

### Manufacturing Quality Control
Model manufacturing process parameters with realistic probability distributions for quality optimization.

```yaml
evaluate:
  expr: "abs(DIAMETER - TARGET) + 0.1*ROUGHNESS + 0.05*abs(PRESSURE - OPTIMAL_PRESSURE)"
  samples: 15000
  top_k: 10
  goal: min
  sampler: lhs
  
  params:
    - name: DIAMETER
      type: float
      dist: normal
      mean: 10.0
      std: 0.2
    - name: ROUGHNESS
      type: float
      dist: lognormal
      log_mu: 0.0
      log_sigma: 0.5
    - name: PRESSURE
      type: float
      dist: triangular
      min: 80.0
      mode: 100.0
      max: 120.0
    - name: TARGET
      type: float
      mode: const
      value: 10.0
    - name: OPTIMAL_PRESSURE
      type: float
      mode: const
      value: 100.0
```

**Use Case**: Manufacturing optimization, quality control, process improvement
**Distributions**: Normal, log-normal, triangular for realistic parameter modeling

---

## Performance Benchmarking

### GPU Thread Stress Test
Benchmark GPU performance with large-scale parallel evaluations for system testing.

```yaml
evaluate:
  expr: "sin(X*3.14159/180) * cos(Y*3.14159/180) + sqrt(abs(Z))"
  samples: 1000000
  top_k: 100
  goal: max
  sampler: rng
  
  search:
    algo: de
    iters: 10
    batch: 100000
    elite_frac: 0.1
  
  params:
    - name: X
      type: float
      dist: uniform
      min: -180.0
      max: 180.0
    - name: Y
      type: float
      dist: uniform
      min: -90.0
      max: 90.0
    - name: Z
      type: float
      dist: uniform
      min: -100.0
      max: 100.0
```

**Use Case**: Performance benchmarking, GPU capability testing
**Scale**: 1M evaluations with Differential Evolution optimizer

---

## Getting Started with Examples

### Running Examples

```bash
# macOS/Linux - Run basic optimization
./qval --config config/example/functions/rosenbrock/min.yaml --report txt,md

# Windows - Run portfolio optimization
.\qval.exe --config config\example\finance\opt\price_target.yaml --report html

# Generate performance reports
./qval --config config/example/optimizers/rosenbrock/cem.yaml --perf --report-out ./reports/
```

### Configuration Tips

1. **Start Small**: Begin with 1K-10K samples for testing, scale up for production
2. **Choose Appropriate Samplers**: 
   - Use `lhs` for general optimization
   - Use `sobol` for high-dimensional problems (≤8D)
   - Use `halton` for quasi-random sequences
3. **Set Realistic Constraints**: Ensure parameter ranges cover the feasible space
4. **Monitor GPU Memory**: Large sample counts require sufficient GPU memory

### Next Steps

- Explore the complete [manual](manual.html) for detailed configuration options
- Check out [config/tutorial/](https://github.com/mdx1358/qval/tree/main/config/tutorial) for step-by-step tutorials
- Review [config/test/](https://github.com/mdx1358/qval/tree/main/config/test) for validation examples

<style>
.example-section {
  margin: 40px 0;
  padding: 25px;
  border-left: 4px solid #667eea;
  background: #f8f9fa;
  border-radius: 0 8px 8px 0;
}

.example-section h3 {
  color: #2c3e50;
  margin-top: 0;
}

.example-meta {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 15px;
  margin: 15px 0;
  padding: 15px;
  background: white;
  border-radius: 6px;
  font-size: 0.9em;
}

.example-meta div {
  padding: 8px 12px;
  background: #e9ecef;
  border-radius: 4px;
}

.example-meta div strong {
  display: block;
  color: #495057;
  margin-bottom: 3px;
}

code {
  background-color: #f8f9fa;
  border: 1px solid #e9ecef;
  border-radius: 4px;
  padding: 2px 4px;
  font-size: 0.9em;
}

pre {
  background-color: #f8f9fa;
  border: 1px solid #e9ecef;
  border-radius: 6px;
  padding: 16px;
  overflow-x: auto;
}

.highlight pre {
  background-color: #f8f9fa;
}
</style>
