FROM python:3.12-alpine
COPY --from=ghcr.io/astral-sh/uv:0.7.12 /uv /uvx /bin/

# Install build tools & runtime dependencies
RUN apk add --no-cache \
    ffmpeg \
    file \
    libmagic

RUN mkdir -p /app/data
WORKDIR /app

# switch to a non-root user
RUN adduser -D -u 1000 app && \
    chown -R app:app /app
USER app

# Enable bytecode compilation
ENV UV_COMPILE_BYTECODE=1

# Copy from the cache instead of linking since it's a mounted volume
ENV UV_LINK_MODE=copy

# Define app data volume
VOLUME /app/data

# Then, add the rest of the project source code and install it
COPY --chown=app:app . /app
RUN uv sync --locked --no-group dev

# Place executables in the environment at the front of the path
ENV PATH="/app/.venv/bin:$PATH"

# Set entrypoint to run the app using uv
ENTRYPOINT ["python", "main.py"]
