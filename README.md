### Home Assignment: Continuous Integration (CI) and GitHub Actions

### Part 1: CI with GitHub Actions
#### 1. Setting up a repo
```bash
   git remote add origin https://github.com/alkon/ci-github-actions-demo.git
```
#### 2. Basic GiHub Action Workflow
   - Created workflow that runs whenever code is pushed to the **main** branch
```yaml
  on:
     push:
       branches:
         - main 
```
   - Checkout the code using `actions/checkout`.
   - Run a shell command to print to the GitHub Actions hello logs
```yaml
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Print hello log
        run: echo "Hello, CI with GitHub Actions"
```
#### 3. Running Tests
   - This project uses simple Python tests — no special dependencies or frameworks are required to run them.
   - `pyptest` chosen to run tests and there it has to be installed, say, using pip
     - Note: there is no need to install pip manually - it was already installed when used `actions/setup-python`
   - So added the step to install `pytest`
```yaml
    - name: Install pytest
      run: pip install pytest
```
---
### Part 2: Advanced GitHub Actions Features

#### 4. Cron Scheduling
   - The workflow is triggered daily at midnight UTC
```yaml
on:
  push: # for test only
  schedule:
    - cron: '0 0 * * *'
```
- Added the steps to checkout the existed code and to print to the GitHub Action logs
```yaml
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
    
      - name: Print log
        run: echo "Scheduled build completed successfully!"
```

#### 5. Matrix build
- It tests across multiple **Python versions** and **operating systems**.
- The Python versions used are **3.12** and **3.13**.
- The operating systems are **Ubuntu** and **Windows**.
- This results in **4 parallel jobs** in total:

  | OS             | Python Version |
  |----------------|----------------|
  | ubuntu-latest  | 3.12           |
  | ubuntu-latest  | 3.13           |
  | windows-latest | 3.12           |
  | windows-latest | 3.13           |

  - Key part of the workflow YAML that defines the build matrix:

     ```yaml
     strategy:
       matrix:
         python: [3.12, 3.13]
         os: [ubuntu-latest, windows-latest]
     ```
    - Note: Setting `fail-fast: false` ensure that **all** matrix jobs run to completion, even if one job fails. 
    - This allows to see test results across all combinations of Python versions and OS
    ``` yaml
        strategy:
          fail-fast: false
          matrix:
          [...]
    ``` 
    
### Self-Hosted GitHub Actions Runner Setup

#### a. Download and Install the Runner

1. **Create a folder** to store the runner files:
  ```bash
    mkdir actions-runner && cd actions-runner
  ```

2. **Download the latest runner package** for macOS:
  ```bash
      curl -o actions-runner-osx-x64-2.323.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.323.0/actions-runner-osx-x64-2.323.0.tar.gz
  ```
3. **Extract the installer**:
  ```bash
    tar xzf ./actions-runner-osx-x64-2.323.0.tar.gz
  ```

#### b. Configure the Runner

  **Start the configuration process** by running the following command:
  ```bash
     ./config.sh --url https://github.com/alkon/ci-github-actions-demo --token YOUR_TOKEN
  ```

  - Note: Replace `YOUR_TOKEN` with the authentication token provided by GitHub when setting up the self-hosted runner in your repository settings.

#### c. Run Self-Hosted Runner
  **Start the runner**:
  ```bash
     ./run.sh
  ```

 - Note: This command will keep the runner running and listening for jobs.

#### d. Using Self-Hosted Runner in GitHub Workflows

- Update GitHub Actions YAML file for the required **job** with the following configuration:

```yaml
jobs:
  job-name:
    runs-on: self-hosted
```
