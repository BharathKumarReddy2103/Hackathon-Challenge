# --- Builder stage: install production deps on a small image ---
FROM node:18-alpine AS builder

WORKDIR /app

# copy dependency manifests first for layer caching
COPY package.json package-lock.json* ./

# deterministic install when lockfile exists; fallback to npm install otherwise
RUN if [ -f package-lock.json ]; then \
      npm ci --only=production --prefer-offline --no-audit --no-fund; \
    else \
      npm install --production --no-audit --no-fund; \
    fi

# copy app sources
COPY . .

# ensure correct ownership for non-root runtimes (optional)
RUN chown -R node:node /app

# --- Runtime stage: distroless Node (minimal & secure) ---
FROM gcr.io/distroless/nodejs:18

WORKDIR /app

# copy only the built app + production deps from builder
COPY --from=builder /app /app

# document listening port
EXPOSE 3001

# distroless images use node as entrypoint, pass the script as CMD
CMD ["appointment-service.js"]