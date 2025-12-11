# --- Builder: install production deps only on a lightweight image ---
FROM node:18-alpine AS builder

WORKDIR /app

# copy only manifests first for caching
COPY package.json package-lock.json* ./

# deterministic, minimal install (production only)
RUN npm ci --only=production --prefer-offline --no-audit --no-fund

# copy source
COPY . .

# set ownership so files are readable by non-root runtimes if needed
RUN chown -R node:node /app

# --- Runtime: distroless node (very small, minimal attack surface) ---
FROM gcr.io/distroless/nodejs:18

WORKDIR /app

# copy only what we need from builder
COPY --from=builder /app /app

# runtime port (documentation & orchestration)
EXPOSE 3000

# distroless images expect the node binary as entrypoint; supply script as CMD
CMD ["patient-service.js"]