# Installing Opensearch

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for Opensearch. This instance will ONLY have opensearch installed.

---

## Introduction

This page will provide instructions for how to install and configure a single node opensearch cluster on Ubuntu 20.04 LTS.

## Housekeeping

The code block below can be copy/pasted into a terminal.

```sh
# Housekeeping, Upgrade/update all existing Ubuntu Server packages
sudo apt update && sudo apt upgrade

sudo timedatectl set-timezone UTC
```

## Install

### Install Opensearch via .tar.gz

---
🗒️ **NOTE**

Opensearch does not currently provide packages for deb/apt installs. A "manual" install is required.

---

The below code block will do the following:

* Create a user that the opensearch service will run as
* Download the opensearch `.tar.gz`
* Create needed directories
* Extract `.tar.gz` and move to permanent directory
* Set folder owner and permissions
* Create empty log file

The code block below can be copy/pasted into a terminal.

``` sh
# Create your OpenSearch user
sudo adduser --system --disabled-password --disabled-login --home /var/empty --no-create-home --quiet --force-badname --group opensearch

# download
wget https://artifacts.opensearch.org/releases/bundle/opensearch/2.4.1/opensearch-2.4.1-linux-x64.tar.gz

# create directories
sudo mkdir -p /graylog/opensearch/data
sudo mkdir /var/log/opensearch

# extract content from tar and move to install directory
sudo tar -zxf opensearch-2.4.1-linux-x64.tar.gz
sudo mv opensearch-2.4.1/* /graylog/opensearch/

# remove empty directory
sudo rm -r opensearch-2.4.1

# cleanup download .tar.gz
rm -f opensearch-2.4.1-linux-x64.tar.gz

# set permissions
sudo chown -R opensearch:opensearch /graylog/opensearch/
sudo chown -R opensearch:opensearch /var/log/opensearch
sudo chmod -R 2750 /graylog/opensearch/
sudo chmod -R 2750 /var/log/opensearch

# create empty log file
sudo -u opensearch touch /var/log/opensearch/graylog.log

```

### Create System Service

The code block below can be copy/pasted into a terminal.

```sh
# create opensearch service
echo "[Unit]
Description=Opensearch
Documentation=https://opensearch.org/docs/latest
Requires=network.target remote-fs.target
After=network.target remote-fs.target
ConditionPathExists=/graylog/opensearch
ConditionPathExists=/graylog/opensearch/data
[Service]
Environment=OPENSEARCH_HOME=/graylog/opensearch
Environment=OPENSEARCH_PATH_CONF=/graylog/opensearch/config
ReadWritePaths=/var/log/opensearch
User=opensearch
Group=opensearch
WorkingDirectory=/graylog/opensearch
ExecStart=/graylog/opensearch/bin/opensearch
# Specifies the maximum file descriptor number that can be opened by this process
LimitNOFILE=65535
# Specifies the maximum number of processes
LimitNPROC=4096
# Specifies the maximum size of virtual memory
LimitAS=infinity
# Specifies the maximum file size
LimitFSIZE=infinity
# Disable timeout logic and wait until process is stopped
TimeoutStopSec=0
# SIGTERM signal is used to stop the Java process
KillSignal=SIGTERM
# Send the signal only to the JVM rather than its control group
KillMode=process
# Java process is never killed
SendSIGKILL=no
# When a JVM receives a SIGTERM signal it exits with code 143
SuccessExitStatus=143
# Allow a slow startup before the systemd notifier module kicks in to extend the timeout
TimeoutStartSec=180
[Install]
WantedBy=multi-user.target" | sudo tee /etc/systemd/system/opensearch.service

```

## Configure

### Create Configuration file

Create Config File

The code block below can be copy/pasted into a terminal.

```sh
cp /graylog/opensearch/config/opensearch.yml /graylog/opensearch/config/opensearch.yml.bak

echo "cluster.name: graylog
node.name: ${HOSTNAME}
path.data: /graylog/opensearch/data
path.logs: /var/log/opensearch
transport.host: 0.0.0.0
network.host: 0.0.0.0
http.port: 9200
discovery.type: single-node
action.auto_create_index: false
plugins.security.disabled: true" | sudo tee /graylog/opensearch/config/opensearch.yml

```

Exit su via `exit`.

### Additional Configurations

Additional Configurations, Set Opensearch Memory/Heap Limit, kernel parameters.

Set opensearch heap size. Heap size should be set to **half of the amount of RAM of the server**. For example, if the server has 32GB of ram, set the heap to 16GB.

---
⚠️ **NOTE**

Heap size should not exceep 31GB.

---

The code block below can be copy/pasted into a terminal.

```sh
echo -n "Enter number of GB to set heap size to (number only): " && tmpheap=$(head -1 </dev/stdin)

```

The code block below can be copy/pasted into a terminal.

```sh
# open search java heap sizing (first command is min, second is max)
sudo sed -i "s/^-Xms[0-9]\+g/-Xms${tmpheap}g/g" /graylog/opensearch/config/jvm.options
sudo sed -i "s/^-Xmx[0-9]\+g/-Xmx${tmpheap}g/g" /graylog/opensearch/config/jvm.options

# Configure the kernel parameters at runtime
sudo sysctl -w vm.max_map_count=262144
echo 'vm.max_map_count=262144' | sudo tee -a /etc/sysctl.conf

# enable and start opensearch service
sudo systemctl daemon-reload
sudo systemctl enable opensearch.service
sudo systemctl start opensearch.service

```

## Verify Completion

---
⚠️ **NOTE**

Opensearch may take a few seconds to start up. If you receive the following message, `curl: (7) Failed to connect to localhost port 9200: Connection refused`, wait a few seconds and try again.

---

```sh
curl localhost:9200
```

Should return something like:

```json
{
  "name" : "<servername>",
  "cluster_name" : "graylog",
  "cluster_uuid" : "<uuid>",
  "version" : {
    "distribution" : "opensearch",
    "number" : "2.4.1",
    "build_type" : "tar",
    "build_hash" : "...",
    "build_date" : "...",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```