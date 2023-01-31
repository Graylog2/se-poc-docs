# Upgrading MongoDB

---
🗒️ **NOTE**

The following steps are completed on the server/instance where MongoDB is installed.

---

# Introduction

This page will provide instructions for how to upgrade MongoDB

# Upgrade Steps

## Upgrading from MongoDB 4.x to 5.x

This section assumes that we are starting with a server that has MongoDB 4.x installed.

If you are unsure what version of mongo is running, run the following command from the linux terminal:

```sh
mongo --eval "db.version()" --quiet

```

Import Public key for package management and create repository list file:

```sh
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list

```

reload the local package database: 

```sh
sudo apt-get update

```

Upgrade MongoDB packages:

```sh
rm -f out_apt_upg_mongo
apt list --upgradable | grep mongo > out_apt_upg_mongo
Lines=$(cat out_apt_upg_mongo)
concat=""
while IFS= read -r line; do
    pkgtoupg=$(echo "$line" | grep -oPo "^(.*?)\/" | grep -oPo "^([\w\-]+)")
    echo $pkgtoupg
    concat="$concat$pkgtoupg "
done <<< "$Lines"
sudo apt-get install -y $concat

```

Enable and Start Mondod service:

```sh
sudo systemctl daemon-reload
sudo systemctl enable mongod
sudo systemctl restart mongod

```

Confirm the upgrade has completed successfully:

```sh
mongosh --eval "db.version()" --quiet

```

This should output something like 5.0.x (where x is the number of the minor release)

Update MongoDB compatibility level to 5.0:

```sh
mongosh --eval 'db.adminCommand( { setFeatureCompatibilityVersion: "5.0" } )' --quiet

```

This should return: `{ ok: 1 }`.

Verify compatibility level:

```sh
mongosh --eval 'db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )' --quiet

```

This should return `{ version: '5.0' }, ok: 1 }`.