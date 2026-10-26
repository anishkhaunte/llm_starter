# Minimal uv setup for LLM practice

## 1. Install uv (once per machine)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Open a new terminal, then check: `uv --version`

## 2. Create the project

```bash
mkdir my-llm-project && cd my-llm-project
uv init --python 3.12        # creates pyproject.toml, .python-version, main.py
```

## 3. Add the minimum packages

```bash
uv add openai python-dotenv ipykernel
```

Add more only when needed, e.g. `uv add anthropic ollama gradio requests beautifulsoup4`.

This creates `.venv/` and `uv.lock` automatically. No manual activation is needed.

## 4. API keys

Create a `.env` file in the project root (exact name, nothing else):

```
OPENAI_API_KEY=sk-...
GOOGLE_API_KEY=...
```

Make sure it is never committed:

```bash
echo ".env" >> .gitignore
echo ".venv" >> .gitignore
```

## 5. Use it in code

```python
from dotenv import load_dotenv
import os
from openai import OpenAI

load_dotenv(override=True)
client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
```

For Gemini through the OpenAI client, pass
`base_url="https://generativelanguage.googleapis.com/v1beta/openai/"` and `api_key=os.getenv("GOOGLE_API_KEY")`.

## 6. Daily commands

| Task | Command |
|---|---|
| Add a package | `uv add <pkg>` |
| Remove a package | `uv remove <pkg>` |
| Run a script | `uv run main.py` |
| Install from an existing repo | `uv sync` |
| Update uv | `uv self update` |

Use `uv add` and `uv run` instead of `pip install` and `python`.

## 7. Notebooks (Cursor / VS Code)

Open the `.ipynb` file, then use **Select Kernel → Python Environments → `.venv`**.
This works because `ipykernel` is installed. If the kernel is not listed, run `uv sync` and reload the window.

## Optional: local models with Ollama

Install Ollama separately, then run `ollama pull llama3.2` and `uv add ollama`.
It needs no API key. Point the OpenAI client at `base_url="http://localhost:11434/v1"` with `api_key="ollama"`.
