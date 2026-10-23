# Replit development setup

The executable `hooks/on_build` prepares Python dependencies and installs the
repository's pre-commit hook when a Replit Development Environment builds this
checkout. It can also be run manually from any directory.

## Machine prerequisites

Install `uv`, a C compiler and build tools, `libmagic`, and PostgreSQL development
libraries before running the hook. On an Ubuntu base, the system packages are:

```sh
apt-get update
apt-get install -y build-essential libmagic1 libpq-dev postgresql redis-server
```

Install `uv` using its [official installation instructions](https://docs.astral.sh/uv/getting-started/installation/).
`uv sync --locked` selects Python 3.12 from the project's requirements and installs
the development dependencies without updating `uv.lock`.

Run:

```sh
.nexus/hooks/on_build
```

The hook does not start services, run migrations, populate sample data, configure
GitHub access, or create a saved Replit environment. Dependencies remain in the
ignored `.venv` directory; no application source or configuration is rewritten.

## Development and tests

Start PostgreSQL and Redis before running database-backed tests. Use the existing
[contributing guide](../CONTRIBUTING.md) for local configuration, migrations,
server commands, and test selection. Keep credentials out of commits.

With the development environment variables configured:

```sh
uv run poe test saleor/core/tests/test_core.py -n0
```

The initial Replit setup passed all 49 tests in this file using Python 3.12 and
PostgreSQL 18. The repository's CI uses PostgreSQL 15; that version remains the
reference for CI parity. This smoke test is not a full-suite validation.

## Pull requests

Use a writable fork as `origin`, and the intended target repository as `upstream`.
For this fork, the target is `jordwalke/saleor`, not `saleor/saleor`.
Set `remote.pushDefault` to `origin` and specify the target explicitly:

```sh
git push -u origin HEAD
gh pr create --repo jordwalke/saleor --base main --head YOUR_ACCOUNT:YOUR_BRANCH
```

GitHub credentials are supplied by the Replit GitHub connection, not this hook.
A successful environment build does not establish permission to push or open PRs.
