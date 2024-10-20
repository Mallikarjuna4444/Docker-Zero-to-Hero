### Summary of Docker Hands-On Tutorial

In this session of the Docker series, we covered essential commands and operations to work with Docker effectively.

#### 1. **Understanding Docker Registry**
   - Docker Registry is where Docker images are stored and retrieved, similar to a GitHub repository for code.
   - You can pull images (download) or push images (upload) to the registry.
   - Example: To install Nginx, you simply run the command `docker pull nginx`, which downloads the latest version from the Docker Registry.

#### 2. **Pulling Images**
   - You can specify versions, e.g., `docker pull nginx:1.20` to get a specific version.
   - After pulling images, use `docker images` to see available images on your system.

#### 3. **Creating Containers**
   - To create a container from an image, use `docker run nginx` for the latest version or specify a version.
   - Check running containers with `docker ps`. To view all containers (running and stopped), use `docker ps -a`.

#### 4. **Running Containers in Detached Mode**
   - Use the `-d` flag (detached mode) to run containers in the background.
   - Example: `docker run -d nginx` runs the Nginx container without blocking the terminal.

#### 5. **Port Mapping**
   - To access services running in containers, you need to map container ports to host ports.
   - For instance, `docker run -p 80:80 nginx` maps port 80 of the container to port 80 of the host, allowing you to access Nginx via `http://localhost`.

#### 6. **Managing Multiple Containers**
   - You can run multiple instances of the same image on different ports. For example, running another Nginx container on port 90: `docker run -p 90:80 nginx:1.20`.
   - Containers can be stopped with `docker stop <container_id>` and removed with `docker rm <container_id>`.

#### 7. **Removing Images**
   - To free up space, remove images with `docker rmi <image_name>`. You must first stop and remove any containers using that image.

#### 8. **Renaming and Restarting Containers**
   - Use `docker rename <old_name> <new_name>` to rename a container.
   - Restart a container with `docker start <container_id>`.

#### 9. **Getting Help**
   - Use `docker <command> --help` to see available options and flags for any Docker command.

#### Conclusion
This hands-on session familiarized users with basic Docker commands, from pulling images to managing containers and port mappings. The next steps involve creating custom Docker images for applications, which will be covered in future videos.

### Example Commands
- **Pull an image:** `docker pull nginx`
- **Run a container:** `docker run -d -p 80:80 nginx`
- **List all containers:** `docker ps -a`
- **Remove a container:** `docker rm <container_id>`
- **Remove an image:** `docker rmi nginx:1.20` 

Stay tuned for more advanced topics in Docker!
