---
layout: default
title: Home
---

# QVAL - GPU-Accelerated Function Evaluator and Optimizer

QVAL is a high-performance, GPU-accelerated function evaluator and optimizer designed for parallel exploration of parameter spaces. It leverages GPU parallelism to evaluate thousands to millions of parameter combinations simultaneously, making it ideal for optimization problems, parameter sweeps, and Monte Carlo simulations.

## 🚀 Key Features

- **GPU-Accelerated Performance**: Parallel evaluation of up to millions of parameter combinations using OpenCL
- **Flexible Expression Engine**: Define objectives using simple math expressions or advanced C-like OpenCL code  
- **Multiple Sampling Strategies**: LHS, Halton sequences, Sobol sequences, and uniform random sampling
- **Advanced Optimizers**: Cross-Entropy Method (CEM) and Differential Evolution (DE) built-in
- **Multi-Objective Support**: Weighted sum aggregation for multiple objectives
- **Rich Parameter Types**: Scalars, multi-dimensional arrays, and various probability distributions
- **Comprehensive I/O**: CSV/XLSX input/output with customizable report generation
- **Cross-Platform**: Native support for macOS (Apple Silicon), Windows support coming soon

## 🔄 How It Works

<div class="steps-container">
  <div class="step-card">
    <h3>1. Define Problem</h3>
    <p>Write your optimization problem in simple YAML configuration with math expressions</p>
  </div>
  <div class="step-card">
    <h3>2. Choose Strategy</h3>
    <p>Select sampling method (LHS, Sobol, Halton) and optimizer (CEM, DE) for your use case</p>
  </div>
  <div class="step-card">
    <h3>3. Run on GPU</h3>
    <p>QVAL compiles and executes massively parallel evaluations on your GPU hardware</p>
  </div>
  <div class="step-card">
    <h3>4. Get Results</h3>
    <p>Receive optimized parameters with comprehensive reports and analysis</p>
  </div>
</div>

## 📊 Why GPU Parallelism Matters

- **Massive Throughput**: Evaluate 100K-1M+ parameter combinations in parallel
- **Fast Convergence**: Keep wall-clock time low by utilizing all GPU cores simultaneously  
- **Scalable Sampling**: High-performance sampling algorithms optimized for parallel execution
- **Real-time Optimization**: Quick iteration cycles for parameter tuning and exploration

## 💾 Downloads

<div class="downloads-section">
  <div class="download-buttons">
    <a href="{{ site.qval.platforms[0].download_url }}" class="download-btn macos">
      <div class="platform">macOS</div>
      <div class="arch">{{ site.qval.platforms[0].arch }}</div>
    </a>
    <a href="{{ site.qval.platforms[1].download_url }}" class="download-btn windows">
      <div class="platform">Windows</div>  
      <div class="arch">{{ site.qval.platforms[1].arch }}</div>
    </a>
  </div>
  <p class="version-info">Latest Version: {{ site.qval.version }} • Released: {{ site.qval.release_date }}</p>
</div>

## 🔧 Quick Start

1. **List available GPU devices:**
   ```bash
   # macOS
   ./qval --list-devices
   
   # Windows (PowerShell)
   .\qval.exe --list-devices
   ```

2. **Run a simple optimization:**
   ```bash
   # macOS
   ./qval --config config/example/basic/basic.yaml
   
   # Windows (PowerShell)
   .\qval.exe --config config\example\basic\basic.yaml
   ```

3. **Generate reports:**
   ```bash
   ./qval --config config/tutorial/00_minimal/00_minimal.yaml --report txt,md,html
   ```

## 📝 Simple Configuration Example

```yaml
evaluate:
  expr: "X^2 + Y^2"  # Minimize sum of squares
  samples: 10000     # Parallel evaluations
  top_k: 10          # Best results to return
  goal: min          # Minimize objective
  sampler: lhs       # Latin Hypercube Sampling
  
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

## 🎯 Use Cases

- **Engineering Optimization**: Design parameter optimization, system tuning
- **Financial Modeling**: Portfolio optimization, risk analysis, pricing models
- **Scientific Computing**: Parameter estimation, sensitivity analysis, Monte Carlo simulations
- **Machine Learning**: Hyperparameter optimization, architecture search
- **Signal Processing**: Filter design, algorithm parameter tuning

## 📖 Documentation

- **[Examples](examples.html)** - Real-world usage examples and tutorials
- **[Manual](manual.html)** - Complete user documentation
- **[GitHub Repository]({{ site.github.repository_url }})** - Source code and issues

## 📋 System Requirements

- **Operating System**: macOS 10.14+ (Apple Silicon), Windows 10+ (coming soon)
- **GPU**: OpenCL 1.2+ compatible device (NVIDIA, AMD, Intel, Apple Silicon)
- **Memory**: 4GB+ RAM recommended for large-scale evaluations
- **Storage**: 500MB+ available space

<style>
.steps-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  margin: 30px 0;
}

.step-card {
  padding: 20px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  text-align: center;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
}

.step-card h3 {
  color: #2c3e50;
  margin-bottom: 10px;
  font-size: 1.1em;
}

.downloads-section {
  text-align: center;
  margin: 40px 0;
  padding: 30px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 12px;
  color: white;
}

.download-buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.download-btn {
  display: inline-block;
  padding: 15px 25px;
  background: white;
  color: #333;
  text-decoration: none;
  border-radius: 8px;
  font-weight: bold;
  transition: transform 0.2s, box-shadow 0.2s;
  min-width: 120px;
  text-align: center;
}

.download-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  text-decoration: none;
  color: #333;
}

.download-btn .platform {
  font-size: 1.1em;
  margin-bottom: 5px;
}

.download-btn .arch {
  font-size: 0.9em;
  color: #666;
  font-weight: normal;
}

.version-info {
  font-size: 0.9em;
  opacity: 0.9;
  margin: 0;
}
</style>
