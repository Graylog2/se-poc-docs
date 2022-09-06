# Installing Graylog Server

---
🗒️ **NOTE**

The following steps are completed on the server/instance that will be used for MongoDB/Graylog. This instance will ONLY have MongoDB/Graylog installed.

---

## Introduction

This page will provide instructions for how to install Graylog.

## housekeeping

Assumes housekeeping steps from [Installing MongoDB](installing%20mongodb.md#housekeeping) have been completed.

## Install

The code block below can be copy/pasted into a terminal.

```sh
wget https://packages.graylog2.org/repo/packages/graylog-4.3-repository_latest.deb
sudo dpkg -i graylog-4.3-repository_latest.deb
sudo apt update && sudo apt install graylog-server graylog-enterprise-plugins graylog-integrations-plugins graylog-enterprise-integrations-plugins

```

## Configure

