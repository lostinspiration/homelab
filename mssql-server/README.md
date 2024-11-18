# Installation
[Reference (_SqlServer 2022 for Ubuntu 22.04_)](https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-linux-ver16&preserve-view=true&tabs=ubuntu2204)

## Create LXC container
- Create new container using `ubuntu-22.04-standard_22.04-1_amd64.tar.zst` image
  - Set memory to `2048`
  - Set `2` cpu cores
  - Set rootfs `16G`
  - Create data mount point sized for `64G` at `/mnt/data`
  - Set a static IP address

## Update Container
```shell
apt update && apt upgrade
```

## Get Dependencies
```shell
apt install -y curl software-properties-common 
```

## Install MS Sql Server
- Add Microsoft public keys
  ```shell
  curl https://packages.microsoft.com/keys/microsoft.asc | tee /etc/apt/trusted.gpg.d/microsoft.asc
  ```
- Add the Microsoft public repository
  ```shell
  curl -fsSL https://packages.microsoft.com/config/ubuntu/22.04/mssql-server-2022.list | tee /etc/apt/sources.list.d/mssql-server-2022.list
  ```
- Update source list and install `mssql-server`
  ```shell
  apt update
  apt install -y mssql-server
  ```
- Run setup, setting an SA password and selecting an edition
  ```shell
  /opt/mssql/bin/mssql-conf setup
  ```
  - Select `2` for developer edition
  - Accept license terms
  - Set system administrator password
- Confirm sqlserver is running
  ```shell
  systemctl status mssql-server --no-pager
  ```

## Grant Permissions
We must grant permissions for the user account and group that the `mssql-server` service is running under to be able to create
database and index files
```shell
chown -R mssql:mssql /mnt/data
```

## Enable Sql Agent
Sql Agent is disabled by default. We can enable it by issuing the following command and restarting the service.
```shell
/opt/mssql/bin/mssql-conf set sqlagent.enabled true
```

Restart sql server service
```shell
systemctl restart mssql-server.service
```

# Get SSMS
[Download](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16)
