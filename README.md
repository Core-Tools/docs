# LLMbench: Unified LLM Benchmark Execution Framework

**LLMbench** is a modular, cross-platform CLI-driven framework for executing large language model (LLM) benchmarks. It abstracts the deployment and execution complexity of standard and custom benchmark suites using a unified gRPC-based architecture.

---

## 🧠 Key Features

- [TODO] Single portable CLI binary (no runtime deps)
- [TODO] Modular benchmark architecture (supports benchmarks aggregators or independent benchmarks like MTEB)
- [TODO] Works with both cloud (OpenAI, etc.) and local (LM Studio, etc.) LLMs
- [TODO] CLI and config-based parameter control
- [TODO] Cross-platform: Linux / macOS / Windows
- [TODO] Report generation: TXT, XLS, HTML
- [TODO] Results export: JSON, CSV
- [TODO] Comparison and diff of benchmark runs
- [TODO] Optional Kubernetes support

---

## 🗺️ Architecture Overview

### 🛠 Components

**LLMbench CLI Tool**
- Entry point to trigger and manage benchmarks
- Talks to benchmark runners via gRPC
- Stores metadata and results in SQLite
- Handles workers process orchestration, logging, test duration, OS limits 

**Benchmark Runners (Python)**
- MTEB and other original benchmark Python implementations
- Controlled via gRPC server libraries
- Execute benchmarks, log progress, and return results

**gRPC Libraries**
- Common interface used by CLI and runners
- Separates client and server logic
- Supports worker lifecycle management and test health monitoring

**OS-Level Utilities (Go)**
- Process monitoring, memory limits, worker orchestration
- Ping, start/stop workers, attach/kill orphaned processes

---

## 🧩 Repository Topology

| Repo                          | Language | Role |
|------------------------------|----------|------|
| `llmbench-cli`               | Go       | Main CLI tool to coordinate everything |
| `llmbench-go-oslib`          | Go       | OS-level abstraction utils (proc, mem, logs, limits) |
| `llmbench-go-grpc-client`    | Go       | Client-side gRPC for launching benchmarks |
| `llmbench-go-llm-client`     | Go       | Logic for interacting with benchmark servers |
| `llmbench-py-grpc-server`    | Python   | gRPC server for receiving CLI instructions |
| `llmbench-py-bench-runner`   | Python   | Executes benchmark suites and parses results |
| `llmbench-os-tools` | Bash/Go/native | OS-specific diagnostic/utility tools |

---

## 🧪 Supported Benchmarks

- [TODO] MTEB (via original Python implementation)
- [TODO] Other standardized or custom Python benchmarks
- [TODO] ...

---

## 🤖 Supported LLM Backends

Any service with OpenAI compatible API:
- **Cloud services**: OpenAI, Anthropic, etc.
- **Local runtimes**: LM Studio, Ollama, custom APIs

---

## 📦 Output Formats

- [TODO] Reports: `.txt`, `.html`, `.xls`
- [TODO] Structured: `.json`, `.csv`
- [TODO] Comparison of results across different runs

---

## 🚀 Execution Modes

- Local: CLI binary + Python runners on same machine
- Distributed: CLI with remote runners over gRPC
- Kubernetes: Benchmark runners as pods; CLI connects via registry

---

## 🔧 Example CLI Usage

```bash
./llmbench-cli run --model "gpt-3.5-turbo" --benchmark "mteb" \
  --llm-url "https://api.openai.com" --output-format json \
  --export-to results/mteb_results.json
