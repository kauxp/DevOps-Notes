# Introduction to DevSecOps

Short summary:

Integrating security into the DevOps lifecycle.

What you'll learn:

- Shift-left security practices
- SCA, SAST, DAST basics
- Secrets management and policy as code

Resources:

- Add links or notes here

Exercises:

- Run a simple SCA scan in CI

Notes:

- Add instructor notes or references here

## GitHub Actions (brief primer)

GitHub Actions lets you run automated tasks when something happens in your repository.

Examples:

- When code is pushed → build the app
- When a PR is created → run tests

These automated tasks are called Workflows and are written in YAML.

### Why we use GitHub Actions

- Built into GitHub (no separate CI server required)
- Easy to build, test, package and deploy
- Supports Linux, Windows and macOS runners

### Basic terms (must remember)

- Workflow: Full automation (YAML file)
- Job: A set of steps
- Step: One action or command
- Runner: Machine that runs a job
- Action: Reusable automation code

### Project structure (Java example)

java-github-actions-demo/
	├── src/
	│   └── Main.java
	├── pom.xml
	└── .github/
			└── workflows/
					└── build.yml

### Where workflow files live

Always inside `.github/workflows/`.

Example: `.github/workflows/build.yml`

### Basic workflow file (mental template)

```yaml
name: My Workflow
on: [push]
jobs:
	build:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v4
			- run: echo "Hello"
```

### Java + Maven example workflow

```yaml
name: Java Build Workflow

on:
	push:
		branches:
			- main

jobs:
	build:
		runs-on: ubuntu-latest

		steps:
			- name: Checkout code
				uses: actions/checkout@v4

			- name: Set up JDK 17
				uses: actions/setup-java@v4
				with:
					java-version: '17'
					distribution: 'temurin'

			- name: Build with Maven
				run: mvn clean package
```

### What happens when you push code?

1. GitHub detects the push to the configured branch (e.g., main)
2. GitHub creates a VM (runner) for the job
3. The workflow checks out your code
4. The runner installs Java (via the setup action)
5. Maven runs and builds/tests the project

The Actions UI shows a green tick for success or a red cross for failure.

### Where to see output

Go to: GitHub Repo → Actions tab → Click the workflow → View logs for each job/step.

### Using Maven in GitHub Actions

- `actions/setup-java` installs the JDK
- `mvn clean package` compiles, runs tests, and packages your app (JAR/WAR)

### Caching (concept)

Caching Maven dependencies speeds up repeated builds by reusing the downloaded dependencies across runs.

### Important commands (examples)

```bash
git clone https://example.com/your-repo.git
mvn clean package
```

### Common mistakes and quick fixes

- Wrong Java version: use the Java version that matches pom.xml
- Missing `.github/workflows`: ensure the folder name is exact
- YAML indentation error: use spaces (no tabs)
- Maven not found: use `actions/setup-java` in the workflow

### Cost awareness (cloud runners)

- GitHub provides free runner minutes (limits vary by account)
- Long or frequent jobs increase usage costs

Always stop unused workflows and optimize build time.

### My mental model (one-liners)

- Workflow = pipeline
- Job = stage
- Step = command
- Runner = machine
- Action = helper

### One-line summary

GitHub Actions is GitHub's built-in CI/CD system that runs automated workflows when events like push or PR happen.
# Introduction to DevSecOps

Short summary:

Integrating security into the DevOps lifecycle.

What you'll learn:


Resources:


Exercises:


Notes:

# Introduction to DevSecOps

Short summary:

Integrating security into the DevOps lifecycle.

What you'll learn:
# Introduction to DevSecOps

Short summary:

Integrating security into the DevOps lifecycle.

What you'll learn:

- Shift-left security practices
- SCA, SAST, DAST basics
- Secrets management and policy as code

Resources:

- Add links or notes here

Exercises:

- Run a simple SCA scan in CI

Notes:

- Add instructor notes or references here

## GitHub Actions (brief primer)

GitHub Actions lets you run automated tasks when something happens in your repository.

Examples:

- When code is pushed → build the app
- When a PR is created → run tests

