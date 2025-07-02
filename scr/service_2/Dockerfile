## ------------------------------- Builder Stage ------------------------------ ##
FROM python:3.13-slim AS builder

# The installer requires curl (and certificates) to download the release archive
RUN apt-get update && apt-get install -y --no-install-recommends curl ca-certificates

#RUN apt-get update && apt-get install --no-install-recommends curl ca-certificates -y \
     #   build-essential && \
    #apt-get clean && rm -rf /var/lib/apt/lists/*

# Download the latest installer, install it and then remove it
ADD https://astral.sh/uv/install.sh /install.sh
RUN chmod -R 655 /install.sh && /install.sh

# Set up the UV environment path correctly
ENV PATH="/root/.local/bin:/home/ubuntu/.local/bin"

WORKDIR /app

COPY ./pyproject.toml .

RUN uv venv && uv sync

## ------------------------------- Production Stage ------------------------------ ##
FROM python:3.13-slim AS production

WORKDIR /app

COPY . .
COPY --from=builder /app/.venv .venv

# Set up environment variables for production
ENV PATH="/app/.venv/bin:$PATH"

# Expose the specified port for container
EXPOSE 8002

# Start the application with Uv in production mode, using environment variable references
CMD ["uv", "run" , "app:app"]
