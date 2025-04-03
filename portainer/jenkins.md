# Jenkins Docker Container
## Setup Volume
- Click `Volumes` in the navigation menu
- Click `Add volume` and name it `jenkins_home`
- Select the appropriate settings for your needs and `Create the volume`

## Install Jenkins
- Click `Add Container`
- Use the `jenkins/jenkins:lts-jdk17` image from docker.io
- Add the ports `8080:8080` and `50000:50000` to the mappings
- Make the following changes under `Advanced container settings`
  - Assign the `jenkins_home` volume to `/var/jenkins_home`
  - Set the `Restart policy` to `Unless Stopped`

## Customizations
Quick and dirty customizations to install python and packages
```shell
apt-get install python3 python3-pip -y
pip install pymssql requests --break-system-packages
```

# HAProxy Setup
Modify your config with the following settings
```text
backend {backend_name} 
    mode http
    server jenkins {ip}:{port} check
    http-request set-header Host {ip}:{port}
    http-request set-header X-Forwarded-Host {ip}
    http-request set-header X-Forwarded-Port {port} 
    http-request add-header X-Forwarded-Proto https
```