These automated tasks are called Workflows and are written in YAML.

### Why we use GitHub Actions

- Built into GitHub (no separate CI server required)
- Easy to build, test, package and deploy
- Supports Linux, Windows and macOS runners

### Basic terms (must remember)

| Term     | Meaning                     |
|----------|-----------------------------|
| Workflow | Full automation (YAML file)  |
| Job      | A set of steps              |
| Step     | One action or command       |
| Runner   | Machine that runs a job     |
| Action   | Reusable automation code    |

### Project structure (Java example)

java-github-actions-demo/
├── src/
│   └── Main.java
├── pom.xml
# Introduction to DevSecOps

Short summary:

Integrating security into the DevOps lifecycle.

What you'll learn:

- Shift-left security practices
- SCA, SAST, DAST basics
- Secrets management and policy as code

Resources:

- Add links or notes here

Exercises:

- Run a simple SCA scan in CI

Notes:

- Add instructor notes or references here

## GitHub Actions (brief primer)

GitHub Actions lets you run automated tasks when something happens in your repository.

Examples:

- When code is pushed → build the app
- When a PR is created → run tests

These automated tasks are called Workflows and are written in YAML.

### Why we use GitHub Actions

- Built into GitHub (no separate CI server required)
- Easy to build, test, package and deploy
- Supports Linux, Windows and macOS runners

### Basic terms (must remember)

| Term     | Meaning                      |
|----------|------------------------------|
| Workflow | Full automation (YAML file)   |
| Job      | A set of steps               |
| Step     | One action or command        |
| Runner   | Machine that runs a job      |
| Action   | Reusable automation code     |

### Project structure (Java example)

java-github-actions-demo/
├── src/
│   └── Main.java
├── pom.xml
└── .github/
		└── workflows/
				└── build.yml

### Where workflow files live

Always inside `.github/workflows/`.

Example: `.github/workflows/build.yml`

### Basic workflow file (mental template)

```yaml
name: My Workflow
on: [push]
jobs:
	build:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v4
			- run: echo "Hello"
```

### Java + Maven example workflow

```yaml
name: Java Build Workflow

on:
	push:
		branches:
			- main

jobs:
	build:
		runs-on: ubuntu-latest

		steps:
			- name: Checkout code
				uses: actions/checkout@v4

			- name: Set up JDK 17
				uses: actions/setup-java@v4
				with:
					java-version: '17'
					distribution: 'temurin'

			- name: Build with Maven
				run: mvn clean package
```

### What happens when you push code?

1. GitHub detects the push to the configured branch (e.g., main)
2. GitHub creates a VM (runner) for the job
3. The workflow checks out your code
4. The runner installs Java (via the setup action)
5. Maven runs and builds/tests the project

The Actions UI shows a green tick for success or a red cross for failure.

### Where to see output

Go to: GitHub Repo → Actions tab → Click the workflow → View logs for each job/step.

### Using Maven in GitHub Actions

- `actions/setup-java` installs the JDK
- `mvn clean package` compiles, runs tests, and packages your app (JAR/WAR)

### Caching (concept)

Caching Maven dependencies speeds up repeated builds by reusing the downloaded dependencies across runs.

### Important commands (examples)

```bash
git clone https://example.com/your-repo.git
mvn clean package
```

### Common mistakes and quick fixes

| Mistake               | Fix / note                                 |
|-----------------------|---------------------------------------------|
| Wrong Java version    | Use the Java version that matches pom.xml   |
| Missing `.github/workflows` | Ensure the folder name is exact         |
| YAML indentation error | Use spaces (no tabs)                       |
| Maven not found       | Use `actions/setup-java` in the workflow    |

### Cost awareness (cloud runners)

- GitHub provides free runner minutes (limits vary by account)
- Long or frequent jobs increase usage costs

Always stop unused workflows and optimize build time.

### My mental model (one-liners)

- Workflow = pipeline
- Job = stage
- Step = command
- Runner = machine
- Action = helper

### One-line summary

GitHub Actions is GitHub's built-in CI/CD system that runs automated workflows when events like push or PR happen.
