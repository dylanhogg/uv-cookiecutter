.EXPORT_ALL_VARIABLES:
DEV=True

venv:
	# Install https://github.com/astral-sh/uv on macOS and Linux:
	# $ curl -LsSf https://astral.sh/uv/install.sh | sh
	# Other recommended libraries, add with `uv add <library>`:
	# tenacity, joblib, jupyterlab, litellm, datasets, pytorch, fastapi, uvicorn, rich
	uv sync --all-groups
	uv pip install -e .

which-python:
	uv run which python
	uv run which python | pbcopy

clean:
	rm -rf .venv

agent-skills-symlink:
	ln -s ../.agents/skills ./.claude/skills
	ln -s ../.agents/skills ./.codex/skills

agent-skills-verify:
	ls -la ./.claude/skills ./.codex/skills
	readlink ./.claude/skills
	readlink ./.codex/skills

run:
	uv run python -m {{cookiecutter.package_name}}.app reqarg1 --optional-arg "optional arg"

run-arg:
	@echo "Usage: make run-args REQARG=reqarg1 OPTARG=optarg1"
	@test -n "$(REQARG)" || (echo "Error: REQARG parameter required" && exit 1)
	uv run python -m {{cookiecutter.package_name}}.app $(REQARG)

run-as-tool:
	uv run app reqarg1 --optional-arg "optional arg"

docker-build:
	docker compose build --no-cache

run-as-docker:
	docker compose run --rm app reqarg --optional-arg optarg

test:
	uv run pytest -vv --capture=no --no-cov tests

test-selected:
	uv run pytest -vv --capture=no --no-cov tests -k "test_app"

test-cov:
	uv run pytest -vv --capture=no --cov-report=term-missing --cov-report=html tests

test-as-docker:
	docker compose run --rm tests

manual-checks:
	uv run ruff format .
	uv run ruff check . --fix
	uv run pyright

precommit-install:
	# One time: Install git hook to run pre-commit automatically on git commit
	# Uninstall with: uv run pre-commit uninstall
	uv run pre-commit install

precommit:
	uv run pre-commit run --all-files

build-dist:
	rm -rf dist
	rm -rf build
	rm -rf sqlexplore.egg-info
	uv build

publish-check:
	uv publish --dry-run

publish-pypi:
	@test -n "$$UV_PUBLISH_TOKEN" || (echo "UV_PUBLISH_TOKEN is required"; exit 1)
	uv publish

.DEFAULT_GOAL := help
.PHONY: help
help:
	@LC_ALL=C $(MAKE) -pRrq -f $(lastword $(MAKEFILE_LIST)) : 2>/dev/null | awk -v RS= -F: '/^# File/,/^# Finished Make data base/ {if ($$1 !~ "^[#.]") {print $$1}}' | sort | egrep -v -e '^[^[:alnum:]]' -e '^$@$$'
