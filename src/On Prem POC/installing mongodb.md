# Installing MongoDB

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for MongoDB/Graylog. This instance will ONLY have MongoDB/Graylog installed.

---

## Introduction

This page will provide instructions for how to install MongoDB on Ubuntu **22**.04 LTS.

## Housekeeping

Authenticate with sudo at least 1 time to ensure code blacks that have multiple lines with sudo commands run correctly:

The code block below can be copy/pasted into a terminal.

```sh
sudo whoami

```

The code block below can be copy/pasted into a terminal.

```sh
# Housekeeping, Upgrade/update all existing Ubuntu Server packages
sudo apt update && sudo apt upgrade -y

# Install Prereqs
sudo apt install -y apt-transport-https uuid-runtime pwgen

# set timezone to UTC
sudo timedatectl set-timezone UTC

```

## Install

Install MongoDB

The code block below can be copy/pasted into a terminal.

```sh
# for reference, via https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/
# Install MongoDB 6
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt update
sudo apt install -y mongodb-org mongodb-mongosh

```

Enable and Start MongoDB

The code block below can be copy/pasted into a terminal.

```
# Enable MongoDB service
sudo systemctl daemon-reload
sudo systemctl enable mongod.service
sudo systemctl start mongod.service
sudo systemctl --type=service --state=active | grep mongod

```

## Verify Completion

```sh
mongosh --eval "db.version()" --quiet

```

Should return something like:

```
6.0.5
```
