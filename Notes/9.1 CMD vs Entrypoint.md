In a Dockerfile, both `CMD` and `ENTRYPOINT` define what gets executed when a container starts — but they serve slightly different purposes and behave differently.

---

### 🔹 `CMD`

* **Purpose:** Provides **default arguments** for the container.
* **Can be overridden** by command-line arguments when you run the container.
* Used when you want the user to be able to easily override the behavior.

**Example:**

```Dockerfile
FROM ubuntu
CMD ["echo", "Hello from CMD"]
```

If you run:

```bash
docker run myimage
```

→ Output: `Hello from CMD`

But if you run:

```bash
docker run myimage echo "Overridden"
```

→ Output: `Overridden`

---

### 🔹 `ENTRYPOINT`

* **Purpose:** Sets the **main executable** for the container.
* **Not overridden** by command-line arguments (unless you use `--entrypoint`).
* Useful when you want your container to always run a specific command.

**Example:**

```Dockerfile
FROM ubuntu
ENTRYPOINT ["echo", "Hello from ENTRYPOINT"]
```

Running:

```bash
docker run myimage
```

→ Output: `Hello from ENTRYPOINT`

Running:

```bash
docker run myimage "Something else"`
```

→ Output: `Hello from ENTRYPOINT Something else`

So command-line arguments are **passed as arguments to the ENTRYPOINT**, not as a replacement.

---

### 🔹 Combining `ENTRYPOINT` and `CMD`

You can use both: `ENTRYPOINT` is the command, `CMD` is its default argument.

```Dockerfile
FROM ubuntu
ENTRYPOINT ["echo"]
CMD ["Hello from combined"]
```

Running:

```bash
docker run myimage
```

→ Output: `Hello from combined`

Running:

```bash
docker run myimage "Override CMD"`
```

→ Output: `Override CMD`

---

### Summary Table

| Feature       | `CMD`                         | `ENTRYPOINT`                                        |
| ------------- | ----------------------------- | --------------------------------------------------- |
| Purpose       | Default command or arguments  | Fixed executable for the container                  |
| Overridable   | Yes (easily via `docker run`) | No (unless using `--entrypoint`)                    |
| Flexibility   | More flexible                 | More rigid                                          |
| Best Use Case | When users might override it  | When the container should always run a specific app |


Great! Here's a practical example of using **both `ENTRYPOINT` and `CMD`** in a Dockerfile.

### 🧪 Use Case:

We want to create a container that **always runs `curl`**, but the user can choose **what URL to fetch**.

---

### 🐳 Dockerfile Example:

```Dockerfile
# Use a lightweight image
FROM alpine

# Install curl
RUN apk add --no-cache curl

# Set ENTRYPOINT to the command we always want to run
ENTRYPOINT ["curl"]

# Set CMD to the default argument for curl (can be overridden)
CMD ["https://example.com"]
```

---

### 💡 Build the Image:

```bash
docker build -t curl-runner .
```

---

### ▶️ Run It (Default CMD in effect):

```bash
docker run curl-runner
```

➡️ This runs: `curl https://example.com`

---

### ✏️ Override CMD at runtime:

```bash
docker run curl-runner https://openai.com
```

➡️ This runs: `curl https://openai.com`

---

### 🧼 Override ENTRYPOINT too (not common, but possible):

```bash
docker run --entrypoint echo curl-runner Hello!
```

➡️ This runs: `echo Hello!`

---

This pattern is commonly used in utility containers like linters, CLI tools, and test runners.

Would you like me to generate a sample repo or give you a `docker-compose.yml` version too?

