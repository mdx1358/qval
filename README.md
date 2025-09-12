# QVAL - GPU-Accelerated Function Evaluator and Optimizer

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
[![Platform Support](https://img.shields.io/badge/platform-macOS%20(Apple%20Silicon)-blue)](https://github.com)
[![GPU Acceleration](https://img.shields.io/badge/acceleration-OpenCL%20GPU-green)](https://www.khronos.org/opencl/)
[![Website](https://img.shields.io/badge/website-mdx1358.github.io%2Fqval-purple)](https://mdx1358.github.io/qval/)

> **🌐 [Visit the Project Website](https://mdx1358.github.io/qval/)** for interactive examples, tutorials, and comprehensive documentation.

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

## 📊 Why GPU Parallelism Matters

- **Massive Throughput**: Evaluate 100K-1M+ parameter combinations in parallel
- **Fast Convergence**: Keep wall-clock time low by utilizing all GPU cores simultaneously  
- **Scalable Sampling**: High-performance sampling algorithms optimized for parallel execution
- **Real-time Optimization**: Quick iteration cycles for parameter tuning and exploration

## 🎯 Use Cases

- **Engineering Optimization**: Design parameter optimization, system tuning
- **Financial Modeling**: Portfolio optimization, risk analysis, pricing models
- **Scientific Computing**: Parameter estimation, sensitivity analysis, Monte Carlo simulations
- **Machine Learning**: Hyperparameter optimization, architecture search
- **Signal Processing**: Filter design, algorithm parameter tuning

## 🔧 Quick Start

### Installation
Download the pre-built binaries for your platform:
- [macOS (Apple Silicon)](https://github.com/mdx1358/qval/releases/latest) - Available now
- [Windows (x64)](https://github.com/mdx1358/qval/releases/latest) - Coming in a few days

### Basic Usage

1. **List available GPU devices:**
   ```bash
   # macOS
   ./qval --list-devices
   
   # Windows (PowerShell)
   .\qval.exe --list-devices
   ```

2. **Run a simple optimization example:**
   ```bash
   # macOS
   ./qval --config config/example/basic/basic.yaml
   
   # Windows (PowerShell)  
   .\qval.exe --config config\example\basic\basic.yaml
   ```

3. **Generate comprehensive reports:**
   ```bash
   # macOS
   ./qval --config config/tutorial/00_minimal/00_minimal.yaml --report txt,md,html
   
   # Windows (PowerShell)
   .\qval.exe --config config\tutorial\00_minimal\00_minimal.yaml --report txt,md,html
   ```

## 📝 Configuration Example

Define your optimization problem in YAML:

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

## 🏗️ Advanced Features

### Multi-Objective Optimization
```yaml
evaluate:
  multi:
    type: weighted_sum
    exprs:
      - { expr: "(X-1)^2 + (Y-1)^2", weight: 0.6, goal: min }
      - { expr: "(X+1)^2 + (Y+1)^2", weight: 0.4, goal: min }
```

### Optimization Algorithms
```yaml
evaluate:
  search:
    algo: cem        # Cross-Entropy Method
    iters: 50        # Optimization iterations
    batch: 5000      # Samples per iteration
    elite_frac: 0.1  # Top 10% for next iteration
```

### Multiple Sampling Strategies
- **LHS**: Latin Hypercube Sampling for space-filling designs
- **Halton**: Low-discrepancy quasi-random sequences
- **Sobol**: Multi-dimensional quasi-random sequences (up to 8D)
- **Uniform**: Standard random sampling

## 🛠️ Development & Testing

### Running Tests
```bash
# Quick test suite
./qval --run-suite config/test

# Include slower tests
./qval --run-suite config/test --include-slow

# Manual test script (if available)
util/run_all_tests.sh
```

## 📖 Documentation

- **[User Manual](bin/doc/manual.md)** - Comprehensive usage guide
- **[Developer Guide](bin/doc/dev.md)** - Development and build instructions
- **[Examples](config/example/)** - Real-world usage examples
- **[Test Suite](config/test/)** - Validation and regression tests

## 🌐 Website & Documentation

Visit our [project website](https://mdx1358.github.io/qval/) for:
- Interactive examples and tutorials
- Complete API documentation  
- Performance benchmarks
- Community showcase

## 📋 System Requirements

- **Operating System**: macOS 10.14+ (Apple Silicon), Windows 10+ (coming soon)
- **GPU**: OpenCL 1.2+ compatible device (NVIDIA, AMD, Intel, Apple Silicon)
- **Memory**: 4GB+ RAM recommended for large-scale evaluations
- **Storage**: 500MB+ available space

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## 📄 License

QVAL is available under a dual license:

- **Personal, Academic, and Research Use**: Free under Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)
- **Commercial Use**: Requires a separate commercial license. Contact mdx1358@fastmail.com for commercial licensing terms.

See the [LICENSE](LICENSE) file for complete terms.

## 🙏 Acknowledgments

- Built with [OpenCL](https://www.khronos.org/opencl/) for cross-platform GPU acceleration
- Inspired by modern optimization frameworks and HPC best practices
- Thanks to the open-source community for tools and libraries

---

**Performance Note**: QVAL is designed for GPU acceleration. While it can run on CPU OpenCL devices, GPU devices provide significantly better performance for parallel evaluations.
