# Installing Graylog Server

---
๐๏ธ **NOTE**

The following steps are completed on the server/instance that will be used for MongoDB/Graylog. This instance will ONLY have MongoDB/Graylog installed.

---

## Introduction

This page will provide instructions for how to install Graylog.

## Housekeeping

Assumes housekeeping steps from [Installing MongoDB](installing%20mongodb.md#housekeeping) have been completed.

## Install

The code block below can be copy/pasted into a terminal.

---
๐๏ธ **NOTE**

The Graylog 5 package `graylog-enterprise` is an all-in-one package for graylog-server and its integration plugins (Enterprise Paid features for Graylog Operations and Graylog Security). It is installed instead of `graylog-server`, which is now used only for Graylog Open Source.

---

```sh
wget https://packages.graylog2.org/repo/packages/graylog-5.0-repository_latest.deb
sudo dpkg -i graylog-5.0-repository_latest.deb
sudo apt update && sudo apt install graylog-enterprise

```

## Configure
### Set Admin Password

You will be prompted to input a password. This will set the password used by the default โadminโ user account.

The code block below can be copy/pasted into a terminal.

```sh
sudo cp /etc/graylog/server/server.conf server.conf.bak
echo -n "Enter admin Password: " && tmppw=$(head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1) && sudo sed -i "s/root_password_sha2 =.*/root_password_sha2 = $tmppw/g" /etc/graylog/server/server.conf

```

### Set password secret (this is used to encrypt passwords for local graylog users)

The code block below can be copy/pasted into a terminal.

```sh
tmppw=$(openssl rand -hex 32)
sudo sed -i "s/password_secret =.*/password_secret = $tmppw/g" /etc/graylog/server/server.conf
tmppw=abc

```

### Bind HTTP server to listen for external connections. Otherwise the Graylog server will only be accessible form the server itself.

The code block below can be copy/pasted into a terminal.

```sh
sudo sed -i 's/#http_bind_address = 127.0.0.1.*/http_bind_address = 0.0.0.0:9000/g' /etc/graylog/server/server.conf

```

### Configure Opensearch server address

The code block below can be copy/pasted into a terminal.

```sh
echo -n "Enter IP of Opensearch Server: " && tmpip=$(head -1 </dev/stdin) && sudo sed -i "s/#elasticsearch_hosts = .*/elasticsearch_hosts = http\:\/\/$tmpip\:9200/g" /etc/graylog/server/server.conf

```

### Configure memory/heap usage

---
โ?๏ธ **NOTE**

The below command will set graylog-server to use 8GB of RAM. Please verify your graylog server is configured with 16GB of RAM.

---

The code block below can be copy/pasted into a terminal.

```sh
sudo sed -i 's/-Xms[0-9]\+g /-Xms8g /g' /etc/default/graylog-server
sudo sed -i 's/-Xmx[0-9]\+g /-Xmx8g /g' /etc/default/graylog-server

```

### Enable Service and Start

The code block below can be copy/pasted into a terminal.

```sh
sudo systemctl daemon-reload
sudo systemctl enable graylog-server.service
sudo systemctl start graylog-server.service
sudo systemctl --type=service --state=active | grep graylog

```

## Verify Completion

```sh
curl localhost:9000/api/
```

Should return something like:

```
{"cluster_id":"<clusterid>","node_id":"<nodeid>","version":"<version>"}
```

