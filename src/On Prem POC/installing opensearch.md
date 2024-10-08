# Installing Opensearch

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for Opensearch. This instance will ONLY have opensearch installed.

---

## Introduction

This page will provide instructions for how to install and configure a single node opensearch cluster on Ubuntu **22**.04 LTS.

Server Recommendations:

| Server Role       | CPU Cores | RAM  |
| ----------------- | --------- | ---- |
| OpenSearch        | 8         | 24GB |

Code blocks below can be copy/pasted into a terminal.

## Housekeeping

Authenticate with sudo at least 1 time to ensure code blocks that have multiple lines with sudo commands run correctly:

```sh
sudo whoami

```

Upgrade existing packages to latest versions. Ensure we start from a clean and updated state.

```sh
# Housekeeping, Upgrade/update all existing Ubuntu Server packages
sudo apt update && sudo apt upgrade -y
```

During `apt upgrade` you may be presented with a screen that looks like this:

![Ubuntu Pending kernel upgrade screenshot](img/ubuntu-apt-update-prompt.png)

1. Press Enter `<Ok>`
2. You will then be presented with a screen that lists some services.
    * ![Ubuntu Daemons using outdated libraries screenshot](img/ubuntu-daemons-using-outdated.png)
3. Leave the defaults, press Tab to select `<Ok>` and then press Enter.

After this is complete, reboot the server to ensure all upgrades/updates are fully applied:

```sh
sudo shutdown -r now

```

After reboot, continue with these commands:

```sh
# set timezone to UTC
sudo timedatectl set-timezone UTC

# disable cloud-init, not needed for our purposes
sudo touch /etc/cloud/cloud-init.disabled

```

## Install

```sh
# verify prereqs are present
sudo apt-get update && sudo apt-get -y install lsb-release ca-certificates curl gnupg2
# download signing key
curl -o- https://artifacts.opensearch.org/publickeys/opensearch.pgp | sudo gpg --dearmor --batch --yes -o /usr/share/keyrings/opensearch-keyring
# create repository file
echo "deb [signed-by=/usr/share/keyrings/opensearch-keyring] https://artifacts.opensearch.org/releases/bundle/opensearch/2.x/apt stable main" | sudo tee /etc/apt/sources.list.d/opensearch-2.x.list
# install opensearch
sudo apt-get update
sudo env OPENSEARCH_INITIAL_ADMIN_PASSWORD=$(tr -dc A-Z-a-z-0-9_@#%^-_=+ < /dev/urandom  | head -c${1:-32}) apt-get -y install opensearch=2.15.0


# set default value for heap variable
tmpheap=1

```

## Configure

### Create Configuration file

Create Config File

```sh
sudo cp /etc/opensearch/opensearch.yml /etc/opensearch/opensearch.yml.bak

echo "cluster.name: graylog
node.name: ${HOSTNAME}
path.data: /var/lib/opensearch
path.logs: /var/log/opensearch
transport.host: 0.0.0.0
network.host: 0.0.0.0
http.port: 9200
discovery.type: single-node
action.auto_create_index: false
plugins.security.disabled: true
indices.query.bool.max_clause_count: 32768" | sudo tee /etc/opensearch/opensearch.yml

```

### Additional Configurations

Additional Configurations, Set Opensearch Memory/Heap Limit, kernel parameters.

Set opensearch heap size. Heap size should be set to **half of the amount of RAM of the server**. For example, if the server has 32GB of ram, set the heap to 16GB.

---
⚠️ **NOTE**

Heap size should not exceed 31GB.

---

This command will prompt for the heap size number, in GB.

```sh
echo -n "Enter number of GB to set heap size to (number only): " && tmpheap=$(head -1 </dev/stdin)

```

Continue with these commands:

```sh
# open search java heap sizing (first command is min, second is max)
sudo sed -i "s/^-Xmx[0-9]\+g/-Xmx${tmpheap}g/g" /etc/opensearch/jvm.options && sudo sed -i "s/^-Xms[0-9]\+g/-Xms${tmpheap}g/g" /etc/opensearch/jvm.options

# Configure the kernel parameters at runtime
# See https://opensearch.org/docs/latest/install-and-configure/install-opensearch/index/#important-settings
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
    "number" : "2.11.0",
    "build_type" : "deb",
    "build_hash" : "6b1986e964d440be9137eba1413015c31c5a7752",
    "build_date" : "2023-11-29T21:43:44.221253956Z",
    "build_snapshot" : false,
    "lucene_version" : "9.7.0",
    "minimum_wire_compatibility_version" : "7.10.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```
