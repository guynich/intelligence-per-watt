TODO(guynich): delete if pushing to HazyResearch

Ubuntu 24.04

## Pre-requisites

1. Install Ollama.

Download a model that you want to profile, e.g.: for testing
```bash
ollama pull llama3.2:1b
```

2. Install `uv`.

3. The `energy-monitor` file built in the following steps can not be ignored by
git.  If you want git to ignore this file then remove it from the cache.  This command does not delete the file.
```bash
git rm -r --cached intelligence-per-watt/src/ipw/telemetry/bin/linux-x86_64/energy-monitor
```

4. Get an API key from OpenAI and add it to `.env` file in the repo directory.  My first profiling run cost 1.12USD, so check you have a few dollars balance.

https://platform.openai.com/docs/overview

```console
OPENAI_API_KEY=<your key>
```

## Installation

1. Clone the repo, or your fork.
```bash
cd
git clone https://github.com/HazyResearch/intelligence-per-watt.git
```

2. Create and activate virtual environment
```bash
uv venv venv_ipw
source ./venv_ipw/bin/activate
```

3. Build energy monitoring
```bash
cd intelligence-per-watt
uv run scripts/build_energy_monitor.py
```
You can add the generated `energy-monitor` file to `.gitignore` if you do not
want to push the file.

4. Install Intelligence Per Watt
```bash
uv pip install -e intelligence-per-watt
```

5. Install Intelligence Per Watt for Ollama
```bash
uv pip install -e 'intelligence-per-watt[ollama]'
```

## Run

### llama3.2:1b
To run with the `ipw` dataset.
```bash
ipw profile --client ollama --model llama3.2:1b --dataset ipw
```

### qwen3:4b
To run with the `ipw` dataset.
```bash
ipw profile --client ollama --model qwen3:4b --dataset ipw
```
Billing had $7.42 on 12/11/2025.

## Analyze

```bash
ipw analyze runs/profile_RTXA2000_llama3_2_1b_Intelligence\ Per\ Watt/
```

```bash
ipw plot runs/profile_RTXA2000_llama3_2_1b_Intelligence\ Per\ Watt/
```

## Backup

Copy the `runs/` folder before deleting the repo.
