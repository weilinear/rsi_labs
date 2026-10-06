# RSI Labs

Small, inspectable experiments in agent self-improvement.

| Notebook | Open in Colab | Purpose |
|---|---|---|
| [Grocery replenishment](grocery_replenishment.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/grocery_replenishment.ipynb) | Real retailer listing evidence; propose an acceptable cheaper refill and improve instructions or memory from observed responses. |
| [Trajectory-based improvement](trajectory_improvement.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/trajectory_improvement.ipynb) | A fixed improver uses observed bill-solving trajectories to choose instructions, a tool, or no change; compares future task accuracy. |
| [Restaurant-bill agent](gemini_self_improvement.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/gemini_self_improvement.ipynb) | Gemini learns a user's tip convention, writes a calculator and a simpler wrapper, then revises its improvement policy. |
| [Earlier deterministic reference](self_improvement.ipynb) | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/weilinear/rsi_labs/blob/main/self_improvement.ipynb) | The original invoice example, without a model or network access. |

## Grocery replenishment: approximate the real problem

`grocery_replenishment.ipynb` uses ten public Target product pages retrieved on
October 6, 2026. It captures observed package sizes, variants, displayed prices,
and real gaps such as store-specific stock, incomplete promotions, and a title /
highlight conflict. Listing summaries include source URLs. No fees, checkout
quotes, or personal purchase history are invented.

The example shopper and eight shopping requests are illustrative. Four requests
are development cases, four are held out, and each gets two attempts. The first
version uses one retailer and three staple categories, so it avoids inventing a
complete multi-store market. Captured prices may come from cached pages and are
not guaranteed current. Human annotations check a limited proxy: acceptable
products, whole packs, sufficient quantities, evidence-linked price arithmetic,
and appropriate uncertainty or substitution questions. Any supported cheaper
candidate can succeed; there is no globally optimal target basket. Read the
actual explanations as well as the score. No purchases are made, and estimated
item-price savings are not verified checkout savings.

The fixed improver proposes task instructions and procedural memory from real
development responses. It can choose no change. Model, evidence, rubric, and
harness remain fixed; tool writing and recursion are deferred. Candidate retention
requires a strict development rubric gain without API failures. Both frozen
versions are then evaluated on held-out requests. The complete pilot attempts 33
model calls, with a 90-second timeout, two workers, and no automatic retries.
Reported tokens cover only calls for which the API returned usage. The notebook
preserves expandable evidence and trajectories.

An initial pilot is excluded from improvement claims: it had API failures and an
overly strict uncertainty-checklist gate. A missing checklist word could fail an
otherwise qualified and correct basket recommendation. We separated checklist
completeness from shopping correctness before the fresh comparison; equivalent
Target URLs for the same product are also accepted. The rubric and its limitations
are explicit, and the initial record is preserved for inspection.

Recorded fresh pilot: original held-out basket checks **8/8**, candidate **5/8**
with three failed/incomplete requests. Every completed recommendation passed
the limited basket checks, so no policy gain was established; the original
was retained. The fresh run attempted 33 calls and returned 141,568 reported
tokens. The excluded first pass attempted another 33 calls and returned 90,424
reported tokens. Request/execution failures are reported separately from basket
errors. This is preliminary evidence about an illustrative shopping task, not
user acceptance, verified checkout savings, or broad self-improvement.

## Trajectory-based improvement

Start with `trajectory_improvement.ipynb` for the measured improvement experiment.
It uses 48 synthetic bills, a stratified 24/24 development and held-out split,
and two attempts per bill. Tipping and rounding rules are explicit. The fixed
improver receives development trajectories only and proposes either an
instruction-only change or an open choice that may write a calculator. No repair
labels or manufactured model failures are supplied. No recursive improvement is
attempted.

All three versions use one model call per bill. For a tool request, the fixed
harness executes Gemini's chosen arguments and returns the tool result directly;
there is no additional model narration. Candidates are retained only after a
strict development-accuracy gain; held-out scores never select a version. Ties
are reported as no demonstrated accuracy improvement. Ordinary currency prefixes
are normalized before comparison, so formatting is not mislabeled as arithmetic.

Each full run makes up to 290 model calls, without retries. Latency and token
counts include proposal and evaluation calls in the complete experiment record.
Requests have a 45-second timeout. API failures are flagged separately. Saved
outputs contain the measured score summaries; complete trajectory records remain
in the notebook session as `experiment`. Repeats are not independent new bills.

The recorded run used `gemini-3.8-flash`: the original scored **96/96** across
48 distinct bills. Both proposals chose **no change**; all 192 re-evaluation
attempts also passed. No calculator was written or used. There was no demonstrated
accuracy improvement, and this run is not a manual-versus-tool ablation. The
complete run used 290 calls and 220,109 reported tokens, excluding four preliminary
smoke-test calls. The notebook includes an expandable record of all trajectories
and both proposals, in addition to concise score summaries.

## Earlier restaurant-bill demonstration

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
