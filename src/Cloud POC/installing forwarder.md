# Installing Graylog Forwarder

---
🗒️ **NOTE**

The following steps must be completed on a linux server. This can be a virtual server but it must be linux.

---

## Introduction

This page will provide instructions for how to install the Graylog Forwarder. For more information about the Graylog Forwarder, see [Forwarder](https://go2docs.graylog.org/5-0/getting_in_log_data/forwarder.html) via our docs site.

## Prerequisites

* A linux server
    * These instructions will cover installing on Ubuntu Server 20.04 LTS
* The ability to "ssh" into the server

## Install

Housekeeping:

The code block below can be copy/pasted into a terminal.

```
sudo apt update && sudo apt upgrade

```

Prereqs:

The code block below can be copy/pasted into a terminal.

```
sudo apt-get install -y openjdk-17-jdk-headless

```

Download and Install:

The code block below can be copy/pasted into a terminal.

```
wget https://packages.graylog2.org/repo/packages/graylog-forwarder-repository_5-1_all.deb
sudo dpkg -i graylog-forwarder-repository_5-1_all.deb
sudo apt-get update
sudo apt-get install -y graylog-forwarder

```

Allow Graylog Inputs to bind to ports <1024:

```
sudo sed -i '/^LimitNOFILE=64000.*/a AmbientCapabilities=CAP_NET_BIND_SERVICE' /usr/lib/systemd/system/graylog-forwarder.service
sudo systemctl daemon-reload

```

## Setup Forwarder via Graylog Cloud

1. Login to your Graylog Cloud instance
2. Using the top menu, Click **System** and then click **Forarders**<br>![image](img/navigate-to-forwarder-page.png)
3. Click "**Get Started!**"<br>![image](img/getting-started.png)

### Complete 'New Forwarder' steps
1. **Start Configuration**<br>![image](img/start-config.png)
2. **Start new Forwarder**<br>![image](img/start-new-forwarder.png)
3. Because we've completed the 'Install Forwarder' step in the above section, click **Continue**<br>![image](img/install-forwarder.png)
4. Input token name: `forwarder`, then click **Create Token**<br>![image](img/create-token.png)
5. Make note of the `forwarder_server_hostname` and `forwarder_grpc_api_token` values. You will need these for the next step.<br>![image](img/note-forwarder-hostname-token.png)
6. Using SSH edit the Graylog Forwarder configuration file to add the snippet we copied in the above step:
    1. SSH to your linux server where you install the Graylog Forwarder
    2. Copy and paste the commands below into the SSH session where you have the Graylog Forwarder installed. These commands will ask for input and the write the values to the Graylog Forwarder configuration file.

For `forwarder_server_hostname`. After copy/pasting this command, you can copy the value for `forwarder_server_hostname` from the forwarder setup page.

```shell
echo -ne "Enter Graylog forwarder_server_hostname: " && tmp=$(head -1 </dev/stdin) && sudo sed -i "s/^forwarder_server_hostname.*/forwarder_server_hostname = $tmp/g" /etc/graylog/forwarder/forwarder.conf

```

For `forwarder_grpc_api_token`. After copy/pasting this command, you can copy the value for `forwarder_grpc_api_token` from the forwarder setup page.

```shell
echo -ne "Enter Graylog forwarder_grpc_api_token: " && tmp=$(head -1 </dev/stdin) && sudo sed -i "s/^forwarder_grpc_api_token.*/forwarder_grpc_api_token = $tmp/g" /etc/graylog/forwarder/forwarder.conf

```

7. Using the SSH, start and enable the Graylog Forwarder:

```
sudo systemctl enable graylog-forwarder
sudo systemctl start graylog-forwarder

```

8. Back to the Graylog Console where we were setting up the new forwarder, Click **Continue**<br>![image](img/start-new-fwd-continue.png)
9. You should see the forwarder appear beneath the 'Select Forwarder' section. Click the forwarder to select it and then click **Configure selected Fowarder**<br>![image](img/select-fwd.png)
10. Click 'Add Forwarder Inputs'<br>![image](img/add-fwd-inputs.png)
11. Click **Create Input Profile**<br>![image](img/create-input-profile.png)
12. Input title "Forwarder" and click **Add Inputs**<br>![image](img/add-inputs.png)

### Add Syslog UDP Input

Before continuing, use SSH to add the following IP Tables rules to the linux server where the Graylog Forwarder is installed.

Back to the Graylog Cloud webpage:

1. Input Type: Syslog UDP
2. Title: Syslog UDP
3. Bind Address: (leave default) 0.0.0.0
4. Port: 514
5. **Create Input**

### Add Syslog TCP Input

1. **Add another input**<br>![image](img/add-another-input1.png)
2. Input Type: Syslog TCP
3. Title: Syslog TCP
4. Bind Address: (leave default) 0.0.0.0
5. Port: 514
6. **Create Input**

### Add Beats Input

1. **Add another input**<br>![image](img/add-another-input2.png)
2. Input Type: Beats
3. Title: Beats
4. Bind Address: (leave default) 0.0.0.0
5. Port: (leave default) 5044
6. **Create Input**

### Save Inputs and complete New Forwarder setup

1. Click **Save Inputs**<br>![image](img/save-inputs-final.png)
2. Click **Exit configuration**<br>![image](img/exit-config.png)

## Conclusion

Congrats, You now have a functional Graylog Forwarder!

Your forwarder should appear on the System/Forwarders page and should show 'Status: Connected'<br>![image](img/status-connected.png)

You can now configure log sources to send log messages to this forwarder using the inputs you created in the above steps. All logs sent to the Graylog Forwarder will be encrypted with TLS encryption and sent to your Graylog Cloud instance for processing and ingestion.
