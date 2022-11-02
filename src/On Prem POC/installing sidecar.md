# Installing Graylog Sidecar

## Introduction

This page will provide instructions for how to install [Graylog Sidecar](https://docs.graylog.org/docs/sidecar).

## Download

Download the latest release via https://github.com/Graylog2/collector-sidecar/releases/latest .

For Microsoft Windows (Both Windows and Windows Server) download graylog_sidecar_installer_[version].exe.

For linux choose the install applicable to your distribution (e.g. deb based or rpm based)

## Install

Graylog sidecar can be installed by executing the download file, such as the `.exe`. You will be prompted to input your Graylog API address and your API key.

Your graylog API will be the same URL as your graylog cluster, followed by `/api`.

For example: if your graylog server is at `192.168.0.2:9000`, your api url would be `http://192.168.0.2:9000/api`

---
🗒️ **NOTE**

Note in the above example we're using http instead of HTTPS. If you have configured graylog to use TLS, you can use HTTPS instead.

---

### Silent/Automated Install

You can also install Graylog Sidecar silently and/or an automated installation method, such as an Endpoint Management system that can distribute and install software remotely on endpoints.

Example install script:

Besure to update the `SERVERURL` and `APITOKEN` arguments with the values applicable to your install and environment.

```
"%~dp0graylog_sidecar_installer_1.1.0-1.exe" /S -SERVERURL=https://GRAYLOGSERVER.DOMAIN.COM/api -APITOKEN=YOURAPITOKEN
"C:\Program Files\Graylog\sidecar\graylog-sidecar.exe" -service install
"C:\Program Files\Graylog\sidecar\graylog-sidecar.exe" -service start
```