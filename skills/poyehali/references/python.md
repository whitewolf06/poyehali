# Python API with FastAPI

Use this profile when a project already has a Python API or when the user selects Python for a separate API. FastAPI is the personal preset for a new Python HTTP API. Keep the frameworks, dependency manager, migrations, and checks already in use when extending a project.

## Rules for the project

- Give each feature module an owner. HTTP handlers translate validated requests into application calls and shape the response. Keep domain behavior separate from routing and storage. Keep cross-module calls behind the owning module's public interface.
- Use typed boundaries and explicit FastAPI request and response models. Modify request, response, and error behavior with HTTP contract tests. In a split UI and API, keep the API as the owner of validation and data and document how the frontend consumes the contract.
- Add PostgreSQL, SQLAlchemy, and Alembic if a relational database is chosen. Keep transaction boundaries explicit and test schema changes by applying migrations to an isolated database. Use synthetic fixtures and exercise failure paths when behavior has meaningful failure cases.

See [FastAPI's router guide](https://fastapi.tiangolo.com/tutorial/bigger-applications/), [response models](https://fastapi.tiangolo.com/tutorial/response-model/), and [HTTP testing](https://fastapi.tiangolo.com/tutorial/testing/).

## Checks and setup by mode

For the rules-only mode, record the actual toolchain and commands, plus any planned checks. For a new API use [uv](https://docs.astral.sh/uv/concepts/projects/sync/) for a local environment and lockfile, Ruff for lint and format, and pytest for unit and HTTP integration tests. Prefer the existing checker if the repository runs a separate type-check command. With uv, use `uv sync --locked` and, once `app/` and `tests/` exist, run `uv run --locked ruff check app tests`, `uv run --locked ruff format --check app tests`, and `uv run --locked python -m pytest` from the API root. Use the actual source and test paths if they differ. Running pytest as a module allows a minimal, unpackaged app to import its local code.

For config mode, use the confirmed Python version in `pyproject.toml` and the toolchain pin appropriate to the project. Add dev dependencies for Ruff and pytest and retain the project's lock mechanism. Use pytest settings in `pyproject.toml` if the project has no existing pytest config. In CI, install from the resolved lockfile and run only checks that apply to files already present. Keep the test command documented as planned if the test suite is pending.

For app mode:

1. Create an application module, such as `app/main.py`, using `FastAPI` and one typed `GET /api/health` endpoint with JSON `{ "status": "ok" }`. Add `fastapi` and `uvicorn` to the runtime dependency set and `httpx` for FastAPI's `TestClient`. Resolve and retain the lockfile. A standalone health endpoint needs neither persistence nor an external service.
2. Add a pytest smoke test using `TestClient` to assert the status code and JSON. When business behavior exists, use separate tests for it and reserve HTTP integration tests for the route boundary. Run Ruff and pytest using the recorded commands.
3. Start the API locally with `uv run --locked uvicorn app.main:app --host 127.0.0.1 --port 8000` or the recorded equivalent. Verify the endpoint over loopback, or run the frontend and API together when both are in scope. Shut down the local test process afterward.
