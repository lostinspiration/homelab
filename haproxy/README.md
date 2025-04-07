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

# SSL Certificates
Certificates are stored by default in `/etc/ssl/private`

- Download certificate bundle and send to proxy server
  ```shell
  scp kichka.dev-ssl-bundle.zip root@10.17.1.7:~/kichka.dev-ssl-bundle.zip
  ```

- Unzip
  ```shell
  unzip kichka.dev-ssl-bundle.zip
  cd kichka.dev-ssl-bundle
  ```

- Combine the domain certificate and private key
  ```shell
  cat domain.cert.pem <(echo) private.key.pem | tee kichka.dev.pem
  ```

- Move to final location
  ```shell
  cp kichka.dev.pem /etc/ssl/private/kichka.dev.pem
  ```

- Restart haproxy
  ```shell
  systemctl restart haproxy
  ```

# Automating Certificate Updates
Updating certificates are a pain. Below is a quick script to help with that.

- Enable API access to registrar. `Porkbun` in my case, and create an api key.
- Save the api key and scret to a file named `secret.json`
- Save the following to a script named `update.sh`
```bash
#!/usr/bin/env bash
wget --output-document=cert.json --method=POST --body-file=secret.json https://api.porkbun.com/api/json/v3/ssl/retrieve/kichka.dev
cat cert.json | jq -j '.certificatechain + "\n" + .privatekey' > kichka.dev.pem
cp kichka.dev.pem /etc/ssl/private/kichka.dev.pem
systemctl restart haproxy
```
- Execute `chmod +x update.sh` to make it executable
- Schedule it however you wish
