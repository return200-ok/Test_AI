# 🚀 GitLab CI/CD Pipeline for Node.js + Docker Web Application

This repository contains a robust and production-ready GitLab CI/CD pipeline that automates building, testing, analyzing, and deploying a Node.js application packaged as a Docker image. The pipeline supports dynamic environments, parallel job execution, SonarQube integration, and Slack notifications.

---

## 🧱 Pipeline Stages Overview

### 1. **Build**
- **`docker_build`**: Builds and pushes a Docker image for the app.
- **`unit_tests`**: Runs unit tests using `npm test`.
- **`sonar_scan`**: Performs static code analysis using SonarQube and enforces a quality gate.

> ⏱️ These jobs run in parallel to minimize build time.

---

### 2. **Test/Staging**
- **`deploy_staging`**: Deploys to a dynamically selected environment (`dev` or `staging`) based on branch (`development`, `staging`).
- **`smoke_tests`**: Executes smoke tests to ensure the deployed app is up and functioning (e.g., `/healthz`, `/api/version`).

---

### 3. **Production**
- **`approve_production`**: Manual approval gate for production deploys (only on semantic version tags).
- **`deploy_production`**: Deploys the Docker image to the production environment.
- **`health_checks`**: Verifies application health post-deployment.

---

## ⚙️ Dynamic Environment Handling

This pipeline dynamically sets the target environment (`TARGET_ENV`) using reusable rules (`rule_default`):

| Condition                        | Environment |
|----------------------------------|-------------|
| Branch `development`             | dev         |
| Branch `staging`                 | staging     |
| Tag matching `x.y.z` (semver)    | production  |

The dynamic environment is used for:
- Naming environments (`dev/branch-name`)
- Setting URLs (e.g., `https://branch-name.dev.example.com`)
- Conditional job execution

---

## 🔒 Quality and Security

- ✅ **SonarQube Quality Gate**: Blocks pipeline if code quality fails.
- 🛡️ **Retries & Resilience**: Jobs like unit tests and smoke tests auto-retry on transient failures.
- 🕵️‍♂️ **Isolated Environments**: All jobs use Docker-based runners for consistent behavior.

---

## 🔔 Slack Notifications

- 📣 Failed jobs send alerts to Slack via webhook.
- 🟢 Successful or failed **production deployments** also send detailed Slack messages with links to logs or environment.

> Add your `SLACK_WEBHOOK_URL` in GitLab CI/CD → Variables.

---

## 🗂️ GitLab CI/CD Configuration Files

| File          | Purpose                            |
|---------------|------------------------------------|
| `.gitlab-ci.yml` | Main pipeline configuration        |
| `Dockerfile`      | Builds the Node.js app container   |
| `README.md`       | CI/CD overview and documentation   |

---

## 📌 Prerequisites

- Node.js app with `package.json` and test scripts
- Dockerfile in the root of the project
- GitLab project with:
  - CI/CD enabled
  - Variables set:
    - `SLACK_WEBHOOK_URL`
    - `CI_REGISTRY_USER`, `CI_REGISTRY_PASSWORD`
    - `SONAR_HOST_URL`, `SONAR_TOKEN`

---

## 🧪 Example Usage

Push to `development` branch:
- → Runs tests, sonar scan, deploys to **dev** environment

Push to `staging` branch:
- → Same as above, but deploys to **staging**

Tag a release (`git tag 1.2.3 && git push --tags`):
- → Prompts manual approval
- → Deploys to **production**
- → Sends health status & Slack alerts

---

## 📞 Need Help?

Feel free to open an issue or reach out to the DevOps team if you encounter problems with the pipeline or environment configuration.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
