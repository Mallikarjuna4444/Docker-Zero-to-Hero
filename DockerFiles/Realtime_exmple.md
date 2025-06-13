Great question! In **real-world Azure DevOps pipelines**, professionals follow a structured, layered approach to ensure:

1. **Code is tested** and passes quality gates (tests, code coverage, static analysis).
2. **Only validated code** is built into a Docker image.
3. The **Docker image is then published** (e.g., to Azure Container Registry) and optionally deployed.

---

## ✅ Typical Azure DevOps Pipeline Workflow (for .NET + Docker)

### 🔁 Overview of Common Stages:

1. **Restore & Build**
2. **Run Unit Tests + Code Coverage**
3. **Static Code Analysis (e.g., SonarQube)**
4. **Publish Build Artifacts**
5. **Build & Push Docker Image**
6. **Deploy to Environment (optional)**

---

### 🔧 Example Azure DevOps YAML Pipeline (Simplified)

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'
  dockerImageName: 'myapp'

stages:
- stage: BuildTestAnalyze
  jobs:
  - job: Build
    steps:
    - task: UseDotNet@2
      inputs:
        packageType: 'sdk'
        version: '8.0.x'

    - script: dotnet restore
      displayName: 'Restore dependencies'

    - script: dotnet build --no-restore -c $(buildConfiguration)
      displayName: 'Build'

    - script: dotnet test --no-build --collect:"XPlat Code Coverage"
      displayName: 'Test with Code Coverage'

    - task: SonarQubePrepare@5
      inputs:
        SonarQube: 'MySonarQubeServiceConnection'
        scannerMode: 'MSBuild'
        projectKey: 'my-project-key'
        projectName: 'MyApp'

    - script: dotnet build
      displayName: 'Build for SonarQube'

    - task: SonarQubeAnalyze@5

    - task: SonarQubePublish@5
      inputs:
        pollingTimeoutSec: '300'

- stage: DockerBuildPush
  jobs:
  - job: BuildAndPush
    steps:
    - task: Docker@2
      displayName: 'Build and Push Docker Image'
      inputs:
        containerRegistry: 'MyAzureContainerRegistry'
        repository: 'myapp'
        command: 'buildAndPush'
        Dockerfile: '**/Dockerfile'
        tags: '$(Build.BuildId)'
```

---

## 🧪 Test and Static Analysis Are NOT in Dockerfile

* **Tests and static analysis are run before building the Docker image**.
* Dockerfiles are kept minimal — only to publish and run the app.
* This keeps runtime images clean and avoids including dev/test tooling.

---

## 🏆 Best Practices in Real-World Azure DevOps Pipelines

| Area                | Practice                                                                       |
| ------------------- | ------------------------------------------------------------------------------ |
| **Build**           | `dotnet restore`, `dotnet build`, `dotnet test`, `dotnet publish` are separate |
| **Tests**           | Run with `--collect:"XPlat Code Coverage"`                                     |
| **Coverage**        | Use `coverlet.collector` and publish to Azure DevOps                           |
| **Static Analysis** | Integrate with SonarQube or Roslyn analyzers                                   |
| **Docker**          | Build Docker image **after tests and analysis pass**                           |
| **CI/CD**           | Use YAML pipelines or classic editor with environments and approvals           |
| **Secrets**         | Store in Azure Key Vault or pipeline secrets, never hardcoded                  |

---

Would you like a **ready-to-deploy Azure DevOps pipeline YAML file** for your .NET + Docker + SonarQube app? I can generate a tailored version.
