# Jenkins with Docker Agent (Docker-in-Docker) Setup

This repository provides a ready-to-run Jenkins environment using Docker Compose. It's specifically configured to enable Jenkins pipelines to utilize Docker agents via a Docker-in-Docker (DinD) setup.

This allows you to define pipeline stages that run inside specific Docker containers, providing clean, isolated, and reproducible build environments.

## Features

*   **Docker Compose:** Orchestrates the Jenkins controller and Docker-in-Docker services.
*   **Custom Jenkins Image:** Builds upon the official Jenkins LTS image (`jenkins/jenkins:2.492.3-jdk17`) and installs:
    *   Docker CLI (`docker-ce-cli`) - To communicate with the DinD service.
    *   Essential Jenkins plugins: `blueocean` and `docker-workflow`.
*   **Docker-in-Docker (DinD):** A dedicated service (`jenkins-docker`) running `docker:dind` allows the Jenkins controller to spawn sibling Docker containers for pipeline agents.
*   **Secure Connection:** Uses TLS to secure the connection between the Jenkins controller and the DinD service.
*   **Persistent Data:** Uses named Docker volumes (`jenkins-data`, `jenkins-docker-certs`) to persist Jenkins configuration, job data, and TLS certificates across container restarts.
*   **Sample Pipeline:** Includes a `Jenkinsfile.groovy` demonstrating a basic pipeline using a `python:alpine` Docker agent.

## Prerequisites

*   Docker
*   Docker Compose

## Getting Started

### 1. Clone the repository:

```bash
git clone jenkins-stencil
cd jenkins-stencil
```

### 2. Build and start the services

```bash
docker compose up -d --build
```
*   `--build`: Ensures the custom `jenkins-server` image is built using the `Dockerfile`.
*   `-d`: Runs the containers in detached mode (in the background).

### 3. Access Jenkins:

Open your web browser and navigate to `http://localhost:8080`.
Jenkins needs an initial admin password to unlock. You can retrieve it by running:
```bash
docker compose exec jenkins-server cat /var/jenkins_home/secrets/initialAdminPassword
```
Copy the password from the terminal and paste it into the Jenkins setup wizard.
Follow the on-screen instructions to complete the initial setup (you can choose "Install suggested plugins" or select your own, though the essential ones are already included).

### 4. Create a Pipeline Job:
*   Once Jenkins is running, create a new item.
*   Choose "Pipeline" and give it a name (e.g., "Docker Agent Test").
*   In the job configuration, scroll down to the "Pipeline" section.
*   Select "Pipeline script from SCM" from the Definition dropdown.
*   Choose "Git" as the SCM.
*   Enter the Repository URL (if you pushed this repo to Git) or simply change the Definition to "Pipeline script" and paste the content of `Jenkinsfile.groovy` into the text area for a quick test.
*   Ensure the "Script Path" is `Jenkinsfile.groovy` (the default).
*   Save the job.

### 5. Run the Pipeline
*   Click "Build Now" on the pipeline job page.
*   Observe the console output. You should see Jenkins pulling the `python:alpine` image (if not already present) and executing the steps defined in the `Jenkinsfile.groovy` inside that container.

## File Structure

*   `README.md`: This file.
*   `compose.yaml`: Defines the Docker Compose services (`jenkins-server`, `jenkins-docker`), networks, and volumes.
*   `./jenkins/Dockerfile`: Defines the custom `jenkins-server` image, installing necessary tools and plugins.
*   `./jenkins/Jenkinsfile.groovy`: A sample declarative pipeline demonstrating the use of a Docker agent.

## Stopping and Cleaning Up

*   To stop the running containers:
    ```bash
    docker compose down
    ```
*   To stop the containers and remove the associated volumes (Jenkins data, certificates):
    ```bash
    docker compose down -v
    ```

