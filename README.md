# llm-starter

Small playground for practising LLM calls from Python. Both notebooks talk to Gemini through the OpenAI-compatible client and use a shared helper, `scraper.py`, to read websites.

## uv quickstart

This project uses [uv](https://docs.astral.sh/uv/) (Python 3.12) to manage the environment. Install it once per machine:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Then, from the project root:

```bash
uv sync                  # creates .venv and installs everything from uv.lock
```

Create a `.env` file in the project root (it is git-ignored, never commit it):

```
GOOGLE_API_KEY=...
GEMINI_BASE_URL=https://generativelanguage.googleapis.com/v1beta/openai/
GOOGLE_MODEL=<gemini model name>
```

Useful commands:

| Task | Command |
|---|---|
| Add a package | `uv add <pkg>` |
| Remove a package | `uv remove <pkg>` |
| Run a script | `uv run <file.py>` |
| Install from an existing repo | `uv sync` |

To run the notebooks in VS Code / Cursor, open the `.ipynb` file and pick **Select Kernel → Python Environments → `.venv`**. If the kernel is not listed, run `uv sync` and reload the window.

For the full walkthrough (new project setup, Ollama, etc.) see [`setup/UV-QUICKSTART.md`](setup/UV-QUICKSTART.md).

## Files

All under `src/llm_starter/`.

### `scraper.py`

Helper module used by both notebooks.

- `fetch_website_contents(url)` returns the page title plus its body text (scripts, styles, images and inputs stripped), truncated to 2,000 characters.
- `fetch_website_links(url)` returns every `href` found on the page.

### `summariser.ipynb`

Summarises a single web page in a snarky tone.

1. Loads the Gemini key, base URL and model from `.env`.
2. Fetches the page text with `fetch_website_contents`.
3. Sends it to the model with a system prompt asking for a short, humorous markdown summary (ignoring navigation text), and mentioning any news or announcements.
4. `summarise(url)` returns the text; `display_summary(url)` renders it as markdown in the notebook.

The example at the bottom summarises `https://www.canva.in/`. Change the URL to try another site.

### `brochure.ipynb`

Builds a short company brochure from a website, using two LLM calls.

1. **Pick the links.** `fetch_website_links` collects all links on the landing page, and the model returns the relevant ones (About, Careers, etc.) as JSON, skipping terms, privacy and email links.
2. **Gather the content.** The landing page and each selected page are fetched with `fetch_website_contents` and combined into one block of text, truncated to 5,000 characters.
3. **Write the brochure.** A second call turns that text into a markdown brochure aimed at customers, investors and recruits, covering culture, customers and careers where the information is available.

`create_brochure(company_name, url)` runs the whole flow and renders the result. The example at the bottom generates one for Hugging Face.
