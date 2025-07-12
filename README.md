# Install and Configure GitLab Using Docker Compose

## Introduction

In modern DevOps workflows, tools like **Docker**, **Docker Compose**, and **GitLab** play a crucial role in enabling automation, collaboration, and scalability for development and deployment pipelines.

### What is Docker?

Docker is a platform that enables developers and operations teams to build, ship, and run applications in **lightweight, portable containers**. These containers package software along with all its dependencies and configurations, ensuring consistent behavior across development, testing, and production environments.

Key benefits of Docker:
- Environment consistency across all stages of the software lifecycle
- Fast and isolated application deployments
- Efficient use of system resources

### What is Docker Compose?

Docker Compose is a tool for defining and running **multi-container Docker applications** using a single YAML file (`docker-compose.yml`). It allows you to:

- Define services (e.g., web, database, CI server) and how they interact
- Set up volumes, networks, and ports declaratively
- Start and stop all services with a single command (`docker-compose up/down`)

This is especially useful for local development environments and microservices architectures.

### What is GitLab?

**GitLab** is an open-source DevOps platform that provides a single application for:

- **Version control** using Git
- **CI/CD pipelines** for automating testing and deployment
- **Issue tracking**, code reviews, wikis, and more
- **Self-hosted DevOps automation**

GitLab enables teams to **collaborate efficiently**, manage source code securely, and deploy infrastructure-as-code or applications using robust CI/CD tools.

---

## Architecture Diagram

```mermaid
flowchart TD
    A[Your Local Machine] -->|uses| B[Docker]
    B -->|runs| C[Docker Compose]
    C -->|orchestrates| D[GitLab Container]
    D -->|serves| E[GitLab Web Interface]
    D -->|binds| F[Ports 80, 443, 22]
    D -->|writes to| G[Volumes (data, logs, config)]
```

> This diagram shows how your local machine uses Docker Compose to orchestrate a GitLab container, which provides services via web and SSH, and persists state using local volumes.

---

## Prerequisites

Before proceeding, ensure your system meets the following requirements:

- **Docker Desktop**: Installed and running. [Install Docker](https://www.docker.com/products/docker-desktop)
- **Docker Compose**: Familiarity with `docker-compose.yml` syntax is helpful.
- **Internet Connection**: Required to pull GitLab images from Docker Hub.

---

## Step 1: Create a `docker-compose.yml` File

Create a `docker-compose.yml` file in your project directory with the following configuration:

```yaml
version: '3.8'

services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    platform: linux/amd64
    hostname: 'localhost' # Replace with your IP if needed (e.g., 162.148.1.124)
    ports:
      - '80:80'     # HTTP
      - '443:443'   # HTTPS
      - '22:22'     # SSH for Git
    volumes:
      - './config:/etc/gitlab'
      - './logs:/var/log/gitlab'
      - './data:/var/opt/gitlab'
```

---

## Step 2: Launch GitLab

From the directory containing your `docker-compose.yml`, start GitLab using:

```bash
docker-compose up -d
```

> The `-d` flag runs the containers in detached mode.

Docker will download the image and initialize GitLab.

---

## Step 3: Access GitLab Web Interface

Once GitLab is running:

- Open your browser and visit: `http://localhost`  
  *(or use the hostname/IP from your compose file, e.g., `http://162.148.1.124`)*

- You will be prompted to **set a root password** for the initial login.

---

## Step 4: Common GitLab Management Commands

Use these commands to manage your GitLab instance:

```bash
docker-compose down        # Stop and remove containers
docker-compose restart     # Restart GitLab
docker-compose logs -f     # View real-time logs
```

---

## Step 5: Reset or Set Root Password (Optional)

If needed, reset the GitLab root user’s password:

```bash
docker exec -it gitlab bash
gitlab-rails console -e production
```

Then run the following inside the console:

```ruby
user = User.find_by(username: 'root')
user.password = 'new_password'
user.password_confirmation = 'new_password'
user.save!
```

---

## Step 6: Configure External URL

To set a custom `external_url`:

```bash
docker exec -it gitlab bash -c "echo \"external_url 'http://localhost'\" >> /etc/gitlab/gitlab.rb && gitlab-ctl reconfigure"
```

> Replace `http://localhost` with your desired domain or IP.

---

## Final Notes

- **Data Persistence**: Volumes ensure your GitLab data survives restarts.
- **Port Conflicts**: Ensure ports 80, 443, and 22 are not in use by other services.
- **Production Use**: This setup is intended for local development/testing. For production, consider using GitLab’s official [Omnibus installation guide](https://docs.gitlab.com/ee/install/).

---

## Resources

- [GitLab Docs](https://docs.gitlab.com/)
- [Docker Compose Docs](https://docs.docker.com/compose/)
- [Docker Official Site](https://www.docker.com/)

---

## About Me

**Emmanuel Naweji, 2025**  
**Cloud | DevOps | SRE | FinOps | AI Engineer**  
Helping businesses modernize infrastructure and guiding engineers into top 1% career paths through real-world projects and automation-first thinking.

---

## Places to Find Me

- [LinkedIn](https://www.linkedin.com/in/ready2assist/)
- [GitHub](https://github.com/Here2ServeU)
- [Medium](https://medium.com/@here2serveyou)

---

## Book a Free Consultation

Ready to adopt GitOps or scale your Kubernetes infrastructure?  
[Schedule a free 1:1 consultation](https://bit.ly/letus-meet)
