Here’s a **production-ready multi-stage Dockerfile for an Angular application**, optimized for:

✅ Efficient builds
✅ Minimal final image size
✅ Secure and fast static file serving via **Nginx**

---

## 📁 Assumed Angular Project Structure

```
my-angular-app/
├── src/
├── angular.json
├── package.json
├── package-lock.json (or yarn.lock)
├── Dockerfile
└── .dockerignore
```

---

## ✅ Dockerfile for Angular (Multi-Stage with Nginx)

```dockerfile
# ---------- Stage 1: Build Angular App ----------
FROM node:18-alpine AS build

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm install --frozen-lockfile

# Copy source code and build
COPY . .
RUN npm run build --prod

# ---------- Stage 2: Serve with Nginx ----------
FROM nginx:stable-alpine AS production

# Clean default Nginx static content
RUN rm -rf /usr/share/nginx/html/*

# Copy Angular build output
COPY --from=build /app/dist/* /usr/share/nginx/html/

# Optional: custom Nginx config for routing
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port and start server
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## 📄 nginx.conf (Optional — for Angular routing)

```nginx
server {
  listen 80;
  server_name _;

  root /usr/share/nginx/html;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

> 📝 This ensures Angular’s client-side routes (like `/dashboard`) load properly on refresh or direct access.

---

## 📄 .dockerignore (Recommended)

```dockerignore
node_modules
dist
.git
Dockerfile
.dockerignore
*.md
```

---

## 🚀 Build and Run

```bash
docker build -t angular-app .
docker run -d -p 80:80 angular-app
```

Visit: [http://localhost](http://localhost)

---

## ✅ Summary

| Feature               | Benefit                                  |
| --------------------- | ---------------------------------------- |
| Multi-stage build     | Small, clean final image (\~20MB)        |
| Nginx serving         | Fast and production-grade static hosting |
| Route handling config | Fixes Angular client-side routing issues |
| Caching dependencies  | Speeds up rebuilds in CI/CD              |

---
