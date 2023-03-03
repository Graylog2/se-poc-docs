# Migrating FROM Elasticsearch TO OpenSearch

---
🗒️ **NOTE**

This document assumes a single node Elasticsearch cluster. While the steps will be the same, there are additional configurations when upgrading a multi-node cluster.

This information is provided as best effort, and while it has been tested and verified to the best of our ability, there is still some risk involved. Its important to understand the risks of upgrading, such as the inability to start OpenSearch and data loss.

---

## Introduction

This page will provide instructions for how to migrate an existing Elasticsearch cluster, with data created and managed by Graylog, to OpenSearch.

## Prerequisites

* Elasticsearch should be version 7.10
    * Anecdotally, people have gotten upgrades to work from older versions of Elasticsearch (6.8)
    * The Elasticsearch version MUST NOT be newer than 7.10 (e.g. 7.11 and later are not supported)
* Elasticsearch Indices version 6082399 (v6.8 or greater)
* Graylog 4.3 (or later)
    * Graylog 4.2 and older do NOT support OpenSearch. Upgrading to OpenSearch will break Graylog 4.2 (and older).

## Planning

Determine if you will install OpenSearch on the same existing server where Elasticsearch is installed, OR if you will install OpenSearch on a new, separate server.

It is preferable to use a separate server.

## Install OpenSearch

See [Installing Opensearch](https://github.com/Graylog2/se-poc-docs/blob/main/src/On%20Prem%20POC/installing%20opensearch.md)

---
🗒️ **NOTE**

Install OpenSearch, but do not start the service. In order to manipulate the data folders for OpenSearch the service must be stopped.

---

To view the status of OpenSearch:

```
sudo systemctl start opensearch

```

If OpenSearch is active (running), stop it:

```
sudo systemctl stop opensearch

```

## Copy/Move Data From Elasticsearch data directory to OpenSearch data directory

* from the Elasticsearch data directory (the default is /var/lib/elasticsearch, but if in doubt the data path is specified in /etc/elasticsearch/elasticsearch.yml)
* to the OpenSearch data directory (/var/lib/opensearch)

Example command:

```
sudo rsync -avP /var/lib/elasticsearch/* /var/lib/opensearch/

```

## Change owner of data folder

```
sudo chown -R opensearch:opensearch /var/lib/opensearch

```

## Start OpenSearch

```
sudo systemctl start opensearch

```