# Pooler Microservices Development Environment Setup 🐳

As a DevOps engineer, I'll leverage Docker and Docker Compose to containerize the Pooler microservices. This approach ensures consistency across development environments, simplifies dependency management, and streamlines deployment, **assuming no software is pre-installed on your machine beyond a basic operating system.**

---

### 1. Initial Setup & Prerequisites with Installation Instructions 🛠️

Before diving into creating Dockerfiles, we'll need to install and configure the following:

#### 1.1. Docker Desktop/Engine

Docker is the cornerstone of this setup. It allows us to build, run, and manage containers.

* **Installation:**
    * **Windows:** Download and run the installer from the official [Docker Desktop for Windows](https://docs.docker.com/desktop/install/windows-install/) page. Follow the on-screen instructions. You might need to enable WSL 2.
    * **macOS:** Download and run the installer from the official [Docker Desktop for Mac](https://docs.docker.com/desktop/install/mac-install/) page. Drag the Docker icon to your Applications folder.
    * **Linux (Docker Engine - Community):** Installation varies slightly depending on your distribution.
        * **Ubuntu:** Follow the instructions on the [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/) page.
        * **Debian:** Follow the instructions on the [Install Docker Engine on Debian](https://docs.docker.com/engine/install/debian/) page.
        * **Fedora:** Follow the instructions on the [Install Docker Engine on Fedora](https://docs.docker.com/engine/install/fedora/) page.
        * **CentOS:** Follow the instructions on the [Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/) page.
    * **Verification:** After installation, open a terminal or command prompt and run:
        ```bash
        docker --version
        docker compose version # Docker Compose V2 is typically bundled with Docker Desktop or installed separately on Linux
        ```
        This will confirm Docker and Docker Compose are installed and accessible.

#### 1.2. Git

Git is essential for cloning the Pooler service repositories.

* **Installation:**
    * **Windows:** Download and run the installer from [Git for Windows](https://git-scm.com/download/win).
    * **macOS:**
        * Using Homebrew (recommended): `brew install git` (install Homebrew first if you don't have it: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`)
        * Using Xcode Command Line Tools: Open a terminal and run `git --version`. If Git is not installed, it will prompt you to install the Xcode Command Line Tools, which includes Git.
    * **Linux:**
        * **Debian/Ubuntu:** `sudo apt update && sudo apt install git`
        * **Fedora:** `sudo dnf install git`
        * **CentOS/RHEL:** `sudo yum install git`
    * **Verification:** Open a terminal and run:
        ```bash
        git --version
        ```

#### 1.3. AWS Account & AWS CLI

For services interacting with AWS (like Pooler Session for SES, Pooler Core API potentially for secrets if not using local mocks), an AWS account is needed, and the AWS CLI is useful for managing resources like SES templates.

* **AWS Account:** If you don't have one, sign up at [Amazon Web Services](https://aws.amazon.com/).
* **AWS CLI Installation:**
    * **General (using `pip` if Python is installed):**
        ```bash
        curl "[https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip](https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip)" -o "awscliv2.zip" # For Linux
        # For Windows/macOS, download the appropriate installer from the link below
        unzip awscliv2.zip
        sudo ./aws/install
        ```
    * **Platform-specific installers:** Detailed instructions and installers for all platforms are available on the [Install or update the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) page.
    * **Configuration:** After installation, configure the CLI with your AWS access keys:
        ```bash
        aws configure
        ```
        You'll be prompted for your AWS Access Key ID, Secret Access Key, default region, and default output format.

#### 1.4. Heroku Account & Heroku CLI (Optional for Dockerized Dev)

While we're containerizing, the document mentions Heroku for environment variables and specific post-launch tasks. Understanding its original deployment context is valuable. The Heroku CLI isn't strictly necessary for a Docker *development* setup but can be useful for inspecting existing Heroku apps or managing Heroku-specific environment variables if you have them defined elsewhere.

* **Heroku Account:** Sign up at [Heroku](https://signup.heroku.com/).
* **Heroku CLI Installation:**
    * **macOS (Homebrew):** `brew install heroku/brew/heroku`
    * **Windows (Standalone installer):** Download from [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli).
    * **Linux:** Follow instructions on the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) page (e.g., using `snap` or `curl` for tarball).
    * **Verification:** `heroku --version`

#### 1.5. PostgreSQL Client

You'll need a client to connect to the PostgreSQL database container for initial setup (running SQL queries).

* **`psql` (Command-line client - recommended):**
    * `psql` usually comes with a PostgreSQL server installation. If you don't want to install the full server locally (since we're using a Docker container for the DB), you can install just the client tools.
    * **Linux (Debian/Ubuntu):** `sudo apt install postgresql-client`
    * **macOS (Homebrew):** `brew install libpq` (then add it to your PATH if needed)
    * **Windows:** You can download the PostgreSQL installer from [PostgreSQL Downloads](https://www.postgresql.org/download/) and select only the "Command Line Tools" component during installation.
* **GUI Clients (Optional):**
    * **pgAdmin 4:** Cross-platform, comprehensive GUI. Download from [pgAdmin](https://www.pgadmin.org/download/).
    * **DBeaver:** Universal database tool. Download from [DBeaver Community](https://dbeaver.io/download/).

#### 1.6. Redis CLI

Used for interacting with Redis, which is a dependency for services using BullMQ (Pooler Transfer, Pooler Wallet, Pooler VAS, Pooler Webhook).

* **Installation:**
    * **Linux (Debian/Ubuntu):** `sudo apt install redis-tools`
    * **macOS (Homebrew):** `brew install redis`
    * **Windows:** You can use the Redis CLI within the Windows Subsystem for Linux (WSL) or find unofficial Windows binaries (search for "Redis for Windows"). Alternatively, you can run a temporary Redis client Docker container: `docker run -it --network host redis redis-cli` (though `host` network might not always be ideal).
* **Verification:** After starting your Redis container, you can connect to it:
    ```bash
    redis-cli -h localhost -p 6379 ping
    ```
    You should get a `PONG` response.

---

### 2. General Dockerfile Structure for Node.js Services 📦

Most of Pooler's server-side services are Node.js applications. We'll use a common Dockerfile structure for them, adapting it for specific service needs. This Dockerfile assumes you have the source code for each service.

```dockerfile
# Use an official Node.js runtime as a parent image
FROM node:18-alpine

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json first to leverage Docker cache
# This means if dependencies don't change, these layers are cached and rebuilds are faster.
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the port the app runs on
EXPOSE <SERVICE_PORT>

# Define the command to run the application
CMD [ "npm", "start" ]
