# 00-prerequisites/ - the raw Messages API on-ramp

Ten notebooks: `001_requests`, `002_system_prompt`, `003_temperature`, `004_streaming`, `005_controlling_output`, the `_exercise` variants of 001, 002, and 005, plus `first_request.ipynb` and `multi_turn_conversation.ipynb`. Start here if the raw Messages API is new to you. Everything the live class builds on top of - tool use, agentic loops, structured output - assumes you're comfortable with what these ten cover.

Adapted with thanks from [jaozc/building-with-the-claude-api](https://github.com/jaozc/building-with-the-claude-api/tree/main).

All **seven non-exercise notebooks are smoke-verified green** against the live API, zero error cells.

Three changes versus the upstream source make them run in this repo's **uv**-managed world:

- **The install cell uses uv, not `%pip`.** uv-managed venvs ship without pip, so the first cell shells out to `uv pip install` pointed at the running kernel's interpreter. It's idempotent and no-ops when the packages are already present.
- **Model is `claude-haiku-4-5`**, the repo default (see the model policy in the root [`CLAUDE.md`](../../CLAUDE.md)).
- **Prompts are Azure-first.** The streaming and controlled-output demos request Azure Event Grid and Azure CLI samples.

## Kernel and dependencies

Every notebook here stamps `kernelspec.name = "claude-architect"`, the same kernel the rest of `notebooks/` uses - there is no separate venv for this suite. Dependencies come from `notebooks/pyproject.toml`, one level up. If the kernel is missing on a fresh clone, register it once from the repo root:

```powershell
uv run --project notebooks python -m ipykernel install --user --name claude-architect --display-name "Claude Architect (notebooks/.venv)"
```

An API key must be present. These notebooks read `notebooks/.env` (gitignored) via `python-dotenv`, so put `ANTHROPIC_API_KEY=...` there, or export it in your shell.

## Conventions

- Attribution to the upstream source lives in each notebook's first cell.
- Smoke artifacts (`_smoke-*.ipynb`) are gitignored, same as the rest of `notebooks/`. They're transient by design.
