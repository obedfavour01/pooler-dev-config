# Pooler Microservices Development Environment Setup 

### 1. Initial Setup & Prerequisites with Installation Instructions 

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

#### 1.3\. Install Node.js (Official Website)**

-   Go to the official Node.js website: <https://nodejs.org/en>

-   Download the **LTS (Long Term Support)** installer for your operating system (Windows .msi, macOS .pkg).

-   Run the downloaded installer and follow the on-screen prompts, accepting defaults.

-   **Verify:** Open your terminal/command prompt and run `node -v` and `npm -v`.

* * * * *

**\. Install NVM (Node Version Manager) & Node.js 18.20.7**

NVM allows easy switching between Node.js versions.

-   **Install NVM:**

    -   **macOS/Linux:** Open your terminal and run:

        Bash

        ```
        curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

        ```

        *(Note: Check the NVM GitHub page for the very latest install script if v0.39.7 isn't current.)*

    -   **Windows:** Download the `nvm-setup.zip` from the latest `nvm-windows` releases on GitHub (<https://github.com/coreybutler/nvm-windows/releases>), unzip, and run the installer.

-   **Verify NVM:** Close and reopen your terminal. Run `nvm --version` (macOS/Linux) or `nvm version` (Windows).

-   **Install Node.js 18.20.7 and set it as default:**

    Bash

    ```
    nvm install 18.18.0
    nvm use 18.18.0
    ```

-   **Verify:** Run `node -v` and `npm -v` to confirm Node.js 18.20.7 is active.

#### 1.4. AWS Account & AWS CLI

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


#### 1.6. PostgreSQL Client

You'll need a client to connect to the PostgreSQL database container for initial setup (running SQL queries).

* **`psql` (Command-line client - recommended):**
    * `psql` usually comes with a PostgreSQL server installation. If you don't want to install the full server locally (since we're using a Docker container for the DB), you can install just the client tools.
    * **Linux (Debian/Ubuntu):** `sudo apt install postgresql-client`
    * **macOS (Homebrew):** `brew install libpq` (then add it to your PATH if needed)
    * **Windows:** You can download the PostgreSQL installer from [PostgreSQL Downloads](https://www.postgresql.org/download/) and select only the "Command Line Tools" component during installation.
* **GUI Clients (Optional):**
    * **pgAdmin 4:** Cross-platform, comprehensive GUI. Download from [pgAdmin](https://www.pgadmin.org/download/).
    * **DBeaver:** Universal database tool. Download from [DBeaver Community](https://dbeaver.io/download/).

#### 1.7. Redis CLI

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

### 2. Repository Setup

#### Clone Repositories

```bash
# Create project directory

mkdir pooler-development
cd pooler-development

git clone https://github.com/P-UP-MFB/pooler-ui.git
git clone https://github.com/P-UP-MFB/pooler-backoffice-ui.git
git clone https://github.com/P-UP-MFB/pooler-simulator.git
git clone https://github.com/P-UP-MFB/pooler-checkout-script.git
git clone https://github.com/P-UP-MFB/pooler-checkout-middleware.git
git clone https://github.com/P-UP-MFB/pooler-mobile.git


git clone https://github.com/P-UP-MFB/pooler-session.git
git clone https://github.com/P-UP-MFB/pooler-core-api.git
git clone https://github.com/P-UP-MFB/pooler-reader.git
git clone https://github.com/P-UP-MFB/pooler-transfer.git
git clone https://github.com/P-UP-MFB/pooler-webhook.git
git clone https://github.com/P-UP-MFB/pooler-wallet.git
git clone https://github.com/P-UP-MFB/pooler-vas.git
git clone https://github.com/P-UP-MFB/pooler-checkout.git
git clone https://github.com/P-UP-MFB/pooler-backoffice-api.git
 ```


### DB Setup

####  - Create the `pooler` Database

From the default `postgres` database or using `psql`:

```bash

CREATE DATABASE pooler;
```

#### - Connect to `pooler` Database

```bash


`psql -U postgres -d pooler`

# Or from inside `psql`:

\c pooler

```


### Pooler Session

```bash
cd pooler-development/pooler-session

# Install the dependencies needed for the application to run successfully.
npm install


# Create Schema(this command must be run in the postgres terminal and ensure you are in the pooler Db)
CREATE SCHEMA pooler_session;


** Create a .env and use the .env.txt reference and populate as required **
touch .env

#To run migrations
npm run migrate:up


# To start services
npm run dev


```

### Pooler Reader

```bash
cd pooler-development/pooler-reader

# Install the dependencies needed for the application to run successfully.
npm install

# To start services
npm run dev


```

### Pooler Core Api

```bash
cd pooler-development/pooler-core-api

# Install the dependencies needed for the application to run successfully.
npm install

# Create Schema(this command must be run in the postgres terminal and ensure you are in the pooler Db)
CREATE SCHEMA pooler_core_api;


** Create a .env and use the .env.txt reference and populate as required **
touch .env

#To run migrations
npm run migrate:up


# To start services
npm run dev
```

### Pooler Wallet

```bash
cd pooler-development/pooler-wallet

cd src

# Install the dependencies needed for the application to run successfully.
npm install


# Create Schema(this command must be run in the postgres terminal and ensure you are in the pooler Db)
CREATE SCHEMA pooler_wallet;


** Create a .env and use the .env.txt reference and populate as required **
touch .env

#To run migrations
npm run migrate:up


# To start services
npm run dev

# To start worker services
npm run startWorker

```


### Pooler Transfer
```bash
cd pooler-development/pooler-transfer

# Install the dependencies needed for the application to run successfully.
npm install

# # Create Schema(this command must be run in the postgres terminal and ensure you are in the pooler Db)
CREATE SCHEMA pooler_transfer;


** Create a .env and use the .env.txt reference and populate as required **
touch .env

#To run migrations
npm run migrate:up


# To start services
npm run dev

# To start worker services
npm run startWorker

```

### Pooler vas
```bash
cd pooler-development/pooler-vas

cd src

# Install the dependencies needed for the application to run successfully.
npm install

# # Create Schema(this command must be run in the postgres terminal and ensure you are in the pooler Db)
CREATE SCHEMA pooler_vas;


** Create a .env and use the .env.txt reference and populate as required **
touch .env

#To run migrations
npm run migrate:up


# To start services
npm run dev

# To start worker services
npm run startWorker
```

### Pooler webhook
```bash
cd pooler-development/pooler-webhook


# Install the dependencies needed for the application to run successfully.
npm install

# # Create Schema(this command must be run in the postgres terminal and ensure you are in the pooler Db)
CREATE SCHEMA pooler_webhook;


** Create a .env and use the .env.txt reference and populate as required **
touch .env

#To run migrations
npm run migrate:up


# To start services
npm run dev

# To start worker services
npm run startWorker
```



### Pooler Checkout

```bash
cd pooler-development/pooler-checkout

# Install the dependencies needed for the application to run successfully.
npm install

# To start services
npm run dev


```

### Pooler BackOffice Api

Make sure to have the following environment variables set in your `.env` file:

```bash


#### Install dependencies

# Make sure you have Node.js and npm installed
npm install


# Create Schema(this command must be run in the postgres terminal and ensure you are in the pooler Db)
CREATE SCHEMA pooler_backoffice_api;


** Create a .env and use the .env.txt reference and populate as required **
touch .env
#### Instructions to run the project

# run these following in these order
- npm run db:migrate
- npm run db:session
- npm run db:core
- npm run db:wallet
- npm run db:transfer
- npm run db:vas
- npm run db:backoffice



#### Start the application

# Start the application in development mode
npm run dev
# Start the application in production mode
npm run start

````

### Pooler Web UI

##### Make sure to sure to use the .env.txt file as a guide to create a .env file  

```bash

cd pooler-development/pooler-web-ui


# Install the dependencies needed for the application to run successfully.
npm install


** Create a .env and use the .env.txt reference and populate as required **
touch .env


# To start services
npm run dev

```

### Pooler Checkout Middleware

##### Make sure to sure to use the .env.txt file as a guide to create a .env file  

```bash

cd pooler-development/pooler-checkout-middleware


# Install the dependencies needed for the application to run successfully.
npm install

# To start services
npm run dev

```
