# Installation
https://haproxy.debian.net

# Additional Configuration
- Create a directory to house additional config files
  ```shell
  mkdir /etc/haproxy/haproxy.d
  ```
- Modify the systemd service config to add the additional config directory parameter
  ```shell
  nano /lib/systemd/system/haproxy.service
  ```
  Add the following line `-f /etc/haproxy/haproxy.d` as shown below
  ![alterservice](./assets/alterservice.png)
- Add any additional configuration files to the `/etc/haproxy/haproxy.d` folder

