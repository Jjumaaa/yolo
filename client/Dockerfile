# -------------------- Stage 1: Build the React Application --------------------
# Base Image Choice: Use node:20-alpine for a modern, small build environment.
FROM node:20-alpine as builder
WORKDIR /app

# Copy and install dependencies first for layer caching
COPY package*.json ./
RUN npm install

# Copy source code
COPY . .

# CRITICAL FIX: Add NODE_OPTIONS to allow legacy OpenSSL providers for older React Scripts
# RUN npm run build will now succeed
RUN NODE_OPTIONS=--openssl-legacy-provider npm run build

# -------------------- Stage 2: Serve Static Files with Nginx --------------------
# Base Image Choice: Using nginx:alpine for a very lightweight static web server (minimal final image size).
FROM nginx:alpine

# Dockerfile Directives: Copy ONLY the production-ready static files from the builder stage
# The 'build' folder contains the optimized static HTML, CSS, and JS.
COPY --from=builder /app/build /usr/share/nginx/html

# Expose the standard HTTP port (This corresponds to port 80 in docker-compose.yaml)
EXPOSE 80

# The Nginx default CMD will run the server, no need to specify one.