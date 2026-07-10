# Use an official uv image with Python 3.11 pre-installed
FROM ghcr.io/astral-sh/uv:python3.11-trixie-slim

WORKDIR /app

# Avoid buffered logs so container output is timely
ENV PYTHONUNBUFFERED=1

# Compile bytecode for faster startup
ENV UV_COMPILE_BYTECODE=1

# Copy from the cache instead of linking (cache mounts use a separate filesystem)
ENV UV_LINK_MODE=copy

# Install production dependencies only
ENV UV_NO_DEV=1

# Ensure installed tools are on PATH by default
ENV UV_TOOL_BIN_DIR=/usr/local/bin

# Install dependencies first for better layer caching
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --locked --no-install-project

# Copy application source and finalize the environment
COPY . /app
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --locked

# Place the project virtual environment at the front of PATH
ENV PATH="/app/.venv/bin:$PATH"

# Do not invoke `uv` as the image entrypoint
ENTRYPOINT []

EXPOSE 8000

CMD ["fastapi", "run", "main.py", "--host", "0.0.0.0", "--port", "8000"]
