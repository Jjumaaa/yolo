# -------------------- Stage 1: Build the React Application --------------------
# Base Image Choice: Use node:20-alpine for a modern, small build environment.
FROM node:20-alpine as builder
WORKDIR /app

# Copy and install dependencies
COPY package*.json ./
RUN npm install

# Copy source code and **BUILD** the static files (Crucial Step)
COPY . .
RUN npm run build

# -------------------- Stage 2: Serve Static Files with Nginx --------------------
# Base Image Choice: Using nginx:alpine for a very lightweight static web server.
FROM nginx:alpine

# Dockerfile Directives: Copy ONLY the production-ready static files
COPY --from=builder /app/build /usr/share/nginx/html

# Expose the standard HTTP port
EXPOSE 80

# The Nginx default CMD will run the server, no need to specify one.