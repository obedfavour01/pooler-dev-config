# Pooler Microservices Development Environment Setup 🐳

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
### 3.  Database and Redis Setup


---

## Start Database and Redis

To begin, spin up your **PostgreSQL** and **Redis** services using Docker Compose:

```bash

docker-compose up -d postgres redis

````

**Wait for Postgres to be Healthy:** Ensure the `postgres` service is fully operational before proceeding. You can monitor its status with:

```bash

docker-compose ps

```

Or by tailing its logs:

```bash

docker-compose logs postgres

```

-----

## Initial Database Setup

Connect to your PostgreSQL instance using a client like **psql**. Connection details can be found in your `docker-compose.yml` (e.g., `host: localhost`, `port: 5432`, `user: user`, `password: password`, `database: pooler_db`).

### For Pooler Core API

  * **Create Ledgers and Savings Accounts:** Manually set up the necessary ledgers and savings accounts on your local "PUP's CBA". For development, this might involve direct inserts into `pooler_db` or using a simulated CBA interface.

  * **Run SQL Queries:**

      * Execute the **SQL QUERY TO CREATE PARTNERS**.

      * Execute the **SQL QUERY TO CREATE CONFIGURATIONS**. ⚠️ **Important:** Replace placeholders such as `<woodcore_collection_fee_savings_account_no>`, `<woodcore_transfer_fee_ledger_id>`, `<woodcore_balance_with_partner_ledger_id>`, `<bank_contact_email_address>`, and `<bank_contact_mobile_number>` with appropriate test values.

  * **Generate `core_banking_token`:** Use the provided Node.js encryption snippet and your test CBA API key to generate this token. Update your `docker-compose.yml` for `pooler-core-api` with this generated token.

  * **Run SQL Dump:** Execute the **SQL DUMP TO CREATE BANKS**.

### For Pooler Session

  * **Run SQL Query:** Execute the **SQL query to set your version config for the mobile app**.

### For Pooler Transfer

  * **Create Ledgers and Savings Accounts:** Create the following accounts on your local "PUP's CBA":

      * Pooler Account Transfer Ledger

      * PUP NIBSS Holding Ledger

      * Woodcore Collection Fee Account

      * PUP NIBSS Income Ledger

      * Pooler NIBSS Settlement Ledger

  * **Run SQL Query:** Execute the **SQL QUERY TO CREATE TRANSFER PARTNERS**. Carefully replace all placeholders, including `<wc_partner_ledger_id_value_in_partner_table_on_core_api>`, `<PUP_BANK_CODE>`, `<p_up_pooler_account_liability_ledger>`, and `<pup_plain_api_key>`, with your specific development values.

### For Pooler VAS

  * **Create Ledgers:** Create the `Pooler VAS Partner Ledger` and `Pooler VAS Income Ledger` on your local "PUP's CBA".

  * **Run SQL Query:** Execute the **SQL QUERY TO RUN (for Configurations table)**.

      * Generate the encrypted `partner_access` using the provided snippet, your chosen `ENCRYPTION_KEY`, and `Bearer <partner_api_key>`. Update your `docker-compose.yml` for `pooler-vas` with this generated value.

### For Pooler Backoffice API

  * If you've altered the schema name, ensure you update the migration files accordingly. The `postgres` service handles the initial database creation.

-----

# 5.3. Build and Run Services

-----

## Build All Images

Build the Docker images for all services defined in your `docker-compose.yml`:

```bash

docker-compose build

```

-----

## Start All Services

Once the images are built, start all your containerized services in detached mode:

```bash

docker-compose up -d

```

-----

## Verify Service Status

To confirm that all services are running as expected, check their status:

```bash

docker-compose ps

```

And monitor their logs for any startup errors:

```bash

docker-compose logs -f

```

```

```

