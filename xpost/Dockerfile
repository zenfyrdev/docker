FROM python:3.12-alpine
COPY --from=ghcr.io/astral-sh/uv:0.7.12 /uv /uvx /bin/

WORKDIR /app

# Install build tools & runtime dependencies
RUN apk add --no-cache \
    ffmpeg \
    file \
    libmagic

# Enable bytecode compilation
ENV UV_COMPILE_BYTECODE=1

# Copy from the cache instead of linking since it's a mounted volume
ENV UV_LINK_MODE=copy

# Install the project's dependencies using the lockfile and settings
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=./xpost/uv.lock,target=uv.lock \
    --mount=type=bind,source=./xpost/pyproject.toml,target=pyproject.toml \
    uv sync --locked --no-install-project --no-dev

# Define app data volume
VOLUME /app/data

# Then, add the rest of the project source code and install it
COPY ./xpost /app
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --locked --no-dev

RUN adduser -D -u 1000 app && \
    chown -R app:app /app

USER app

# Place executables in the environment at the front of the path
ENV PATH="/app/.venv/bin:$PATH"

# Set entrypoint to run the app using uv
ENTRYPOINT ["uv", "run", "main.py"]
