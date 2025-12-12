TODO(guynich): delete this file if push request something else for HazyResearch.

Test runs on Ubuntu 24.04.

- [Pre-requisites](#pre-requisites)
- [Installation](#installation)
- [Run](#run)
  - [llama3.2:1b](#llama321b)
  - [gemma3:1b](#gemma31b)
  - [gemma3:4b](#gemma34b)
  - [TODO: qwen3:4b](#todo-qwen34b)
- [Analyze](#analyze)
- [Backup](#backup)
- [Results](#results)
- [Next steps](#next-steps)


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
Follow
[installation pre-requisites](https://github.com/HazyResearch/intelligence-per-watt#prerequisites)
including uv, Rust, Protocol Buffer compiler.  I chose Ollama for inference
engine.

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

This is a fork.  Check if a sync is needed with
[source repo](https://github.com/HazyResearch/intelligence-per-watt).

e.g.: in a branch.
```bash
git remote -v
git remote add upstream git@github.com:HazyResearch/intelligence-per-watt.git
git fetch upstream
git merge upstream/main
```

### llama3.2:1b
Run with the `ipw` dataset.
```bash
ipw profile --client ollama --model llama3.2:1b --dataset ipw
```

### gemma3:1b
Run with the `ipw` dataset.
```bash
ollama pull gemma3:1b

ipw profile --client ollama --model gemma3:1b --dataset ipw
```

### gemma3:4b
Run with the `ipw` dataset.
```bash
ollama pull gemma3:4b

ipw profile --client ollama --model gemma3:4b --dataset ipw
```

### TODO: qwen3:4b
Run with the `ipw` dataset.

> Run crashed without user input.

> Run without reasoning?

```bash
ipw profile --client ollama --model qwen3:4b --dataset ipw
```

## Analyze

Example run commands.

```bash
ipw analyze runs/profile_RTXA2000_llama3_2_1b_Intelligence\ Per\ Watt/
```

```bash
ipw plot runs/profile_RTXA2000_llama3_2_1b_Intelligence\ Per\ Watt/
```

## Backup

Copy the `runs/` folder before deleting the repo.

## Results

Test runs on NVidia RTX-A2000 (Ampere GPU).

| Model       | Profiling (hms) | Scoring (hms) | API cost ($) | Accuracy | IPW   |
|-------------|-----------------|---------------|--------------|----------|-------|
| llama3.2:1b | 01:25:23        | 01:08:30      | 1.12         | 0.397    | 0.011 |
| gemma3:1b   | 02:06:57        | 01:04:22      | 1.21         | 0.518    | 0.012 |
| gemma3:4b   | 09:01:42        | 01:11:36      | 1.22         | 0.695    | 0.023 |

## Next steps

- [ ] Increase timeout_seconds to try mitigate OpenAIClient read timeouts (N=[8, 13]).
- [ ] Generate ipw results for reasoning models that can infer with [thinking disabled](https://ollama.com/blog/thinking): compare with thinking enabled and disabled.
