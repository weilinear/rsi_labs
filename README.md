# RSI Labs

Small, inspectable experiments in agent self-improvement.

| Notebook | Open in Colab | Purpose |
|---|---|---|
| [Restaurant-bill agent](gemini_self_improvement.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/gemini_self_improvement.ipynb) | Gemini learns a user's tip convention, writes a calculator and a simpler wrapper, then revises its improvement policy. |
| [Earlier deterministic reference](self_improvement.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/self_improvement.ipynb) | The original invoice example, without a model or network access. |

## Restaurant-bill example

The notebook is self-contained. It uses the official `google-genai` SDK and has a
package-install cell. In Colab, add `GOOGLE_API_KEY` to **Secrets**, enable notebook
access, and run the setup cell:

```python
import os
from google.colab import userdata
os.environ["GOOGLE_API_KEY"] = userdata.get('GOOGLE_API_KEY')
```

The model defaults to `gemini-3.8-flash`; override it with `GEMINI_MODEL`.
For local Jupyter, install the SDK and skip the Colab secret cell with
`GOOGLE_API_KEY` already in the kernel environment. No companion Python files are
needed. Run the experiment cells from top to bottom.

The story distinguishes several changes:

1. Correct one bill temporarily, then retain the user's preference to tip before tax.
2. Have Gemini write a reusable calculator and check it against an independent oracle.
3. Demonstrate a constructed dollars-versus-cents regression; write and verify a simpler wrapper.
4. Revise the improver's instructions and evaluate its repair choices on new failure cases.

The last score measures **repair-choice accuracy**, not end-to-end agent improvement.
The first tool interventions are directed demonstrations. Diagnostic cases are
human-authored, the initial improver is deliberately weak, and there is no claim of
broad transfer, equal compute, or sustained recursive acceleration.

Generated Python is checked against a small arithmetic syntax allowlist before
execution. This is a narrow toy validator, not a general code sandbox. Tools are
retained only after development checks; the calculator also has a held-out check.
The user's preference and independent evaluator remain external and fixed.

Live execution consumes API quota and sends synthetic inputs, feedback, and code to
Google. Keys are never printed or stored. Requests have a 45-second timeout, no
retries, and a 24-call cap. Retained tools and policies live in the current session.

Saved experiment outputs were captured by executing cells sequentially with CPython
and the SDK; the Colab setup cells were checked separately. The earlier deterministic
notebook remains available as a historical reference.
