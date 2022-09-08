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
sudo apt install apt-transport-https openjdk-11-jre-headless uuid-runtime pwgen

```

## Install

---
⚠️ **NOTE**

Graylog requires MongoDB 4.x. Graylog is not compatible with MongoDB 5.x or 6.x

---

```sh
# Install
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
sudo apt update
sudo apt install -y mongodb-org

# Enable MongoDB service
sudo systemctl daemon-reload
sudo systemctl enable mongod.service
sudo systemctl restart mongod.service
sudo systemctl --type=service --state=active | grep mongod

```