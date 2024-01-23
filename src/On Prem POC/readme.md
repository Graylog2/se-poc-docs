These documents provide simple and concise instructions for installing Graylog and all required dependencies.

These instructions apply for Debian based distros such as Ubuntu. While these instructions should work on any debian distro, they have only been tested and validated against Ubuntu 22.

The documents should be followed in this order:

1. [Installing Opensearch](installing%20opensearch.md)
2. [Installing MongoDB](installing%20mongodb.md)
3. [Installing Graylog Server](installing%20graylog-server.md)

Server Recommendations:

| Server Role       | CPU Cores | RAM  |
| ----------------- | --------- | ---- |
| Graylog/MongoDB   | 8         | 16GB |
| OpenSearch        | 8         | 24GB |