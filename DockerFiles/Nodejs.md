Here's a **production-ready Dockerfile** for a **Node.js application**, designed with:

✅ Multi-stage build
✅ Lightweight final image
✅ Dependency caching
✅ Secure and optimized for production use

---

## ✅ Production-Ready Dockerfile for Node.js App

```dockerfile
# ---------- Stage 1: Build ----------
FROM node:18-alpine AS build

# Set working directory
WORKDIR /app

# Copy dependency files first for better caching
COPY package*.json ./

# Install dependencies
RUN npm install --production

# Copy source code
COPY . .

# Optional: build step (for frontend assets, TypeScript, etc.)
# RUN npm run build

# ---------- Stage 2: Runtime ----------
FROM node:18-alpine

# Create non-root user for security
RUN addgroup app && adduser -S -G app app

# Set working directory
WORKDIR /app

# Copy built app and node_modules from the build stage
COPY --from=build /app ./

# Use non-root user
USER app

# Expose port (match your app, e.g., Express listens on 3000)
EXPOSE 3000

# Run the app
CMD ["node", "server.js"]
```

> 🔁 Replace `server.js` with your actual entry point (e.g., `index.js`, `app.js`, or built file if using TypeScript).

---

## 📄 Example Project Structure

```
my-node-app/
├── server.js
├── package.json
├── package-lock.json
├── Dockerfile
└── .dockerignore
```

---

## 📄 Recommended `.dockerignore`

```dockerignore
node_modules
npm-debug.log
Dockerfile
.dockerignore
.env
.git
```

---

## 🚀 Build and Run

```bash
docker build -t my-node-app .
docker run -p 3000:3000 my-node-app
```

Then go to: [http://localhost:3000](http://localhost:3000)

---

## ✅ Features & Best Practices Used

| Feature                | Why It Matters                     |
| ---------------------- | ---------------------------------- |
| Multi-stage build      | Keeps final image lean (\~50MB)    |
| Alpine base image      | Minimal footprint & attack surface |
| Non-root user          | Security best practice             |
| Caching `package.json` | Faster CI/CD builds                |
| `--production` install | Skips dev dependencies             |

---

Would you like a version with:

* **TypeScript** support?
* **PM2** process manager for production clustering?
* Or a `docker-compose.yml` setup with MongoDB or Redis?

Let me know!
