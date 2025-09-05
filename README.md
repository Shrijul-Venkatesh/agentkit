# 🤖 Enhanced Toolkits for AI Agents

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Agno Framework](https://img.shields.io/badge/framework-Agno-green.svg)](https://github.com/agno-ai/agno)

Production‑ready toolkits for building intelligent agents with OpenAI function calling and the Agno framework. This repository provides 8 core toolkits and 9 calculator modules with strict schemas, robust validation, comprehensive error handling, caching, and rate limiting.

---

## ✨ Highlights

- 🧠 Advanced reasoning and structured thinking with bias detection
- 🔍 SearxNG search with optional full‑content fetching and anti‑bot bypass
- 📁 Enterprise‑grade secure file operations (atomic edits, locking, limits)
- 📈 Finance data via Yahoo Finance with caching and throttling
- 🧮 Financial calculators (NPV/IRR/TVM/Loan/Bond/Risk/etc.)
- 🎥 YouTube metadata and transcript utilities
- ☁️ Weather data with multi‑language support
- 📥 Universal URL downloader with smart content processing
- 🔒 Strict OpenAI function schemas for reliable agent tool use

---

## 📦 Installation

Install from GitHub:

```bash
# Core installation
pip install git+https://github.com/malvavisc0/enhancedtoolkits.git

# All optional features
pip install "enhancedtoolkits[full] @ git+https://github.com/malvavisc0/enhancedtoolkits.git"

# Selective extras
pip install "enhancedtoolkits[youtube] @ git+https://github.com/malvavisc0/enhancedtoolkits.git"
pip install "enhancedtoolkits[content] @ git+https://github.com/malvavisc0/enhancedtoolkits.git"
pip install "enhancedtoolkits[weather] @ git+https://github.com/malvavisc0/enhancedtoolkits.git"
```

Recommended: use a virtual environment. See `docs/getting-started/installation.md` for details and Docker usage.

---

## 🚀 Quick Start

```python
from enhancedtoolkits import (
    ReasoningTools,
    SearxngTools,
    ThinkingTools,
    FilesTools,
    YFinanceTools,
    YouTubeTools,
    WeatherTools,
    DownloaderTools,
)

reasoning = ReasoningTools()
search = SearxngTools(host="http://searxng:8080")
thinking = ThinkingTools()
files = FilesTools()
finance = YFinanceTools()
youtube = YouTubeTools()
weather = WeatherTools()
downloader = DownloaderTools()
```

Using with an Agno `Agent`:

```python
from agno.agent import Agent
from enhancedtoolkits import *

agent = Agent(
  name="AI Assistant",
  model="gpt-4",
  tools=[
    ReasoningTools(),
    SearxngTools(host="http://searxng:8080"),
    ThinkingTools(),
    FilesTools(),
    YFinanceTools(),
    YouTubeTools(),
    WeatherTools(),
    DownloaderTools(),
  ]
)
```

---

## 🧱 Architecture & Philosophy

- All toolkits inherit from `StrictToolkit` (`src/enhancedtoolkits/base.py`).
- `StrictToolkit` registers functions with strict=True and validates OpenAI compatibility using `OpenAISchemaValidator` (`src/enhancedtoolkits/utils/schema.py`).
- Each toolkit returns consistently formatted JSON strings for reliable agent consumption.
- Caching, throttling, and robust exception hierarchies are included where relevant.

Why strict schemas? Agents (especially via OpenAI tools) are less error‑prone when every parameter is marked required. This repo enforces that at the schema level to prevent ambiguous calls and improve reliability.

---

## 🧰 Core Toolkits

Exported at the package root (see `src/enhancedtoolkits/__init__.py`):

- `ReasoningTools` — multi‑modal reasoning, bias detection, session/state helpers
- `SearxngTools` — SearxNG search (general/news/images/videos/categories), optional page content fetching
- `ThinkingTools` — structured thinking chains, reflections, quality assessment
- `FilesTools` — safe file I/O with path validation, atomic writes, locking, size/line limits
- `YFinanceTools` — stock and company data, statements, news, recommendations with validation, caching, and throttling
- `YouTubeTools` — metadata and transcript extraction with retries and language support
- `WeatherTools` — current conditions, forecasts, temperatures, multi‑language responses
- `DownloaderTools` — universal downloader (BYPARR anti‑bot integration, MarkItDown processing, multiple formats)

Notes:
- Public methods are registered for agent tools; refer to the API Reference for the authoritative list and signatures. The internal method names may be prefixed (e.g., `fetch_*`) but the toolkit exposes a consistent capability surface.

---

## 🧮 Calculator Suite

Import calculators individually from `enhancedtoolkits.calculators`:

- `ArithmeticCalculatorTools` — arithmetic, powers/logs, primes/factorial, mean/median/std
- `TimeValueCalculatorTools` — PV, FV, annuities (present/future), perpetuities
- `InvestmentAnalysisCalculatorTools` — NPV, IRR (Newton–Raphson), CAGR, ROI
- `LoanCalculatorTools` — payment calculations, amortization schedules
- `BondCalculatorTools` — price and yield‑to‑maturity approximations
- `RiskMetricsCalculatorTools` — Sharpe ratio, volatility
- `DepreciationCalculatorTools` — straight‑line, declining balance
- `BusinessAnalysisCalculatorTools` — break‑even point
- `UtilityCalculatorTools` — currency conversion, inflation adjustment

All calculators share a robust base with validation and standardized JSON outputs.

Example:

```python
from agno.agent import Agent
from enhancedtoolkits.calculators import (
  ArithmeticCalculatorTools,
  TimeValueCalculatorTools,
  InvestmentAnalysisCalculatorTools,
)

agent = Agent(
  name="Financial Analyst",
  model="gpt-4",
  tools=[
    ArithmeticCalculatorTools(),
    TimeValueCalculatorTools(),
    InvestmentAnalysisCalculatorTools(),
  ]
)
```

---

## ⚙️ Configuration

Common environment variables:

```bash
# BYPARR for anti-bot
BYPARR_URL=http://byparr:8191/v1
BYPARR_TIMEOUT=60
BYPARR_ENABLED=false

# Downloader defaults
URL_DOWNLOADER_MAX_RETRIES=3
URL_DOWNLOADER_TIMEOUT=30

# Weather
WEATHER_API_URL=https://wttr.in

# Logging
LOG_LEVEL=INFO
```

Programmatic configuration examples are available in `docs/getting-started/configuration.md`.

---

## 🔧 Practical Examples

Finance + time value of money:

```python
from enhancedtoolkits import YFinanceTools
from enhancedtoolkits.calculators import TimeValueCalculatorTools

finance = YFinanceTools(enable_caching=True, cache_ttl=300)
tvm = TimeValueCalculatorTools()

# Use finance toolkit methods (see API Reference for exact names/signatures)
# Use calculator outputs (JSON strings) directly or parse as needed
fv = tvm.calculate_future_value(present_value=10000, rate=0.07, periods=10)
print(fv)
```

Secure file operations:

```python
from enhancedtoolkits import FilesTools

files = FilesTools(base_dir="/secure/workspace")
# Use chunked read/edit/insert/delete helpers; operations are atomic and validated
```

Universal content downloading:

```python
from enhancedtoolkits import DownloaderTools

downloader = DownloaderTools(byparr_enabled=True, enable_caching=True)
# Download web pages, PDFs, Office docs, images, archives, etc., with smart processing
```

---

## 📚 Documentation

MkDocs Material site includes:

- Getting Started, Installation, Configuration, Quick Start
- Core Toolkits setup guides
- Calculator modules with use cases
- API Reference (manual + auto‑generated with mkdocstrings)

Build locally:

```bash
pip install -r docs/requirements.txt
mkdocs serve
```

Deploy to GitHub Pages:

```bash
mkdocs gh-deploy
```

Automated docs pipeline: `.github/workflows/docs.yml`.

---

## 🧪 Quality & Error Handling

- Strong input validation and clear exceptions across toolkits
- Consistent JSON outputs for agent consumption
- Caching and throttling knobs for external services
- Defensive handling of network and parsing failures

---

## 🛡️ StrictToolkit in Depth

`StrictToolkit` ensures OpenAI compatibility by registering functions with strict schemas (all parameters required) and validating schemas at registration. This reduces tool‑call ambiguity and makes agent integrations more reliable.

See:
- `src/enhancedtoolkits/base.py`
- `src/enhancedtoolkits/utils/schema.py`

---

## 🔗 Repository Structure

- `src/enhancedtoolkits/` — core toolkits and calculators
- `docs/` — MkDocs site content and API pages
- `pyproject.toml` — packaging and tooling config
- `mkdocs.yml` — documentation configuration

---

## 🤝 Contributing

Contributions welcome!

- Development setup & guidelines: `docs/developer/index.md`, `docs/developer/contributing.md`
- Docs deployment guide: `docs/developer/deployment.md`
- Run tests, lint, and type checks as configured in `pyproject.toml`

Please open issues and discussions on GitHub.

---

## 📄 License

MIT License. See `LICENSE`.

---

## 📦 Versioning & Distribution

- Package name: `enhancedtoolkits`
- Python: 3.8+
- Install directly from GitHub (see Installation)

Changelog and releases are managed on the GitHub repository.

---

Made with ❤️ for AI agent developers who want reliable, production‑grade tools.
