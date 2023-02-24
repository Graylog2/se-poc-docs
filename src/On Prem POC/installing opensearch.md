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

```sh
wget https://artifacts.opensearch.org/releases/bundle/opensearch/2.5.0/opensearch-2.5.0-linux-x64.deb
sudo dpkg -i opensearch-2.5.0-linux-x64.deb

```

## Configure

### Create Configuration file

Create Config File

The code block below can be copy/pasted into a terminal.

```sh
cp /etc/opensearch/opensearch.yml /etc/opensearch/opensearch.yml.bak

echo "cluster.name: graylog
node.name: ${HOSTNAME}
path.data: /var/lib/opensearch
path.logs: /var/log/opensearch
transport.host: 0.0.0.0
network.host: 0.0.0.0
http.port: 9200
discovery.type: single-node
action.auto_create_index: false
plugins.security.disabled: true" | sudo tee /etc/opensearch/opensearch.yml

```

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
sudo sed -i "s/^-Xms[0-9]\+g/-Xms${tmpheap}g/g" /etc/opensearch/jvm.options
sudo sed -i "s/^-Xmx[0-9]\+g/-Xmx${tmpheap}g/g" /etc/opensearch/jvm.options

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
    "number" : "2.5.0",
    "build_type" : "deb",
    "build_hash" : "b8a8b6c4d7fc7a7e32eb2cb68ecad8057a4636ad",
    "build_date" : "2023-01-18T23:48:43.426713304Z",
    "build_snapshot" : false,
    "lucene_version" : "9.4.2",
    "minimum_wire_compatibility_version" : "7.10.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```