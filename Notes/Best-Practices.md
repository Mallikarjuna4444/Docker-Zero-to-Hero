Sure! Here’s a summarized version of your video script, focusing on the key points about building a custom Docker image for a React.js application:

---

**Welcome Back to the Docker Series!**

If you haven’t seen the previous video, I highly recommend watching it first, as it covers essential instructions for building custom images. Today, we’ll explore best practices for creating a production-ready Docker image for a React.js application using multi-stage builds, Docker caching, and a `.dockerignore` file.

### Project Overview
We'll start with a simple React.js To-Do application that communicates with Spring Boot APIs and MongoDB, which we set up previously.

### Running the Application
Traditionally, we run the application using:
```bash
npm install
npm start
```
This starts the app on localhost:3000. However, we want to make this production-ready.

### Creating the Dockerfile
We'll create a `Dockerfile.dev` for development purposes:
```dockerfile
FROM node:alpine
WORKDIR /app
COPY . .
RUN npm install
ENTRYPOINT ["npm", "start"]
```
To build the image, use:
```bash
docker build -t todo-ui:1.0.0 -f Dockerfile.dev .
```

### Running the Docker Image
Run the image with:
```bash
docker run -p 3001:3000 todo-ui:1.0.0
```
Access it at localhost:3001. But remember, this is not optimized for production.

### Building for Production
To create a production build:
1. Change the Dockerfile to install and build:
```dockerfile
RUN npm run build
```
2. Use a server to serve the build:
```dockerfile
RUN npm install -g serve
ENTRYPOINT ["serve", "-s", "build"]
```
3. Update the run command accordingly:
```bash
docker run -p 3002:5000 todo-ui:1.0.0
```

### Multi-Stage Builds
To reduce image size, we’ll implement multi-stage builds. Create `Dockerfile.prod`:
```dockerfile
# Build stage
FROM node:alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
```
Build the production image:
```bash
docker build -t todo-ui:1.0.2 -f Dockerfile.prod .
```

### Optimizing Build Times with Docker Caching
To leverage caching, copy `package.json` separately:
```dockerfile
COPY package*.json ./
RUN npm install
COPY . .
```
This ensures `npm install` runs only when dependencies change.

### Using `.dockerignore`
Create a `.dockerignore` file to exclude unnecessary files like `node_modules` and `build`:
```
node_modules
build
```

### Final Steps
Run the optimized image:
```bash
docker run -p 3004:80 todo-ui:1.0.2
```
Access it at localhost:3004.

### Conclusion
We’ve built a lean, production-ready Docker image using multi-stage builds, optimized build times with caching, and used a `.dockerignore` file.

Try applying these concepts to the Spring Boot application from the previous video!

Thanks for watching! If you found this helpful, please share and subscribe for more updates!

--- 

Let me know if you need any adjustments or additional details!
