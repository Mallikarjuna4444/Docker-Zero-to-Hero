## https://spacelift.io/blog/docker-run-environment-variables

To pass Docker environment values to your source code, you typically need to set environment variables in the Docker container and then access them in your application code. Here are the steps to achieve this:

### 1. Set Environment Variables in the Dockerfile
You can specify environment variables directly in the Dockerfile using the `ENV` directive. For example:

```dockerfile
# Dockerfile
FROM node:14

# Set environment variables
ENV MY_ENV_VAR="value"
ENV DB_HOST="localhost"
ENV DB_PORT="5432"

# Other Dockerfile commands (e.g., copying app files, installing dependencies)
COPY . /app
WORKDIR /app
RUN npm install

# Start the application
CMD ["node", "app.js"]
```

### 2. Set Environment Variables Using `docker run`
Alternatively, you can pass environment variables when running the container using the `-e` flag with `docker run`. For example:

```bash
docker run -e MY_ENV_VAR="value" -e DB_HOST="localhost" -e DB_PORT="5432" my-image
```

### 3. Pass Environment Variables with a `.env` File
If you have many environment variables, you can use a `.env` file and pass it to Docker using `--env-file`:

#### Example `.env` file:
```
MY_ENV_VAR=value
DB_HOST=localhost
DB_PORT=5432
```

#### Running the Docker container:
```bash
docker run --env-file .env my-image
```

### 4. Access Environment Variables in Your Application Code
In your application, you can access these environment variables using the standard method for your programming language.

- **Node.js**: Use `process.env` to access the environment variables:

```javascript
// app.js
const myEnvVar = process.env.MY_ENV_VAR;
const dbHost = process.env.DB_HOST;
const dbPort = process.env.DB_PORT;

console.log(`DB Host: ${dbHost}, DB Port: ${dbPort}`);
```

- **Python**: Use `os.environ`:

```python
import os

my_env_var = os.getenv('MY_ENV_VAR')
db_host = os.getenv('DB_HOST')
db_port = os.getenv('DB_PORT')

print(f"DB Host: {db_host}, DB Port: {db_port}")
```

- **Java**: Use `System.getenv()`:

```java
String myEnvVar = System.getenv("MY_ENV_VAR");
String dbHost = System.getenv("DB_HOST");
String dbPort = System.getenv("DB_PORT");

System.out.println("DB Host: " + dbHost + ", DB Port: " + dbPort);
```

### 5. Access Environment Variables in Docker Compose (Optional)
If you're using Docker Compose, you can define environment variables in the `docker-compose.yml` file:

```yaml
version: '3'
services:
  app:
    image: my-image
    environment:
      - MY_ENV_VAR=value
      - DB_HOST=localhost
      - DB_PORT=5432
    # Or use an env_file to specify a file with environment variables
    env_file:
      - .env
```

Then, you can access them in your application as shown above.

---

This is how you pass Docker environment variables to your source code, ensuring that they can be accessed securely and efficiently at runtime.
