# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A curated **monorepo of ~110 independent, self-contained example apps** demonstrating LLM
patterns: RAG, single/multi-agent systems, voice agents, MCP agents, memory, and fine-tuning.
There is **no root-level package, build system, shared dependency set, or repo-wide test/lint
suite.** Each example owns its own `requirements.txt` and `README.md` and is meant to be run in
isolation. Treat every subdirectory as its own mini-project — do not try to install or run things
from the repo root.

## Working in an individual app

Always `cd` into the specific app directory first, then:

```bash
cd <category>/<app_name>
python -m venv .venv && source .venv/bin/activate   # recommended; envs are not shared between apps
pip install -r requirements.txt
```

Run the app — the launch command depends on the app type (check its README):
- **Streamlit UIs** (the majority, ~80 apps): `streamlit run <file>.py`. The entry file is usually
  `app.py` or `main.py`, but is often named after the app (e.g. `travel_agent.py`, `vision_rag.py`).
- **CLI / MCP agent scripts**: `python <file>.py` (e.g. `python notion_mcp_agent.py`).

When unsure how a given app starts, read that app's `README.md` — each one documents its exact
prerequisites and run command.

## API keys & secrets

Apps obtain provider keys one of two ways (sometimes both):
- **Streamlit sidebar input at runtime** (~70 apps) — keys are typed into the running UI, not the
  environment.
- **Environment variables via `.env` + `python-dotenv`** (~57 apps) — `os.getenv("OPENAI_API_KEY")`
  etc. `OPENAI_API_KEY` is by far the most common; others include `ANTHROPIC_API_KEY`,
  `GOOGLE_API_KEY`, `FIRECRAWL_API_KEY`, `TOGETHER_API_KEY`, `PERPLEXITY_API_KEY`, `GITHUB_TOKEN`.

## Tests & linting

There is no repository-wide test or lint config. The only subproject with its own test suite and
linter is **`beifong`** (`advanced_ai_agents/multi_agent_apps/ai_news_and_podcast_agents/beifong/`),
which has `tests/` (pytest, files named `*_test.py`) and a `ruff.toml`. Run those from inside that
directory after installing its requirements:

```bash
cd advanced_ai_agents/multi_agent_apps/ai_news_and_podcast_agents/beifong
pytest tests/                      # all tests
pytest tests/agent_agno_test.py    # a single test file
ruff check .
```

## Architecture & layout

Apps are grouped by category at the top level; each leaf directory is one app:

- `starter_ai_agents/` — small single-purpose agents (good reference for minimal patterns).
- `advanced_ai_agents/` — `single_agent_apps/`, `multi_agent_apps/` (including `agent_teams/`),
  and `autonomous_game_playing_agent_apps/`.
- `voice_ai_agents/` — speech in/out agents (e.g. OpenAI voice, audio tours).
- `mcp_ai_agents/` — agents that drive external **MCP servers**; these typically use agno's
  `MultiMCPTools` to launch MCP servers over stdio via `npx` (see
  `mcp_ai_agents/multi_mcp_agent/multi_mcp_agent.py` for the multi-server pattern).
- `rag_tutorials/` — RAG variants (agentic, corrective/CRAG, hybrid search, local, vision, etc.),
  generally backed by a vector store (`qdrant-client`, `chromadb`) and sometimes local models.
- `advanced_llm_apps/` — `llm_apps_with_memory_tutorials/` (uses `mem0ai`), `chat_with_X_tutorials/`,
  and `llm_finetuning_tutorials/`.
- `ai_agent_framework_crash_course/google_adk_crash_course/` — a **numbered, sequential tutorial**
  (`1_starter_agent` → `7_plugins`); steps build on each other, so read them in order.
- `docs/` — README banner assets only.

### Frameworks you'll encounter
Each app picks one agent framework; the common ones (by frequency) are **agno** (formerly phidata),
**LangChain / LangGraph**, **OpenAI** and the **OpenAI Agents SDK**, **CrewAI**, and **Google ADK**.
Local-model apps use **Ollama**. Don't assume a single framework across apps — confirm per app via
its imports and `requirements.txt`.

## Adding or modifying an app

Follow the existing structure: each app is a new folder under the appropriate category, containing
its own `requirements.txt` and a `README.md` documenting setup and the exact run command. Keep
changes scoped to the one app's directory; there is no shared library to update.
