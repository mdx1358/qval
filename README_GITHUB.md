# GitHub Setup Files for QVAL

This directory contains all the files needed to set up QVAL on GitHub with a professional repository page and GitHub Pages website.

## Files Included

### Repository Files (Copy to repo root)
- `README.md` - Main repository README with project overview, features, and quick start
- `_config.yml` - Jekyll configuration for GitHub Pages
- `index.md` - GitHub Pages homepage 
- `examples.md` - Examples page with real-world usage scenarios
- `manual.md` - Condensed manual for web viewing

### GitHub Workflow & Templates (Copy to repo root)
- `.github/workflows/release.yml` - Automated release builds for macOS/Linux/Windows
- `.github/ISSUE_TEMPLATE/bug_report.md` - Bug report template
- `.github/ISSUE_TEMPLATE/feature_request.md` - Feature request template

## Setup Instructions

### 1. Copy Files to GitHub Repository

Copy these files to your GitHub repository root:

```bash
# Copy main files to repo root
cp github/README.md /path/to/your/repo/
cp github/_config.yml /path/to/your/repo/
cp github/index.md /path/to/your/repo/
cp github/examples.md /path/to/your/repo/
cp github/manual.md /path/to/your/repo/

# Copy GitHub workflow and templates
cp -r github/.github /path/to/your/repo/
```

### 2. Customize Configuration

Edit these files with your actual information:

#### `_config.yml`
```yaml
# Update these with your actual GitHub info
url: "https://yourusername.github.io"
baseurl: "/qval"  # Use "" for user site (yourusername.github.io)
github:
  repository_url: "https://github.com/yourusername/qval"
  repository_nwo: "yourusername/qval"
author:
  name: "Your Name"
  email: "your.email@example.com"

# Update download URLs when you create releases
qval:
  platforms:
    - name: "macOS"
      download_url: "https://github.com/yourusername/qval/releases/latest/download/qval-macos.zip"
    - name: "Linux" 
      download_url: "https://github.com/yourusername/qval/releases/latest/download/qval-linux-x64.tar.gz"
    - name: "Windows"
      download_url: "https://github.com/yourusername/qval/releases/latest/download/qval-windows-x64.zip"
```

#### `README.md`
- Replace all instances of "yourusername" with your GitHub username
- Update download links in the Installation section
- Update website URL in the Website & Documentation section

### 3. Enable GitHub Pages

1. Push the files to your GitHub repository
2. Go to repository Settings → Pages
3. Set Source to "Deploy from a branch"
4. Select branch: `main` (or your default branch) 
5. Set folder: `/ (root)`
6. Click Save

Your site will be available at:
- Project site: `https://yourusername.github.io/qval` (if baseurl: "/qval")
- User site: `https://yourusername.github.io` (if baseurl: "")

### 4. Optional: Set Up Automated Releases

The included GitHub Actions workflow will automatically build and release binaries when you push version tags.

To use it:
1. Ensure your project builds with CMake
2. Push a version tag: `git tag v1.0.0 && git push origin v1.0.0`
3. The workflow will build for all platforms and create a GitHub release

To disable: Delete `.github/workflows/release.yml`

## Website Structure

The GitHub Pages site includes:

### Homepage (`index.md`)
- Project overview and key features
- "How It Works" workflow explanation
- Prominent download buttons for all platforms
- Quick start guide and configuration example
- Links to examples and documentation

### Examples Page (`examples.md`)
- Real-world optimization scenarios
- Function optimization (Rosenbrock)
- Multi-dimensional sampling (8D sphere with Sobol)
- Cross-entropy method portfolio optimization
- Multi-objective engineering design
- Signal processing filter design
- Manufacturing quality control
- GPU performance benchmarking
- Configuration tips and next steps

### Manual Page (`manual.md`)
- Complete user documentation
- Installation and quick start
- Configuration reference
- Command line options
- Parameter types and distributions
- Expression syntax and built-ins
- Sampling strategies
- Optimization algorithms
- Multi-objective optimization
- Input/output options
- Report generation
- Performance tuning
- Troubleshooting guide

## Features Included

### Professional Repository
- Comprehensive README with badges, features, and usage
- Clear installation instructions
- Code examples and configuration samples
- Links to documentation and examples

### GitHub Pages Website
- Multi-page professional site
- Responsive design for mobile/desktop
- Syntax highlighting for YAML code
- Download integration with GitHub releases
- SEO-friendly with proper meta tags

### Issue Management
- Bug report template with system info fields
- Feature request template with use case fields
- Consistent issue labeling

### Automated Releases
- Multi-platform builds (macOS, Linux, Windows)
- Automatic asset packaging
- Release notes generation
- Artifact uploads

## Customization Options

### Styling
The site uses minimal Jekyll styling that can be customized:
- Edit CSS in the `<style>` sections of the markdown files
- Add custom CSS files and reference them in `_config.yml`
- Override the minima theme with custom layouts

### Content
- Add more examples to `examples.md`
- Expand the manual with additional sections
- Create additional pages (e.g., tutorials, API reference)
- Add blog functionality with Jekyll posts

### Advanced Features
- Add search functionality
- Integrate with analytics
- Add community features (discussions, contributors)
- Set up automated documentation generation

This setup provides a professional foundation that can grow with your project while maintaining consistency and usability.
