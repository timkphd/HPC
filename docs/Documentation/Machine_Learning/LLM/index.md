---
layout: default
title: LLM
parent: Machine Learning
---

# Large Language Model (LLM) Assistants

Kestrel hosts two locally-served LLM-based assistants for coding and HPC-support workflows. Both run open-weight models on Kestrel's own GPU nodes, so no code or data leaves NLR: **[OnField Assistant (`ofa`)](#onfield-assistant-ofa)**, a retrieval-augmented assistant built and maintained in-house and tuned for OpenFOAM/AMReX/MARBLES/VASP/HPC-support workflows, and **[opencode](#opencode)**, a general-purpose terminal coding agent that can also reach NLR's centrally-hosted ServeAI Gateway models.

## OnField Assistant (`ofa`)

[OnField Assistant](https://github.com/nileshsawant/onfield-assistant) (`ofa`) is a locally-hosted, retrieval-augmented-generation (RAG) LLM assistant for HPC and scientific-computing workflows. It runs Gemma 4 (31B, Google, Apache 2.0) via Ollama on a Kestrel GPU node, and layers RAG over Kestrel's documentation plus OpenFOAM, AMReX, MARBLES, VASP, ReFrame, and quantum-computing source/paper corpora. It currently ships eight specialized modes &mdash; `--code` (default), `--openfoam`, `--hpc`, `--amrex`, `--marbles`, `--quantum-computing`, `--vasp`, and `--rhel9_reframe` &mdash; each swapping in a different system prompt and RAG index.

```
module load assistant
```

puts `ofa` on `$PATH` (and the `ofa_client` Python module on `$PYTHONPATH`). A GPU is allocated automatically via SLURM the first time you run `ofa` (override the account/partition/walltime with the `OFA_ACCOUNT` / `OFA_PARTITION` / `OFA_WALLTIME` environment variables).

!!! note
    Full installation notes, the RAG-maintenance playbook, and an in-depth technical writeup live in the project's own docs: [nileshsawant.github.io/onfield-assistant](https://nileshsawant.github.io/onfield-assistant/).

There are five ways to use `ofa`:

### 1. Command line

```
$ ofa                            # interactive chat, default --code mode
$ ofa "explain this SLURM error"
$ ofa --openfoam --save ./mycase # OpenFOAM case generator, writes files to ./mycase
$ ofa --hpc                      # Kestrel HPC / Slurm documentation assistant
$ ofa --resume                   # resume the previous session
$ ofa --list-models              # show the model registry
```

Type `quit` to leave interactive mode, or `/help` for the full list of slash commands. Run `ofa --help` to see every flag, including `--save`, `--fast`, `--model`, `--no-rag`, and the `--serve*` flags used below.

### 2. VS Code Chat (BYOK)

`ofa --serve` starts an OpenAI-compatible HTTP server (`/v1/chat/completions`) on your allocation. Paired with the [OnField Assistant VS Code extension](https://github.com/nileshsawant/onfield-assistant/tree/main/vscode-ext) (one-click SLURM allocation + login-node port bridge) or a manual `ssh -L` tunnel, this registers every `ofa` mode as a "Bring Your Own Key" model in VS Code Copilot Chat's model picker. Full walkthrough: [Use ofa from VS Code Chat (the OnField Assistant extension)](https://github.com/nileshsawant/onfield-assistant#use-ofa-from-vs-code-chat-the-onfield-assistant-extension).

### 3. Python (`ofa_client`)

A stdlib-only Python client talks to a running `ofa --serve` over HTTP &mdash; no extra packages needed:

```python
from ofa_client import ask
text = ask("what is a good turbulence model for cavity flow at Re=1e4?")
```

It also supports attaching files/images, multi-turn `Session()` objects for client-side conversation history, and auto-detects the server URL/token from `$OFA_SCRATCH`. This is useful for having a running simulation ask `ofa` to summarize a plot or diagnose a crash mid-run &mdash; see the [`ofa_client` docs](https://github.com/nileshsawant/onfield-assistant#programmatic-use-from-python-ofa_client).

### 4. Through opencode

`ofa --serve` also works as a model provider for opencode (see the [opencode](#opencode) section below), so opencode's model picker can include the whole `ofa` mode family alongside opencode's own ServeAI Gateway / local-Ollama providers. See [Use ofa from opencode](https://github.com/nileshsawant/onfield-assistant#use-ofa-from-opencode-rhel9--access-gated) for the per-allocation setup.

### 5. Custom / third-party agents

Because `ofa --serve` speaks the standard OpenAI `/v1/chat/completions` API, any agent framework that supports pointing at a custom `base_url` + `api_key` can use `ofa` as its backend LLM. For example, [AMReX Agent](https://github.com/AMReX-Codes/amrex-agent) has a `litellm` provider for exactly this; pointed at `ofa`:

```bash
module load assistant
ofa --serve --serve-enable-tools
export LITELLM_BASE_URL="http://localhost:$(cat $OFA_SCRATCH/.ofa_serve_port)/v1"
export LITELLM_API_KEY="$(cat $OFA_SCRATCH/.ofa_api_key)"
export LITELLM_MODEL="ofa-code"
```

See ofa's [Bring your own agent](https://github.com/nileshsawant/onfield-assistant#bring-your-own-agent) section for the full recipe and other integrations.

## opencode

[opencode](https://opencode.ai) is a terminal-native, open-source AI coding agent (also available as a desktop app / IDE extension). NLR's build is enabled to talk to two kinds of models:

* **ServeAI Gateway** &mdash; NLR-managed models hosted centrally on OpenStack, with no personal GPU allocation required: Devstral 2 123B, GPT-OSS 120B, Gemma 4 31B, Nemotron 3 Super 120B, and Nemotron 3 Nano 30B, all with tool-calling enabled.
* **Local Node Model** &mdash; models you run yourself via Ollama on a GPU allocation (`gpt-oss:120b`, `gemma4:31b`), reached at `$OLLAMA_HOST` (`127.0.0.1:11434` by default).

`module load opencode` is only available on Kestrel's GPU login nodes (`kestrel-gpu.hpc.nlr.gov`, i.e. `kl5`/`kl6`, which run RHEL9) &mdash; it is not on the CPU/RHEL8 login node stack:

```
ssh kestrel-gpu.hpc.nlr.gov
module load opencode
opencode
```

The first time you load the module, it generates a per-user config at `~/.config/opencode/opencode-kestrel.json` with both providers above pre-wired and a conservative default permission policy (for example, `git push` and `rm` require confirmation or are denied outright). It leaves the file untouched on subsequent loads, so any customization you make is preserved.

!!! note
    Opencode's Kestrel rules instruct it to warn you if it is about to run a local-Ollama model from a login node &mdash; that workload needs a GPU job (`salloc`/`sbatch`), not the shared login node itself.

opencode can also add `ofa` as another provider (see [Through opencode](#4-through-opencode) above), giving you the entire `ofa` mode family alongside opencode's built-in providers in the same model picker.
