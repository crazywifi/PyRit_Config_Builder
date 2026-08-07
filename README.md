# PyRit Config Builder

**URLs:**

**PyRit Builder:** https://crazywifi.github.io/PyRit_Config_Builder/pyrit_config_builder_all_in_one.html

**PyRit Guide:** https://crazywifi.github.io/PyRit_Config_Builder/PyRIT_Guide.html

**PyRit Custom Script Guide:** https://crazywifi.github.io/PyRit_Config_Builder/pyrit_custom_script_builder_lab_guide.html

**PyRit Scanner Custom Guide:** https://crazywifi.github.io/PyRit_Config_Builder/pyrit_scanner_custom_http_lab_guide.html

---
**Universal configuration builder and lab guides for Microsoft [PyRIT](https://microsoft.github.io/PyRIT/) (Python Risk Identification Tool).**


This toolkit helps security testers, red teamers, and trainers attack **their own** AI / LLM applications in a controlled way — without writing everything from scratch.

It includes:

| File | What it is |
|------|------------|
| **`pyrit_config_builder_all_in_one.html`** | Interactive **Config Builder** (open in a browser). Generates custom Python attack scripts and PyRIT Scanner configs. |
| **`PyRIT_Guide.html`** | Full teaching guide: Framework vs Scanner, annotated code examples, Scanner files explained, teacher mental model. |
| **`pyrit_custom_script_builder_lab_guide.html`** | Step-by-step lab guide for the **Custom Script Builder** path. |
| **`pyrit_scanner_custom_http_lab_guide.html`** | Step-by-step lab guide for the **PyRIT Scanner** path on a custom HTTP lab. |

---

## Tool name

**Suggested repository / product name:**

### **PyRit Config Builder**

Short description for GitHub:

> Browser-based config builder and training guides for Microsoft PyRIT — generate Framework scripts or Scanner configs for any HTTP chat API, including LM Studio–friendly setups.

Alternate names (if you prefer):

| Name | Tone |
|------|------|
| **PyRIT Config Builder** | Focus on the interactive HTML tool only |
| **PyRIT Red Team Workbench** | Broader “lab + training” feel |
| **AIRT Lab Kit** | Emphasizes Microsoft AIRT scenarios |

---

## What problem this solves

Microsoft PyRIT is powerful, but many users are not full-time Python developers. They need:

1. **A visual way** to build configs (targets, converters, scorers, scenarios)
2. **Clear guides** for two official usage paths:
   - **Framework** — your own Python script (`PromptSendingAttack`, Red Teaming, etc.)
   - **Scanner** — `pyrit_scan` with Microsoft AIRT scenarios
3. **HTTP lab support** — register a local chat API (e.g. `http://127.0.0.1:5000/chat`) as an `HTTPTarget`
4. **LM Studio compatibility** — work around `response_format=json_object` limits for SelfAsk scorers

This toolkit addresses all four.

---

## Two paths (mental model)

```
PyRIT
 ├── Framework (Python code)
 │     You write / generate pyrit_test.py
 │     PromptSendingAttack (“Brute Force”) or LLM-Driven attacks
 │     Run:  python pyrit_test.py
 │     No pyrit_scan backend required
 │
 └── Scanner (pyrit_scan CLI)
       Microsoft AIRT scenarios (jailbreak, leakage, cyber, …)
       Files: custom_target_init.py + scanner.env + pyrit_custom_scan.yaml
       (+ lmstudio_pyrit_proxy.py if using LM Studio)
       Run:  pyrit_scan --start-server --config-file ./pyrit_custom_scan.yaml
             pyrit_scan airt.jailbreak --target lab_target ...
```

| | **Framework** | **Scanner** |
|--|---------------|-------------|
| Who writes attacks | You (or your attacker LLM) | Microsoft datasets + techniques |
| Fixed prompts | `PromptSendingAttack` | Technique `prompt_sending` |
| Backend server | Not required | Required |
| Best for | Custom flags, demos, client labs | Standardized AIRT assessment |

---

## Quick start

### 1. Prerequisites

```bash
pip install -U pyrit
python -c "import pyrit; print(pyrit.__version__)"
```

- Lab API (optional but typical): `http://127.0.0.1:5000`
- Optional local judge/attacker: [LM Studio](https://lmstudio.ai/) on port `1234`
- A **fresh session cookie** if your lab uses sessions

### 2. Open the Config Builder

1. Download or clone this repository.
2. Open **`pyrit_config_builder_all_in_one.html`** in Chrome / Edge / Firefox (double-click or “Open with browser”).
3. Choose a mode:
   - **Custom Script Builder** → generate `pyrit_test.py`
   - **PyRIT Scanner** / **Scanner V2** → generate YAML, env, init script, and run commands

No install step is required for the HTML builder itself (it is a self-contained page).

### 3. Follow the matching lab guide

| You chose… | Read this |
|------------|-----------|
| Custom Script Builder | `pyrit_custom_script_builder_lab_guide.html` |
| PyRIT Scanner on HTTP lab | `pyrit_scanner_custom_http_lab_guide.html` |
| Full teaching / presentation | `PyRIT_Guide.html` |

---

## Using the Config Builder

### Custom Script Builder (Framework)

Typical flow:

1. **Attack mode** — Brute Force (fixed prompts) or LLM-Driven (objective + strategy)
2. **Target API** — paste a raw HTTP request, pick the message field, set response JSON path
3. **LLM provider** — only if you need an attacker or SelfAsk judge
4. **Converters** — optional (Base64, jailbreak template, …)
5. **Scoring** — None / SubString / Regex / SelfAsk / …
6. **Options** — memory, timeout, output format
7. **Generate** — download `pyrit_test.py` and run:

```bash
python pyrit_test.py
```

### PyRIT Scanner (AIRT scenarios)

Typical flow:

1. Paste lab HTTP request → register as e.g. `lab_target`
2. Configure judge / adversarial models in generated `scanner.env`
3. Download:
   - `custom_target_init.py`
   - `scanner.env`
   - `pyrit_custom_scan.yaml`
   - `lmstudio_pyrit_proxy.py` (if using LM Studio)
4. Start services in order: lab → LM Studio → proxy (if needed) → PyRIT backend
5. Run:

```bash
pyrit_scan --stop-server
pyrit_scan --start-server --config-file ./pyrit_custom_scan.yaml
pyrit_scan --list-targets
pyrit_scan airt.jailbreak --target lab_target --techniques default --max-dataset-size 1 --max-concurrency 1
```

---

## Repository layout (suggested)

```text
pyrit-lab-toolkit/
├── README.md
├── pyrit_config_builder_all_in_one.html    # Interactive builder
├── PyRIT_Guide.html                       # Full teaching guide
├── pyrit_custom_script_builder_lab_guide.html
├── pyrit_scanner_custom_http_lab_guide.html
└── examples/                              # optional: sample generated scripts
```

---

## Important notes

- **Only test systems you own or have permission to test.**
- Scanner **success %** is a **judge score summary**, not “percentage of a database dumped.” Keep prompt + response evidence (SQLite / JSON export).
- **LM Studio** often rejects PyRIT’s `response_format: json_object`. Use the included compatibility proxy on port **1235**, or prefer SubString/Regex scorers for local-only runs.
- Framework scripts use `{{PROMPT}}`; Scanner init scripts usually use `{PROMPT}`. Both must match `prompt_regex_string`.

---

## Compatibility

- Designed for **PyRIT 1.0.x**
- Works with custom **HTTP** chat labs, OpenAI, Azure, Ollama, and LM Studio
- Guides and builder assume a text-oriented HTTP JSON API (not vision-only pipelines)

---

## Credits

- [Microsoft PyRIT](https://microsoft.github.io/PyRIT/) — Python Risk Identification Tool  
- [PyRIT documentation](https://microsoft.github.io/PyRIT/1.0.1/) 
- This toolkit (Config Builder + lab guides) is a community training / productivity layer around official PyRIT APIs.

---

## License

State your preferred license here (e.g. MIT).  
PyRIT itself is governed by Microsoft’s license on the upstream repository.

---

## Contributing

Issues and PRs are welcome for:

- Builder UX fixes
- New converter / scorer presets
- Lab guide corrections for newer PyRIT releases
- Additional language translations of the guides
