---
layout: default
title: Home
---

<header class="hero" role="banner">
    <div class="container">
      <div class="hero-content">
        <h1 class="gradient-text animate-on-scroll">QVAL</h1>
        <h2 class="animate-on-scroll">Evaluate formulas and find the best parameters — fast.</h2>
        <p class="hero-description animate-on-scroll">Write a simple YAML file, point to your CSV data, and QVAL runs your expression thousands of times on your GPU (or on an OpenCL CPU runtime when available). It then reports the best results and saves everything neatly to an output folder.</p>
        <div class="hero-badges" role="complementary">
          <span class="badge">No code</span>
          <span class="badge">YAML</span>
          <span class="badge">CSV/XLSX</span>
          <span class="badge">GPU</span>
        </div>
        <div class="download-buttons" style="margin-top: 1.5rem;" id="downloads">
          <a href="https://github.com/mdx1358/qval/releases/latest/download/qval-macos-arm64.zip" class="download-btn download-btn-mac" data-track="download-mac">
            <span class="download-icon">⬇</span> Download QVAL for macOS
          </a>
          <a href="#" class="download-btn download-btn-win" data-track="download-win" style="opacity: 0.6; cursor: not-allowed;">
            <span class="download-icon">⬇</span> Windows (Coming Soon)
          </a>
        </div>
        <div class="download-buttons" style="margin-top: 0.75rem;">
          <a href="#quickstart" class="link-btn">Read Quick Start</a>
        </div>
      </div>
    </div>
  </header>

  <main>
    <section id="features" class="features" aria-labelledby="features-heading">
      <div class="container">
        <h2 id="features-heading" class="gradient-text">Key Features</h2>
        <div class="feature-card">
          <h3>Simple configs</h3>
          <p>Describe your formula and parameters in a small YAML file. No code changes needed.</p>
        </div>
        <div class="feature-card">
          <h3>Your data: CSV/XLSX</h3>
          <p>Read inputs from CSV or Excel (XLSX) by column name or index. Get clear CSV/XLSX reports out.</p>
        </div>
        <div class="feature-card">
          <h3>Fast on your machine</h3>
          <p>Runs on your GPU when available; can run on CPU when an OpenCL CPU runtime (e.g., POCL) is installed.</p>
        </div>
        <div class="feature-card">
          <h3>Clear results</h3>
          <p>Top results table, summary stats, and saved kernel/code for traceability (CSV and XLSX outputs).</p>
        </div>
        <div class="feature-card">
          <h3>Flexible sampling</h3>
          <p>Random, Sobol, LHS, or Halton — choose what works best without complexity.</p>
        </div>
      </div>
    </section>

    <section id="how-it-works" class="features" aria-labelledby="how-heading">
      <div class="container">
        <h2 id="how-heading" class="gradient-text">How it works</h2>
        <div class="quickstart-steps">
          <div class="step-card">
            <div class="step-num">1</div>
            <h3>Define a config</h3>
            <p>Write a small YAML: expr, goal (min/max), samples, top_k, and params.</p>
          </div>
          <div class="step-card">
            <div class="step-num">2</div>
            <h3>Load your data</h3>
            <p>Point to CSV/XLSX columns by name or index, or use constants. Per-parameter or global.</p>
          </div>
          <div class="step-card">
            <div class="step-num">3</div>
            <h3>Sample variations</h3>
            <p>Generate candidates using RNG, Sobol, LHS, or Halton to explore the parameter space.</p>
          </div>
          <div class="step-card">
            <div class="step-num">4</div>
            <h3>Compute</h3>
            <p>QVAL builds an OpenCL kernel for your expression and evaluates all variations on the device.</p>
          </div>
          <div class="step-card">
            <div class="step-num">5</div>
            <h3>Get results</h3>
            <p>QVAL ranks all results, saves the best ones, and generates detailed reports with analysis.</p>
          </div>
        </div>
      </div>
    </section>

    <section id="quickstart" class="features" aria-labelledby="qs-heading">
      <div class="container">
        <h2 id="qs-heading" class="gradient-text">Quick Start</h2>
        <div class="quickstart-steps">
          <div class="step-card">
            <div class="step-num">1</div>
            <h3>Download & unzip</h3>
            <p>Grab the build for your OS and unzip.</p>
          </div>
          <div class="step-card">
            <div class="step-num">2</div>
            <h3>Run an example</h3>
            <details class="example-detail">
              <summary>Show commands</summary>
              <pre class="code-clip"><code class="language-bash"># macOS/Linux
./run_basic_example.sh
# Windows
run_basic_example.bat</code></pre>
            </details>
          </div>
          <div class="step-card">
            <div class="step-num">3</div>
            <h3>Open the results</h3>
            <details class="example-detail">
              <summary>Where to look</summary>
              <pre class="code-clip"><code class="language-text">output/tutorial/00_minimal/report/report.txt
output/tutorial/00_minimal/code/eval.cl</code></pre>
            </details>
          </div>
        </div>
        <p class="note">CPU execution requires an OpenCL CPU runtime (e.g., POCL). Without it, only GPU devices are used.</p>
      </div>
    </section>

    <section id="contact" class="features" aria-labelledby="contact-heading">
      <div class="container">
        <h2 id="contact-heading" class="gradient-text">Contact</h2>
        <div class="feature-card">
          <h3>Get in Touch</h3>
          <p>For inquiries, email: <a href="mailto:mdx1358@fastmail.com" style="color: var(--primary-light);">mdx1358@fastmail.com</a></p>
        </div>
        <div class="feature-card">
          <h3>GitHub</h3>
          <p><a href="https://github.com/mdx1358/qval" style="color: var(--primary-light); font-weight: 600; text-decoration: none;" target="_blank">📚 View on GitHub</a></p>
          <p style="font-size: 0.95rem; color: var(--text-muted);">Documentation, releases, issues, and example configs</p>
        </div>
      </div>
    </section>
  </main>
