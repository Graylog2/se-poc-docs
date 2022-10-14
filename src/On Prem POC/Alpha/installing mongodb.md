# Installing MongoDB

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for MongoDB/Graylog. This instance will ONLY have MongoDB/Graylog installed.

---

## Introduction

This page will provide instructions for how to install MongoDB.

## Housekeeping

The code block below can be copy/pasted into a terminal.

```sh
# Housekeeping, Upgrade/update all existing Ubuntu Server packages
sudo apt update && sudo apt upgrade

# Install Prereqs
sudo apt install apt-transport-https openjdk-17-jre-headless uuid-runtime pwgen

```

## Install

---
⚠️ **NOTE**

Graylog requires MongoDB 4.x. Graylog is not compatible with MongoDB 5.x or 6.x

---

The code block below can be copy/pasted into a terminal.

```sh
# Install
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
sudo apt update
sudo apt install -y mongodb-org

# Enable MongoDB service
sudo systemctl daemon-reload
sudo systemctl enable mongod.service
sudo systemctl restart mongod.service
sudo systemctl --type=service --state=active | grep mongod

```

## Verify Completion

```sh
mongo --eval "db.version()"
```

Should return something like:

```
MongoDB shell version v5.0.13
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("<uuid>") }
MongoDB server version: 5.0.13
5.0.13
```