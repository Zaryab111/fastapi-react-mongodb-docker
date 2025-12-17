FROM python:3.12-slim-bookworm

ENV PYTHONUNBUFFERED=true
WORKDIR /app

# Install uv
COPY --from=ghcr.io/astral-sh/uv:0.5.11 /uv /uvx /bin/

# Put venv executables first
ENV PATH="/app/.venv/bin:$PATH"

ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy

# Install dependencies (creates /app/.venv)
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --frozen --no-install-project

# Copy app
ADD pyproject.toml README.md ./
ADD app /app/app

# Sync project deps
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync

# Install compatible passlib/bcrypt into the VENV (NOT system python)
RUN uv pip install --python /app/.venv/bin/python --force-reinstall \
    "passlib==1.7.4" "bcrypt==3.2.2"

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "1"]
