Got it! I'll keep the **same detailed explanations** from before but replace the **Azure CLI commands** with **Azure DevOps YAML pipeline snippets** for each example.

---

# 1. .NET Core Web App Deployment with ADO YAML

### Dockerfile (same as before)

```dockerfile
# Build Stage
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish -c Release -o /app/publish

# Runtime Stage
FROM mcr.microsoft.com/dotnet/aspnet:7.0
WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "MyDotnetApp.dll"]
```

---

### Azure DevOps pipeline (build, push, deploy)

```yaml
trigger:
- main

variables:
  imageName: mydotnetapp
  registry: myregistry.azurecr.io
  azureWebAppName: my-dotnet-webapp
  azureResourceGroup: myRG
  azureServiceConnection: 'AzureServiceConnection'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: DotNetCoreCLI@2
      inputs:
        command: 'publish'
        projects: '**/*.csproj'
        arguments: '--configuration Release --output $(Build.ArtifactStagingDirectory)/publish'

    - task: Docker@2
      inputs:
        containerRegistry: '$(registry)'
        repository: '$(imageName)'
        command: 'buildAndPush'
        Dockerfile: '**/Dockerfile'
        tags: |
          $(Build.BuildId)

- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: DeployWebApp
    environment: 'production'
    pool:
      vmImage: 'ubuntu-latest'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureWebAppContainer@1
            inputs:
              azureSubscription: '$(azureServiceConnection)'
              appName: '$(azureWebAppName)'
              resourceGroupName: '$(azureResourceGroup)'
              imageName: '$(registry)/$(imageName):$(Build.BuildId)'
```

---

# 2. Java Spring Boot Web App Deployment with ADO YAML

### Dockerfile (same as before)

```dockerfile
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY target/my-java-app.jar app.jar
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

---

### Azure DevOps pipeline

```yaml
trigger:
- main

variables:
  imageName: myjavaapp
  registry: myregistry.azurecr.io
  azureWebAppName: my-java-webapp
  azureResourceGroup: myRG
  azureServiceConnection: 'AzureServiceConnection'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: Maven@3
      inputs:
        mavenPomFile: 'pom.xml'
        goals: 'clean package'
        publishJUnitResults: true
        testResultsFiles: '**/surefire-reports/TEST-*.xml'

    - task: Docker@2
      inputs:
        containerRegistry: '$(registry)'
        repository: '$(imageName)'
        command: 'buildAndPush'
        Dockerfile: '**/Dockerfile'
        tags: |
          $(Build.BuildId)

- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: DeployWebApp
    environment: 'production'
    pool:
      vmImage: 'ubuntu-latest'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureWebAppContainer@1
            inputs:
              azureSubscription: '$(azureServiceConnection)'
              appName: '$(azureWebAppName)'
              resourceGroupName: '$(azureResourceGroup)'
              imageName: '$(registry)/$(imageName):$(Build.BuildId)'
```

---

# 3. Python Flask Web App Deployment with ADO YAML

### Dockerfile (same as before)

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["gunicorn", "-b", "0.0.0.0:80", "app:app"]
```

---

### Azure DevOps pipeline

```yaml
trigger:
- main

variables:
  imageName: mypyapp
  registry: myregistry.azurecr.io
  azureWebAppName: my-python-webapp
  azureResourceGroup: myRG
  azureServiceConnection: 'AzureServiceConnection'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - script: |
        python3 -m venv venv
        source venv/bin/activate
        pip install -r requirements.txt
      displayName: 'Install dependencies'

    - task: Docker@2
      inputs:
        containerRegistry: '$(registry)'
        repository: '$(imageName)'
        command: 'buildAndPush'
        Dockerfile: '**/Dockerfile'
        tags: |
          $(Build.BuildId)

- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: DeployWebApp
    environment: 'production'
    pool:
      vmImage: 'ubuntu-latest'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureWebAppContainer@1
            inputs:
              azureSubscription: '$(azureServiceConnection)'
              appName: '$(azureWebAppName)'
              resourceGroupName: '$(azureResourceGroup)'
              imageName: '$(registry)/$(imageName):$(Build.BuildId)'
```

---

# 4. Angular SPA Deployment with ADO YAML

### Dockerfile (same as before)

```dockerfile
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build --prod

FROM nginx:alpine
COPY --from=build /app/dist/my-angular-app /usr/share/nginx/html
```

---

### Azure DevOps pipeline

