Here’s a GitHub Actions version of your requested cheatsheet/tutorial:

---

### Learn GitHub Actions in Y Minutes

So, you’re building software and want to automate repetitive tasks—testing, deployment, or anything in between. Maybe you’re tired of manually running your scripts or just want to ensure consistency in your workflow.

Meet your automation assistant: **GitHub Actions**!

---

### Introduction

**GitHub Actions 101**  
GitHub Actions is a CI/CD (Continuous Integration and Continuous Deployment) tool built into GitHub. It lets you automate your workflows directly from your repositories.

**When to Use It**

- **CI/CD**: Automate testing and deployment for your code.
- **Code Quality**: Run linters, style checks, or security scans.
- **Infrastructure as Code (IaC)**: Deploy cloud infrastructure using tools like Terraform or Pulumi.
- **Custom Automation**: Automate anything from generating documentation to handling issues and PRs.

**When *Not* to Use It**

- **Massive Workloads**: If your tasks are resource-intensive and exceed GitHub's runner limits, consider using self-hosted runners or external CI systems.
- **Non-GitHub Repos**: GitHub Actions is tightly coupled with GitHub; it’s not the best fit for repos hosted elsewhere.

Use GitHub Actions wisely and monitor usage limits—there are quotas for free accounts and repositories.

---

### Setting Up GitHub Actions

To start using GitHub Actions:
1. Add a YAML file under `.github/workflows/` in your repository.
2. Define your workflow steps, triggers, and jobs.

---

### The Basics

Here are some essential concepts to get started:

#### **Workflows**
A workflow is defined by a YAML file and consists of jobs.

- **Basic Workflow**: Runs tests on every push to the main branch.
  ```yaml
  name: CI
  on:
    push:
      branches:
        - main
  jobs:
    build:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Set up Node.js
          uses: actions/setup-node@v3
          with:
            node-version: 16
        - name: Install dependencies
          run: npm install
        - name: Run tests
          run: npm test
  ```

#### **Triggers**
Decide when your workflow runs:
- `push`: Run on every push.
- `pull_request`: Run on PRs.
- `schedule`: Run at specific times (CRON syntax).
  ```yaml
  on:
    schedule:
      - cron: "0 0 * * 0" # Every Sunday at midnight
  ```

#### **Runners**
The environment where your jobs run:
- GitHub-hosted runners: Pre-configured with common OSes (e.g., `ubuntu-latest`).
- Self-hosted runners: Custom environments for your workflows.

---

### Moving Up: Advanced Workflows

Get more out of your workflows with these techniques:

#### **Reusable Workflows**
Avoid duplication by reusing workflows across repositories.
- Define a reusable workflow:
  ```yaml
  name: Reusable Workflow
  on:
    workflow_call:
      inputs:
        environment:
          required: true
  jobs:
    deploy:
      runs-on: ubuntu-latest
      steps:
        - name: Deploy to ${{ inputs.environment }}
          run: ./deploy.sh ${{ inputs.environment }}
  ```
- Use it in another workflow:
  ```yaml
  jobs:
    call-workflow:
      uses: org/repo/.github/workflows/deploy.yml@main
      with:
        environment: production
  ```

#### **Matrix Builds**
Run a job with multiple configurations.
- Example: Test across Node.js versions.
  ```yaml
  jobs:
    test:
      runs-on: ubuntu-latest
      strategy:
        matrix:
          node: [14, 16, 18]
      steps:
        - uses: actions/checkout@v3
        - name: Set up Node.js ${{ matrix.node }}
          uses: actions/setup-node@v3
          with:
            node-version: ${{ matrix.node }}
        - name: Run tests
          run: npm test
  ```

#### **Secrets**
Store sensitive information (e.g., API keys).
- Add secrets in your repository settings.
- Access them in workflows:
  ```yaml
  - name: Use secret
    run: echo "${{ secrets.MY_SECRET }}"
  ```

#### **Artifacts**
Save and share files between jobs or workflows.
- Save:
  ```yaml
  - name: Upload artifact
    uses: actions/upload-artifact@v3
    with:
      name: build-output
      path: dist/
  ```
- Download:
  ```yaml
  - name: Download artifact
    uses: actions/download-artifact@v3
    with:
      name: build-output
  ```

---

### Advanced Techniques

#### **Custom Actions**
Create custom actions for reusable functionality.
- Example `action.yml`:
  ```yaml
  name: My Custom Action
  description: Prints a message
  inputs:
    message:
      required: true
      description: The message to print
  runs:
    using: "node12"
    main: "index.js"
  ```

#### **Optimizing Performance**
- Use caching to speed up workflows:
  ```yaml
  - name: Cache dependencies
    uses: actions/cache@v3
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-node-
  ```

- Split workflows to avoid hitting time limits or exceeding concurrent jobs.

---

### Practical Tips and Tricks

- **Start Small**: Begin with simple workflows, then iterate.
- **Use Pre-Built Actions**: Check out the [GitHub Actions Marketplace](https://github.com/marketplace/actions) for reusable actions.
- **Lint Your YAML**: Tools like `actionlint` help you catch syntax errors.
- **Monitor Usage**: Keep an eye on usage limits in the repository settings.

---

Happy automating!