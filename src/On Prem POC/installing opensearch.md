# Installing Opensearch

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for Opensearch. This instance will ONLY have opensearch installed.

---

## Introduction

This page will provide instructions for how to install and configure a single node opensearch cluster.

## Housekeeping

The code block below can be copy/pasted into a terminal.

```sh
# Housekeeping, Upgrade/update all existing Ubuntu Server packages
sudo apt update && sudo apt upgrade

# Install OpenJDK
sudo apt install openjdk-11-jdk

```

## Install

### Install Opensearch via .tar.gz

---
⚠️ **NOTE**

Graylog-server 4.x is currently **not** compatible with Opensearch 2.x. We must use 1.x

---

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
wget https://artifacts.opensearch.org/releases/bundle/opensearch/1.3.4/opensearch-1.3.4-linux-x64.tar.gz

# create directories
sudo mkdir -p /graylog/opensearch/data
sudo mkdir /var/log/opensearch

# extract content from tar and move to install directory
sudo tar -zxf opensearch-1.3.4-linux-x64.tar.gz
sudo mv opensearch-1.3.4/* /graylog/opensearch/

# set permissions
sudo chown -R opensearch:opensearch /graylog/opensearch/
sudo chown -R opensearch:opensearch /var/log/opensearch
sudo chmod -R 2750 /graylog/opensearch/
sudo chmod -R 2750 /var/log/opensearch

# create empty log file
sudo -u opensearch touch /var/log/opensearch/graylog.log

```

### Create System Service

The code blocks below can be copy/pasted into a terminal.

Elevate as root if not already running as root

```sh
sudo su

```

```sh
# create opensearch service
cat > /etc/systemd/system/opensearch.service <<EOF
[Unit]
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
WantedBy=multi-user.target
EOF

```

## Configure

### Create Configuration file

Elevate as root if not already running as root

```sh
sudo su

```

Create Config File

```sh
cp /graylog/opensearch/config/opensearch.yml /graylog/opensearch/config/opensearch.yml.bak
cat > /graylog/opensearch/config/opensearch.yml <<EOF
cluster.name: graylog
node.name: ${HOSTNAME}
path.data: /graylog/opensearch/data
path.logs: /var/log/opensearch
transport.host: 0.0.0.0
network.host: 0.0.0.0
http.port: 9200
discovery.type: single-node
action.auto_create_index: false
plugins.security.disabled: true
EOF

```

Exit su via `exit`.

### Additional Configurations

Additional Configurations, Set Opensearch Memory/Heap Limit, kernel parameters.

Set opensearch heap size

```sh
echo -n "Enter number of GB to set heap size to (number only): " && tmpheap=$(head -1 </dev/stdin)

```

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