```yaml
trigger:
- main

variables:
  imageName: my-angular-app
  registry: myregistry.azurecr.io
  azureWebAppName: my-angular-webapp
  azureResourceGroup: myRG
  azureServiceConnection: 'AzureServiceConnection'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: '18.x'

    - script: |
        npm install
        npm run build --prod
      displayName: 'Build Angular App'

    - task: Docker@2
      inputs:
        containerRegistry: '$(registry)'
        repository: '$(imageName)'
        command: 'buildAndPush'
        Dockerfile: '**/Dockerfile'
        tags: |
          $(Build.BuildId)

- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: DeployWebApp
    environment: 'production'
    pool:
      vmImage: 'ubuntu-latest'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureWebAppContainer@1
            inputs:
              azureSubscription: '$(azureServiceConnection)'
              appName: '$(azureWebAppName)'
              resourceGroupName: '$(azureResourceGroup)'
              imageName: '$(registry)/$(imageName):$(Build.BuildId)'
```

---

# 5. React SPA Deployment with ADO YAML

### Dockerfile (same as before)

```dockerfile
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
```

---

### Azure DevOps pipeline

```yaml
trigger:
- main

variables:
  imageName: my-react-app
  registry: myregistry.azurecr.io
  azureWebAppName: my-react-webapp
  azureResourceGroup: myRG
  azureServiceConnection: 'AzureServiceConnection'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: '18.x'

    - script: |
        npm install
        npm run build
      displayName: 'Build React App'

    - task: Docker@2
      inputs:
        containerRegistry: '$(registry)'
        repository: '$(imageName)'
        command: 'buildAndPush'
        Dockerfile: '**/Dockerfile'
        tags: |
          $(Build.BuildId)

- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: DeployWebApp
    environment: 'production'
    pool:
      vmImage: 'ubuntu-latest'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureWebAppContainer@1
            inputs:
              azureSubscription: '$(azureServiceConnection)'
              appName: '$(azureWebAppName)'
              resourceGroupName: '$(azureResourceGroup)'
              imageName: '$(registry)/$(imageName):$(Build.BuildId)'
```

---

# 6. Node.js Web App Deployment with ADO YAML

### Dockerfile (same as before)

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 80
CMD ["node", "server.js"]
```

---

### Azure DevOps pipeline

```yaml
trigger:
- main

variables:
  imageName: my-node-app
  registry: myregistry.azurecr.io
  azureWebAppName: my-node-webapp
  azureResourceGroup: myRG
  azureServiceConnection: 'AzureServiceConnection'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: '18.x'

    - script: |
        npm install
      displayName: 'npm install'

```


```
- task: Docker@2
  inputs:
    containerRegistry: '$(registry)'
    repository: '$(imageName)'
    command: 'buildAndPush'
    Dockerfile: '**/Dockerfile'
    tags: |
      $(Build.BuildId)
```

* stage: Deploy
  dependsOn: Build
  jobs:

  * deployment: DeployWebApp
    environment: 'production'
    pool:
    vmImage: 'ubuntu-latest'
    strategy:
    runOnce:
    deploy:
    steps:
    \- task: AzureWebAppContainer\@1
    inputs:
    azureSubscription: '\$(azureServiceConnection)'
    appName: '\$(azureWebAppName)'
    resourceGroupName: '\$(azureResourceGroup)'
    imageName: '\$(registry)/\$(imageName):\$(Build.BuildId)'

```

---

### Summary of what changed

- Instead of using `az webapp config container set` or CLI commands, you use **AzureWebAppContainer@1** task to deploy the container.
- Instead of manually pushing with Docker CLI, you use `Docker@2` task to build & push images.
- Variables hold your Azure subscription, resource group, registry, app name, image name for easy re-use.
- Build steps use respective language build tools (dotnet publish, maven package, npm build, etc).

---

Certainly! Here's a **detailed example** of deploying an **Azure Function App using a container** with an **Azure DevOps YAML pipeline**. I'll include the Dockerfile, pipeline, and highlight mandatory conditions you must meet.

---

## Example: Deploy .NET Azure Function App as a Container via Azure DevOps YAML

---

### 1. Dockerfile for .NET Azure Function (isolated process model)

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src

# Copy csproj and restore as distinct layers
COPY *.csproj .
RUN dotnet restore

# Copy remaining files and publish
COPY . .
RUN dotnet publish -c Release -o /app/publish

# Runtime image
FROM mcr.microsoft.com/azure-functions/dotnet-isolated:4-dotnet7
WORKDIR /home/site/wwwroot
COPY --from=build /app/publish .

# Expose port (default for Azure Functions)
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

```

---

### 2. Azure DevOps YAML Pipeline to Build, Push & Deploy

```yaml
trigger:
- main

