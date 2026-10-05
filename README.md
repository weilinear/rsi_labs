# RSI Labs

Small, inspectable experiments in recursive self-improvement.

Both notebooks are self-contained; no local Python modules are needed. The
deterministic reference uses Python's standard library. The Gemini experiment uses
the official `google-genai` SDK and includes a package-install cell for Colab.
Open them in Colab, Jupyter, or VS Code with a Python 3.10+ kernel and run the cells
from top to bottom.

| Notebook | Open in Colab | Purpose |
|---|---|---|
| [Deterministic reference](self_improvement.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/self_improvement.ipynb) | Demonstrates output refinement, persistent agent improvement, and improvement-policy evolution without an LLM or network access. |
| [Gemini experiment](gemini_self_improvement.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/gemini_self_improvement.ipynb) | Uses a fixed Gemini model to propose structured edits, with independent Python execution and evaluation. Contains captured live outputs. |

## Gemini setup

In Colab, add `GOOGLE_API_KEY` to the Secrets panel and enable notebook access.
The notebook includes a setup cell that loads it with:

```python
import os
from google.colab import userdata
os.environ["GOOGLE_API_KEY"] = userdata.get('GOOGLE_API_KEY')
```

Do not paste credentials into notebook cells. For local Jupyter, skip the Colab
secret-loading cell and set `GOOGLE_API_KEY` in the kernel environment. Optionally
set `GEMINI_MODEL`; the default is `gemini-3.8-flash`.

Gemini calls use `genai.Client(...).models.generate_content(...)` with a JSON
schema. The SDK is configured with a 45-second timeout and one attempt, preserving
the experiment's no-retry behavior. See the [SDK documentation](https://googleapis.github.io/python-genai/).

Rerunning the Gemini notebook makes paid or quota-consuming API requests and sends
synthetic task inputs and feedback to Google's API. It caps model calls at 24 and
times out each request after 45 seconds. No automatic retries or silent fallback
are used. Keys are not printed or saved in the notebook's artifacts.

For explicit offline harness checks, set `TOY_BACKEND=replay` before starting the
kernel. Replay uses a hand-authored fixture and is labeled separately from Gemini.

## What the examples demonstrate

- **Output refinement:** correct a current answer without retaining a change.
- **Agent self-improvement:** propose, evaluate, and retain task-rule changes.
- **Recursive self-improvement:** change the configurable improvement procedure,
  then reuse it to generate subsequent improvements.

The Gemini task agent is a configurable Python invoice calculator. Gemini is its
improver; it proposes JSON edits rather than executing invoices or writing code.
The interpreter, reference calculation, and acceptance rule stay fixed. Generated
strings are never executed as Python.

The saved SDK run on October 5, 2026 used 12 Gemini calls and 12,036 reported
tokens. Its selected policy changed the allowed fields per candidate from one to
three. After one candidate evaluation, the resulting agent scored 100% on four
held-out invoices, compared with 25% using the initial improvement policy.

This deliberately bounded toy does not establish broad transfer, equal total
compute, an advantage over manually allowing three-field edits, or unlimited
recursive progress. Exact repeated model requests are cached within a run.

Notebook execution writes inspectable JSON under `toy_artifacts/`,
`gemini_artifacts/`, or `replay_artifacts/`. Those generated directories are ignored
by Git. Saved notebook outputs were captured by executing cells sequentially with
CPython in a shared namespace, rather than through an IPython kernel.
