# Installing Graylog Sidecar

## Introduction

This page will provide instructions for how to install [Graylog Sidecar](https://docs.graylog.org/docs/sidecar).

Sidecar is used to collect logs from devices, and is installed on any endpoint you wish to collect logs from.

## Prerequisites

### API Token
Before you can use Sidecar, you must first generate an API token that Sidecar can use to securely communicate with your graylog cluster.

See https://docs.graylog.org/docs/sidecar#stepbystep-guide

### Beats Input

Make sure you have a beats input created and started before continuing.

## Download

Download the latest release via https://github.com/Graylog2/collector-sidecar/releases/latest .

For Microsoft Windows (Both Windows and Windows Server) download graylog_sidecar_installer_[version].exe.

For linux choose the install applicable to your distribution (e.g. deb based or rpm based)

## Install

Install Sidecar on any device(s) you want to collect logs from and forward to your graylog cluster.

Sidecar can be installed by executing the download file, such as the `.exe`. You will be prompted to input your Graylog API address and your API key.

Your graylog API will be the same URL as your graylog cluster, followed by `/api`.

For example: if your graylog server is at `192.168.0.2:9000`, your api url would be `http://192.168.0.2:9000/api`

---
🗒️ **NOTE**

Note in the above example we're using http instead of HTTPS. If you have configured graylog to use TLS, you can use HTTPS instead.

---

### Silent/Automated Install

You can also install Graylog Sidecar silently and/or an automated installation method, such as an Endpoint Management system that can distribute and install software remotely on endpoints.

Example install script:

NOTE: update `graylog_sidecar_installer.exe` with the name of the downloaded sidecar installer file.

NOTE: update the `SERVERURL` and `APITOKEN` arguments with the values applicable to your install and environment.

```
"%~dp0graylog_sidecar_installer.exe" /S -SERVERURL=https://GRAYLOGSERVER.DOMAIN.COM/api -APITOKEN=YOURAPITOKEN
"C:\Program Files\Graylog\sidecar\graylog-sidecar.exe" -service install
"C:\Program Files\Graylog\sidecar\graylog-sidecar.exe" -service start
```