variables:
  imageName: myfuncapp
  registry: myregistry.azurecr.io
  azureFuncAppName: my-function-app
  azureResourceGroup: myResourceGroup
  azureServiceConnection: 'AzureServiceConnection'  # Replace with your Azure DevOps service connection

stages:
- stage: Build
  displayName: Build and Push container image
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: DotNetCoreCLI@2
      inputs:
        command: 'publish'
        projects: '**/*.csproj'
        arguments: '--configuration Release --output $(Build.ArtifactStagingDirectory)/publish'

    - task: Docker@2
      inputs:
        containerRegistry: '$(registry)'
        repository: '$(imageName)'
        command: 'buildAndPush'
        Dockerfile: '**/Dockerfile'
        tags: |
          $(Build.BuildId)

- stage: Deploy
  displayName: Deploy to Azure Function App
  dependsOn: Build
  jobs:
  - deployment: DeployFunction
    environment: 'production'
    pool:
      vmImage: 'ubuntu-latest'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureFunctionAppContainer@1
            inputs:
              azureSubscription: '$(azureServiceConnection)'
              appName: '$(azureFuncAppName)'
              resourceGroupName: '$(azureResourceGroup)'
              imageName: '$(registry)/$(imageName):$(Build.BuildId)'
```

---

### 3. Mandatory Conditions and Notes

* **Azure Function App must be created with Linux OS and Docker Container** as publishing option.
  CLI example to create it beforehand (outside pipeline):

  ```bash
  az functionapp create \
    --resource-group myResourceGroup \
    --name my-function-app \
    --storage-account <storage-account> \
    --plan <app-service-plan> \
    --runtime dotnet-isolated \
    --functions-version 4 \
    --deployment-container-image-name myregistry.azurecr.io/myfuncapp:tag \
    --os-type Linux
  ```

* **Set Application Settings:**

  Your Function app requires some settings for container-based deployments, for example:

  * `WEBSITES_ENABLE_APP_SERVICE_STORAGE` = `false` (optional but recommended for containers)
  * `FUNCTIONS_WORKER_RUNTIME` = `dotnet-isolated` (matches your function runtime)
  * If using secrets or keys, configure those as environment variables in the Function App Configuration.

* **Azure DevOps service connection must have rights** to push container images to your registry and deploy to the Azure Function.

* **Ports & Startup:** The Azure Functions runtime inside the container listens on port 80 by default; no need to expose custom ports.

* **Dockerfile Base Image:** Use the official Azure Functions base images like `mcr.microsoft.com/azure-functions/dotnet-isolated:4-dotnet7` for best compatibility.

---

### Summary

* You build/publish your function app code inside the pipeline.
* Docker task builds the container image, tags with build ID, and pushes to your container registry.
* Deployment step uses `AzureFunctionAppContainer@1` task to point your Function App to the new image.
* The Function App is pre-created and configured to accept container deployments.

---

### Why `/home/site/wwwroot`?

* **Azure Functions runtime expects your function app code inside `/home/site/wwwroot` directory inside the container.**

* This is the **default working directory** where Azure Functions host looks for the function app files (function.json, binaries, etc).

---

### How does it relate to the Dockerfile?

In the example Dockerfile:

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet-isolated:4-dotnet7
WORKDIR /home/site/wwwroot
COPY --from=build /app/publish .
```

* The `WORKDIR` sets the container's working directory to `/home/site/wwwroot`.
* The `COPY` command copies the published build output from the build stage into `/home/site/wwwroot` inside the container.

---

### How does Azure Function App link to this?

* When Azure runs your container, the **Functions host automatically serves from `/home/site/wwwroot`**.
* Your container image must have the function app files in that directory to run properly.
* If your app files are somewhere else in the container, the function host won’t find them, and the app will fail.

---

### Summary of `/home/site/wwwroot` role:

| Purpose              | Explanation                                                                                  |
| -------------------- | -------------------------------------------------------------------------------------------- |
| `/home/site/wwwroot` | **Default app root folder inside container** where Azure Functions runtime expects your code |
| Dockerfile `WORKDIR` | Sets current directory inside container to `/home/site/wwwroot`                              |
| Dockerfile `COPY`    | Copies your build output to `/home/site/wwwroot` so Functions runtime can load it            |

---

### So to **successfully deploy a containerized Azure Function:**

* Your container image **must have the function app files at `/home/site/wwwroot`**.
* Your Dockerfile should copy your build output there.
* This directory is baked into the official Azure Functions base images and expected by the runtime.

---
