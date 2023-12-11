# Installing Opensearch

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for Opensearch. This instance will ONLY have opensearch installed.

---

## Introduction

This page will provide instructions for how to install and configure a single node OpenSearch cluster on RHEL 9 distros such as Rocky and Alma. While these instructions should work on any RHEL distro, they have only been tested and validated against Rocky 9 and Alma 9.

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

After update, continue with these commands:

```sh
# set timezone to UTC
sudo timedatectl set-timezone UTC

```

## Install

NOTE: For Rocky, you will need to install wget: `sudo yum install wget`

```sh
# Download OpenSearch repository (repo) file
sudo wget -O /etc/yum.repos.d/opensearch-2.x.repo https://artifacts.opensearch.org/releases/bundle/opensearch/2.x/opensearch-2.x.repo

```

For Alma 9 and Rocky 9 only:
```sh
# disable repo GPG check. This is required because the repo is signed with an outdated SHA-1 key which has been deprecated in RHEL9 linix distros.
sudo sed -i "s/^gpgcheck=.*/gpgcheck=0/g" /etc/yum.repos.d/opensearch-2.x.repo

```

For Alma 8 only:
```sh
# Import the public GNU Privacy Guard (GPG) key. This key verifies that your OpenSearch instance is signed. 
sudo rpm --import https://artifacts.opensearch.org/publickeys/opensearch.pgp
```

Continuing OpenSearch installation:

Verify that we can list the packages and accept any prompts
```sh
sudo yum list opensearch
```

Install
```sh
# Install the x64 package using yum.
sudo yum -y install opensearch.x86_64

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
plugins.security.disabled: true" | sudo tee /etc/opensearch/opensearch.yml

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
  "name" : "localhost",
  "cluster_name" : "graylog",
  "cluster_uuid" : "Lfir9JG5QkW2d9HSHzcGgw",
  "version" : {
    "distribution" : "opensearch",
    "number" : "2.10.0",
    "build_type" : "rpm",
    "build_hash" : "eee49cb340edc6c4d489bcd9324dda571fc8dc03",
    "build_date" : "2023-09-20T23:54:59.957975456Z",
    "build_snapshot" : false,
    "lucene_version" : "9.7.0",
    "minimum_wire_compatibility_version" : "7.10.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```
