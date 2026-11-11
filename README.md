# Resume skill evidence for human review

A small Flask application that locates explicit skill terms in resume text and job requirements, then creates a consistent set of interview prompts. The output is evidence for a reviewer to inspect, rather than a candidate score.

**Python · Flask · Regular expressions · Input validation**

[Matching logic](core.py) · [Boundary and rendering tests](test_app.py) · [Evaluation cases](evaluate.py)

## Try it in one minute

Python 3.12, from the repository root:

```bash
python -m venv .venv
# Windows PowerShell: .venv\Scripts\Activate.ps1
. .venv/bin/activate
pip install -r requirements-dev.txt
python app.py
```

Open http://127.0.0.1:5001. Submit the prefilled fictional sample; no account, API key or external model is required.

For resume text `Python and SQL` and job text `SQL Docker`, the result contains:

```json
{
  "evidence_terms": ["sql"],
  "terms_to_review": ["docker"]
}
```

The full response also contains interview prompts. `Docker` being absent from the text does not establish that the person lacks the skill.

![Skill evidence and interview prompts](artifacts/demo.png)

## Why deterministic matching?

The supported vocabulary is deliberately explicit: Python, SQL, Flask, React, Docker, Pandas, TensorFlow and Excel. Case-insensitive word boundaries prevent matches such as `Python` inside `pythonic` and `SQL` inside `NoSQL`.

For this narrow task, a regular-expression matcher is easy to inspect, requires no third-party inference service and returns repeatable outputs. It also has clear limits: it cannot distinguish genuine experience from copied or negated text. “I do not know Python” still contains the term. A human must review the original context.

## Validation and behavior

```bash
python -m pytest -q
python evaluate.py
```

The five tests cover word boundaries, expected evidence sets, a basic name/age substitution check, empty input and escaped script input. The evaluation reports three authored exact-match cases. These are functional checks; neither establishes hiring validity or fairness across populations.

Each document is limited to 20,000 characters. Empty input and job descriptions without supported terms produce actionable errors. Flask/Jinja escapes user text in the HTML output.

## Architecture

`app.py` handles form input and rendering; `core.py` extracts terms and compares sets. The application does not store form contents or call an external model. Local request metadata can appear in development-server logs. Use fictional or appropriately consented text; the example dataset contains no real applicants.

## Scope and responsible use

There is no PDF parsing, job-fit prediction, candidate ranking, personality inference or emotion detection. The interface supports a human-led interview workflow. No protected-trait field is used, but that alone does not prove the workflow is fair. An operational system would need privacy controls, consent, accessibility review and a validated review process.

The server is local development software; authentication, abuse controls and production hosting are not included. Docker is an unvalidated recipe.

## Next useful improvement

Add evidence spans so the reviewer can see the sentence containing each term, then test negation and ambiguous mentions explicitly. Improve synonyms only with documented fixtures; avoid silently converting term coverage into an applicant score.

Source code and authored examples are MIT. [Project provenance](PROVENANCE.md) distinguishes this portfolio implementation from the earlier CV project idea; [license notes](LICENSE-REVIEW.md) cover dependencies.
