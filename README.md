# Multi-Domain Support Triage Agent

A terminal-based support triage agent that classifies and responds to support tickets
across **HackerRank**, **Claude (Anthropic)**, and **Visa** using a curated support corpus
and the Claude API.

---

## Architecture

```
support_issues.csv
       │
       ▼
  ┌──────────────────────────────────────────┐
  │  Pre-flight Safety Checks (no LLM)       │
  │  ├─ Prompt injection / manipulation?     │
  │  └─ High-risk keywords? (fraud, hack…)   │
  └────────────────┬─────────────────────────┘
                   │
                   ▼
  ┌──────────────────────────────────────────┐
  │  Corpus Retrieval                         │
  │  ├─ Select domains by company field       │
  │  └─ Score & rank relevant snippets        │
  └────────────────┬─────────────────────────┘
                   │
                   ▼
  ┌──────────────────────────────────────────┐
  │  Claude API (claude-sonnet-4)             │
  │  System: strict JSON-only triage prompt   │
  │  User:   ticket + corpus + risk hint      │
  └────────────────┬─────────────────────────┘
                   │
                   ▼
         triage_output.csv
  (status, product_area, response,
   justification, request_type)
```

---

## Setup

**Requirements:** Python 3.8+, no external packages needed (stdlib only).

```bash
# Set your Anthropic API key
export ANTHROPIC_API_KEY="sk-ant-..."

# Clone / copy the project folder
cd support-triage-agent
```

---

## Usage

### Process a CSV file

```bash
python agent.py --input support_issues.csv --output results.csv
```

With sample reference file:
```bash
python agent.py \
  --input support_issues.csv \
  --output results.csv \
  --sample sample_support_issues.csv
```

### Interactive single-ticket mode

```bash
python agent.py --interactive
```

You'll be prompted for company, subject, and issue text. The agent prints the full JSON result immediately.

### Options

| Flag | Default | Description |
|---|---|---|
| `--input / -i` | — | Input CSV path |
| `--output / -o` | `triage_output.csv` | Output CSV path |
| `--sample / -s` | — | Sample CSV (for format reference) |
| `--interactive` | off | Single-ticket interactive mode |
| `--delay` | `0.5` | Seconds between API calls (rate-limit buffer) |
| `--verbose` | off | Extra debug output |

---

## Input CSV schema

| Field | Required | Notes |
|---|---|---|
| `issue` | ✅ | Main ticket body |
| `subject` | ❌ | May be blank, noisy, or misleading |
| `company` | ✅ | `HackerRank`, `Claude`, `Visa`, or `None` |

---

## Output CSV schema

| Field | Values |
|---|---|
| `status` | `replied` \| `escalated` |
| `product_area` | Short string e.g. `billing_subscription` |
| `response` | User-facing message grounded in corpus |
| `justification` | Internal reasoning for the decision |
| `request_type` | `product_issue` \| `feature_request` \| `bug` \| `invalid` |

---

## Safety features

1. **Prompt injection detection** — pattern-matched before the LLM sees the ticket
2. **Risk keyword escalation** — fraud, legal threats, account compromise → always escalated
3. **Corpus-grounded responses** — the LLM is instructed (and the corpus constrains it) to never invent policies
4. **Graceful API errors** — failures produce a safe escalation response, never a crash
5. **Company inference** — when `company=None`, the LLM infers from content

---

## Extending the corpus

Edit the `CORPUS` dict in `agent.py`. Each company → area → list of fact strings.
The retrieval step scores snippets by keyword overlap with the issue text.
