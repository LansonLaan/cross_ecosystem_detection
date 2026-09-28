# Cross-Ecosystem Malicious Package Detection Using SocketAI

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/Status-Completed-brightgreen.svg)]()
[![Download Dataset](https://img.shields.io/badge/Download-Dataset-blue)](https://github.com/LansonLaan/cross_ecosystem_detection/releases/download/v1.0.0/cross_ecosystem_detection.zip)

> **Replicating SocketAI: Detecting malicious packages across Go, PHP, Ruby, and Rust ecosystems using pure LLM-based analysis.**
> A validation study based on the ICSE 2025 paper *"Leveraging Large Language Models to Detect npm Malicious Packages"*.

---

## 📋 Overview

This repository contains my independent replication and extension of the ICSE 2025 paper **SocketAI**, applied to **cross-ecosystem malicious package detection**. Instead of focusing solely on npm (JavaScript), I evaluated the pure LLM-based three-stage detection pipeline on **119 real-world malicious packages** from **four ecosystems**:

| Ecosystem       | Package Manager | Language | # Packages    |
| --------------- | --------------- | -------- | ------------- |
| Go              | Go Modules      | Go       | 95            |
| PHP             | Composer        | PHP      | 10            |
| Ruby            | RubyGems        | Ruby     | 12            |
| Rust            | Cargo           | Rust     | 2             |
| **Total** |                 |          | **119** |

**All 119 packages are confirmed malicious** (ground truth provided by the dataset). The goal was to evaluate whether the pure LLM method (without any additional signals like static analysis, registry verification, or typosquat detection) can effectively identify malicious packages across multiple ecosystems.

📦 **The full dataset (`cross_ecosystem_detection.zip`, 423.61 MiB) is available in the [Release v1.0.0](https://github.com/LansonLaan/cross_ecosystem_detection/releases/tag/v1.0.0).**

---

## 🎯 Key Findings

| Metric                              | Result                       |
| ----------------------------------- | ---------------------------- |
| **Total Packages**            | 119                          |
| **True Positives (Detected)** | 82                           |
| **False Negatives (Missed)**  | 37                           |
| **Recall (Detection Rate)**   | **68.9%**              |
| **Total LLM Calls**           | 19,095                       |
| **Total Tokens**              | 37,489,283                   |
| **Estimated Cost**            | **$10.50 (≈ 75 RMB)** |

### Detection Rate by Ecosystem

| Ecosystem       | Packages      | Malicious Detected | Detection Rate  |
| --------------- | ------------- | ------------------ | --------------- |
| Go              | 95            | 58                 | **61.1%** |
| PHP             | 10            | 10                 | **100%**  |
| Ruby            | 12            | 12                 | **100%**  |
| Rust            | 2             | 2                  | **100%**  |
| **Total** | **119** | **82**       | **68.9%** |

### Key Observations

| Observation                                    | Insight                                                                                                                                   |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Pure LLM works, but with limitations** | The three-stage method detected ~69% of all malicious packages                                                                            |
| **Go ecosystem is hardest**              | Go projects are large and distributed across many files → LLM's single-file analysis struggles to capture cross-file malicious behaviors |
| **PHP/Ruby/Rust show 100% detection**    | These ecosystems have more localized, file-level malicious patterns that LLMs can easily identify                                         |
| **Cost is reasonable**                   | $10.50 to detect 119 packages across 4 ecosystems                                                                                         |

---

## 🔬 What is SocketAI?

SocketAI is a **three-stage LLM-based code review workflow** introduced in the ICSE 2025 paper:

| Stage            | Role                                        | Task                                                                                      |
| ---------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **Step 1** | `SecureGPT` (Security Analyst)            | Generates 3 independent reports per file: identifies sources, sinks, flows, and anomalies |
| **Step 2** | `Security Researcher` (Critical Reviewer) | Cross-validates Step 1 reports, corrects logic errors, adjusts scores                     |
| **Step 3** | `Chief Security Analyst` (Final Arbiter)  | Synthesizes all evidence, outputs final `MALICIOUS` / `BENIGN` verdict                |

### Key Technical Details

| Component           | Specification                                                                                   |
| ------------------- | ----------------------------------------------------------------------------------------------- |
| **Model**     | DeepSeek-Chat (OpenAI-compatible API)                                                           |
| **Prompting** | Zero-shot Chain-of-Thought + Role-Play                                                          |
| **Threshold** | `malware_score >= 0.5` → `MALICIOUS`                                                       |
| **Method**    | **Pure LLM only** — no static analysis, no registry verification, no typosquat detection |

---

## 📁 Repository Structure

```text
cross_ecosystem_detection/
│
├── README.md                         # This file
├── LICENSE                           # MIT License
│
├── socketai_detector/                # Main detection code
│   ├── config.py                     # API key, thresholds, model config
│   ├── prompts.py                    # Three-stage LLM prompts (Step 1/2/3)
│   ├── socketai.py                   # Core SocketAI implementation
│   ├── batch_detector.py             # Batch detection script
│   ├── requirements.txt              # Python dependencies
│   ├── results_cross_ecosystem.json  # Full detection results
│   └── venv/                         # Python virtual environment (excluded)
│
└── data_final/                       # Dataset (available in Release v1.0.0)
    ├── go/                           # 95 Go packages (.zip + extracted)
    ├── php/                          # 10 PHP packages (.zip + extracted)
    ├── ruby/                         # 12 Ruby gems (.gem + extracted)
    ├── rust/                         # 2 Rust crates (.crate + extracted)
    ├── data_final_summary.csv        # Dataset summary
    └── data_final_manifest.csv       # Package manifest
```

📥 **Download the full dataset:**
[Release v1.0.0](https://github.com/LansonLaan/cross_ecosystem_detection/releases/tag/v1.0.0)
[Direct link to `cross_ecosystem_detection.zip`](https://github.com/LansonLaan/cross_ecosystem_detection/releases/download/v1.0.0/cross_ecosystem_detection.zip)

---

## 🚀 Quick Start

### Prerequisites

| Tool    | Version                          |
| ------- | -------------------------------- |
| Python  | 3.9+                             |
| API Key | DeepSeek or OpenAI-compatible    |
| macOS   | 12.0+ (tested on M2 MacBook Air) |

### Installation

```bash
# Clone the repository
git clone https://github.com/LansonLaan/cross_ecosystem_detection.git
cd cross_ecosystem_detection

# Create and activate virtual environment
cd socketai_detector
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Set API key
export DEEPSEEK_API_KEY="your-api-key-here"
```

### Download Dataset

The dataset is not stored in the Git repository. Download it from the Release page:

```bash
# Option 1: Download via browser
open https://github.com/LansonLaan/cross_ecosystem_detection/releases/tag/v1.0.0

# Option 2: Download via curl (direct link)
curl -L -o cross_ecosystem_detection.zip \
  https://github.com/LansonLaan/cross_ecosystem_detection/releases/download/v1.0.0/cross_ecosystem_detection.zip

# Unzip into data_final/
unzip cross_ecosystem_detection.zip -d data_final/
```

### Run Detection

```bash
# Test mode (2 packages per ecosystem)
python3 batch_detector.py

# Full mode (all 119 packages)
# Edit batch_detector.py: set TEST_MODE = False
python3 batch_detector.py
```

### Prevent Sleep (macOS)

```bash
caffeinate -i python3 batch_detector.py
```

### View Results

```bash
# Summary statistics
python3 -c "
import json
with open('results_cross_ecosystem.json') as f:
    data = json.load(f)
    print(f'Total: {data[\"total_packages\"]}')
    mal = sum(1 for r in data['results'] if r.get('verdict') == 'MALICIOUS')
    ben = sum(1 for r in data['results'] if r.get('verdict') == 'BENIGN')
    print(f'MALICIOUS: {mal}, BENIGN: {ben}')
"
```

---

## 🧪 Dataset Details

The dataset contains **119 confirmed malicious packages** from four ecosystems:

### Go (95 packages)

| Characteristic  | Detail                                                        |
| --------------- | ------------------------------------------------------------- |
| Format          | `.zip` archives of Go modules                               |
| Sources         | GitHub repositories with typosquatting / dependency confusion |
| Common Patterns | Command injection, reverse shells, info stealers              |

### PHP (10 packages)

| Characteristic  | Detail                                                                |
| --------------- | --------------------------------------------------------------------- |
| Format          | `.zip` archives of Composer packages                                |
| Sources         | Packagist malicious package reports                                   |
| Common Patterns | Post-install backdoors, eval-based obfuscation, remote code execution |

### Ruby (12 packages)

| Characteristic  | Detail                                             |
| --------------- | -------------------------------------------------- |
| Format          | `.gem` files                                     |
| Sources         | RubyGems malicious package reports                 |
| Common Patterns | Typosquatting, dependency confusion, info stealers |

### Rust (2 packages)

| Characteristic  | Detail                                                 |
| --------------- | ------------------------------------------------------ |
| Format          | `.crate` files                                       |
| Sources         | [Crates.io](https://crates.io/) malicious package reports |
| Common Patterns | Build-time code execution                              |

---

## 💡 Challenges & Solutions

### Challenge 1: Nested Directory Structure (Go Packages)

**Problem**: Go packages often extract to nested paths (e.g., `GO-0001/.../github.com/user/repo@version/`), making it difficult for the detector to find `go.mod` or source files.

**Solution**: Implemented `find_go_module_root()` in `batch_detector.py` to recursively locate the directory containing `go.mod` or `.go` files.

### Challenge 2: ASCII Encoding Errors

**Problem**: File paths containing `@` symbols (common in Go module versioning) caused `openai` library to fail with `'ascii' codec can't encode characters`.

**Solution**: Added UTF-8 safe encoding for all file paths, prompts, and API request content in `socketai.py`.

### Challenge 3: Package Duplication

**Problem**: `discover_packages()` was adding both `.zip` files and their extracted directories, causing duplicates.

**Solution**: Added `seen_names` tracking to ensure each package is added only once.

### Challenge 4: API Timeouts

**Problem**: Some large files caused Step 3 to time out and hang.

**Solution**: Added `timeout=120` parameter to API calls in `socketai.py` to prevent indefinite waiting.

### Challenge 5: Ruby Gem Unpacking

**Problem**: `gem unpack` failed because Ruby was not installed on the system.

**Solution**: Ruby packages were skipped in this run. For future runs: `brew install ruby` before running the detector.

### Challenge 6: MacBook Sleep Mode

**Problem**: Closing the lid or system sleep interrupts API calls.

**Solution**: Used `caffeinate -i` command to prevent system sleep during execution.

---

## 📊 Complete Detection Results

```json
{
  "total_packages": 119,
  "malicious_predicted": 82,
  "benign_predicted": 37,
  "errors": 0,
  "llm_calls": 19095,
  "total_tokens": 37489283,
  "estimated_cost_usd": 10.496999,
  "by_ecosystem": {
    "go": { "total": 95, "malicious": 58, "benign": 37 },
    "php": { "total": 10, "malicious": 10, "benign": 0 },
    "ruby": { "total": 12, "malicious": 12, "benign": 0 },
    "rust": { "total": 2, "malicious": 2, "benign": 0 }
  }
}
```

---

## 🔧 Technical Stack

| Layer                  | Technology                               |
| ---------------------- | ---------------------------------------- |
| **Language**     | Python 3.9+                              |
| **LLM API**      | DeepSeek-Chat (OpenAI-compatible)        |
| **VM**           | Python virtual environment               |
| **File Parsing** | `zipfile`, `tarfile`, `subprocess` |
| **OS**           | macOS 12.0+ (M2 chip)                    |
| **IDE**          | VS Code                                  |

---

## 📝 Conclusion

The **pure LLM-based SocketAI method** effectively detects malicious packages across multiple ecosystems with an overall **68.9% recall rate** at a very low cost (**$10.50**). However, detection performance varies significantly by ecosystem:

| Ecosystem       | Detection Rate  | Why?                                                                                             |
| --------------- | --------------- | ------------------------------------------------------------------------------------------------ |
| PHP, Ruby, Rust | **100%**  | Malicious patterns are localized and file-level — easy for LLM to spot                          |
| Go              | **61.1%** | Projects are large and distributed → single-file analysis misses cross-file malicious behaviors |

This validates the SocketAI approach while also revealing its limitations in complex, multi-file ecosystems like Go.

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgments

- **Original SocketAI Paper**: Zahan et al., ICSE 2025
- **Dataset**: Provided by the research supervisor
- **API**: DeepSeek for affordable LLM access

---

## 📬 Contact

For questions or collaboration, feel free to open an issue or reach out.

---

Built with ❤️ for the open-source security community.
