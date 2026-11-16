# Kanban Dashboard – Docker & Jenkins CI/CD on AWS

## Project Overview

This project demonstrates the deployment of a React + TypeScript Kanban Dashboard using Docker, Jenkins CI/CD, Docker Hub, GitHub, and AWS EC2.

The application is containerized using a multi-stage Docker build and served through Nginx running as a non-root user.

The CI/CD pipeline automatically builds, tests, and deploys the application whenever code is pushed to the GitHub repository.

## Live Application

**Application URL:** http://52.66.157.239

**Jenkins Dashboard:** http://52.66.157.239:8080

## Project Repositories

- **GitHub:** https://github.com/gilllavish25/kanban-dashboard
- **Docker Hub:** https://hub.docker.com/r/gilllavish25/lavish-kanban-dashboard

## Technologies Used

- React
- TypeScript
- Vite
- Docker
- Docker Hub
- Jenkins
- GitHub
- AWS EC2
- Ubuntu Linux
- Nginx
- GitHub Webhooks

## Project Architecture

GitHub → GitHub Webhook → Jenkins → Docker Build → Docker Hub → AWS EC2 → Running Application

## CI/CD Pipeline Workflow

1. Developer pushes code to the GitHub repository.
2. GitHub Webhook automatically triggers Jenkins.
3. Jenkins checks out the latest source code.
4. Jenkins installs dependencies and builds the React application.
5. Docker builds the application image using a multi-stage Dockerfile.
6. Docker image is tagged with the Jenkins build number and `latest`.
7. Jenkins pushes the images to Docker Hub.
8. Jenkins connects to the deployment environment and pulls the new image.
9. The previous application container is replaced with the new container.
10. Jenkins verifies the deployment using an HTTP health check.

## Docker Implementation

The application uses a multi-stage Docker build.

**Build Stage:**
- Uses Node.js Alpine image.
- Installs dependencies using `npm ci`.
- Builds the React application.

**Production Stage:**
- Uses Nginx Unprivileged Alpine image.
- Copies the production build into the Nginx web directory.
- Runs Nginx as a non-root user.
- Exposes port 8080 inside the container.
- Includes a Docker health check.

## Docker Security

The project includes the following security practices:

- Multi-stage Docker build to reduce the final image size.
- Non-root container execution.
- Docker image versioning using Jenkins build numbers.
- `.dockerignore` to exclude unnecessary files.
- Alpine-based Docker images.
- Package updates during the Docker build.
- Docker resource limits.

## Docker Resource Limits

The production container runs with:

- Memory limit: 128 MB
- CPU limit: 0.5 CPU
- Restart policy: `unless-stopped`

## Jenkins CI/CD Configuration

**Jenkins Job:** `Kanban-Dashboard-CI-CD`

The Jenkins pipeline includes the following stages:

- GitHub Checkout
- Docker Build
- Docker Hub Push
- Application Deployment
- Deployment Health Check

Docker images are tagged using Jenkins build numbers to maintain version history.

Example:

- `gilllavish25/lavish-kanban-dashboard:26`
- `gilllavish25/lavish-kanban-dashboard:latest`

## GitHub Webhook Integration

GitHub Webhooks are configured to trigger Jenkins automatically whenever a new commit is pushed to the `main` branch.

The Webhook Relay integration forwards GitHub events to Jenkins without requiring the Jenkins port to be publicly accessible to GitHub.

The webhook was tested successfully by pushing a new commit to GitHub and verifying that Jenkins automatically started and completed the build.

## Health Checks and Monitoring

The project uses Docker health checks to monitor application availability.

The health check sends an HTTP request to the application running inside the container.

Jenkins also performs an HTTP check after deployment to verify that the application is responding successfully.

## Automatic Rollback

The deployment pipeline includes rollback logic.

If the new application deployment fails its health check, the pipeline attempts to restore the previous Docker image.

A separate Jenkins job, `Kanban-Automatic-Rollback-Test`, was used to test the rollback process.

The isolated rollback test successfully detected a simulated failure, restored the previous working image, and verified HTTP 200.

## Run the Application Locally

Clone the repository:

```bash
git clone https://github.com/gilllavish25/kanban-dashboard.git
cd kanban-dashboard
```

Install dependencies:

```bash
npm ci
```

Start the development server:

```bash
npm run dev
```

Build the application:

```bash
npm run build
```

## Run Using Docker

Build the Docker image:

```bash
docker build -t kanban-dashboard .
```

Run the container:

```bash
docker run -d \
  --name kanban-app \
  --restart unless-stopped \
  --memory=128m \
  --cpus=0.5 \
  -p 80:8080 \
  kanban-dashboard
```

Check the running container:

```bash
docker ps
```

Check container health:

```bash
docker inspect --format='{{.State.Health.Status}}' kanban-app
```

## Project Outcome

This project demonstrates practical experience with:

- Containerizing a React application using Docker.
- Creating multi-stage Docker builds.
- Running containers securely as a non-root user.
- Managing Docker images using Docker Hub.
- Configuring Jenkins CI/CD pipelines.
- Integrating GitHub Webhooks with Jenkins.
- Deploying applications on AWS EC2.
- Implementing health checks and resource limits.
- Testing automatic rollback in an isolated environment.

## Author

**Lavish Gill**

GitHub: https://github.com/gilllavish25
