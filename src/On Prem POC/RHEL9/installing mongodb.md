# Installing MongoDB

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for MongoDB/Graylog. This instance will ONLY have MongoDB/Graylog installed.

---

## Introduction

This page will provide instructions for how to install MongoDB on RHEL 9 distros such as Rocky, Alma, and RHEL. While these instructions should work on any RHEL distro, they have only been tested and validated against Rocky 9, Alma 9, and RHEL 9.

Code blocks below can be copy/pasted into a terminal.

## Housekeeping

Authenticate with sudo at least 1 time to ensure code blocks that have multiple lines with sudo commands run correctly:

```sh
sudo whoami

```

Upgrade existing packages to latest versions. Ensure we start from a clean and updated state.

```sh
# Housekeeping, Upgrade/update all existing Ubuntu Server packages
sudo yum update
```

After update, with these commands:

```sh
# set timezone to UTC
sudo timedatectl set-timezone UTC

```

## Install

Install MongoDB

```sh
# for reference, via https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/
# Install MongoDB 6

# Prevent startup error: vm.max_map_count is too low
sudo sysctl -w vm.max_map_count=262144
echo 'vm.max_map_count=262144' | sudo tee -a /etc/sysctl.conf

echo "[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-7.0.asc" | sudo tee /etc/yum.repos.d/mongodb-org-6.0.repo

sudo yum install -y mongodb-org
```

Enable and Start MongoDB

```sh
# Enable MongoDB service
sudo systemctl daemon-reload
sudo systemctl enable mongod.service
sudo systemctl start mongod.service

```

## Verify Completion

```sh
mongosh --eval "db.version()" --quiet

```

Should return something like:

```
6.0.12
```
