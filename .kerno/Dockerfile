# Build stage
FROM node:18-slim AS build

WORKDIR /app

# Copy package files for dependency installation
COPY package*.json ./

# Install all dependencies (including devDependencies for build)
RUN npm ci

# Copy source code
COPY . .

# Build the application
RUN npm run build

# Production stage
FROM node:18-slim

# Install curl for healthcheck probes
RUN apt-get update && apt-get install -y --no-install-recommends curl && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install only production dependencies
RUN npm ci --only=production

# Copy built application from build stage
COPY --from=build /app/dist ./dist

# Create non-root user
RUN useradd -r -u 1001 -g root appuser && \
    chown -R appuser:root /app

USER appuser

# Expose default NestJS port
EXPOSE 3000

# Start the application in production mode
CMD ["npm", "run", "start:prod"]
