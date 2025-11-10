# -------------------- Stage 1: Builder Stage --------------------
# Choice of Base Image: Use node:20-alpine for modern, small build environment.
FROM node:20-alpine as builder
WORKDIR /app

# Copy and install dependencies first for layer caching
COPY package*.json ./

# --- CRITICAL FIX: FORCE CACHE BUST ---
# Incrementing the ARG value forces Docker to see a change in this layer's content, 
# thereby guaranteeing npm install runs.
ARG FORCE_REBUILD=20251110_3 
RUN npm install
# --------------------------------------

# Copy source code
COPY . .

# -------------------- Stage 2: Production Stage --------------------
# Base Image Choice: Use node:20-alpine for the final, minimal runtime.
FROM node:20-alpine
WORKDIR /app

# Dockerfile Directives: Copy ONLY the final files from the builder stage
COPY --from=builder /app /app

# Expose the port the Node server listens on
EXPOSE 5000

# Define the command to run your app
CMD ["node", "server.js"]