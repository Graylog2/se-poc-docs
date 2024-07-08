
---
🗒️ **NOTE**

This information is provided for reference and is not intended to be used for running Graylog in a production capacity. Information taken from the following sources:

* [Graylog2/docker-compose](https://github.com/Graylog2/docker-compose) (GitHub)
* [Docker](https://go2docs.graylog.org/current/downloading_and_installing_graylog/docker_installation.htm) (Graylog Docs)

---

# Prerequisites

* [Docker Desktop](https://docs.docker.com/get-docker/)
* Or [Docker Engine](https://docs.docker.com/engine/install/)

# Preparation

* Download files in this directory`
    * `docker-compose.yml`
    * `env.txt`
* Edit `env.txt` and provide values for
    * `GRAYLOG_PASSWORD_SECRET`
        * Used to encrypt Graylog user passwords
        * Generate via `pwgen -N 1 -s 96` or generate a random password using an online tool or password manager
    * `GRAYLOG_ROOT_PASSWORD_SHA2`
        * This is the password for Graylog's default, built-in `admin` user account.
        * Generate via `echo -n yourpassword | shasum -a 256 | cut -d" " -f1`
            * Replace `yourpassword` with the password you want to set.

Once started, Graylog will be accessible using a web browser via port 9000 (TCP/9000).

# Start Graylog via Docker and Docker Compose File

Via a terminal:

1. navigate to the directory where your `docker-compose.yml` and `env.txt` files are.
2. Execute:
    `docker compose -f docker-compose.yml --env-file env.txt up -d`

# Stop And Remove Graylog via Docker Compose

Via a terminal:

1. navigate to the directory where your `docker-compose.yml` and `env.txt` files are.
2. Execute:
    `docker compose -f docker-compose.yml --env-file env.txt down`
