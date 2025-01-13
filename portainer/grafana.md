# Grafana Docker Container
## Setup Volume
- Click `Volumes` in the navigation menu
- Click `Add Volume` and name it `grafana`
- Select the appropriate settings for your needs and `Create the volume`

## Install Grafana
- Click `Add Container`
- Use the `grafana/grafana-oss` image from docker.io
- Add the ports `3000:3000` to the mappings
- Make the following changes under `Advanced container settings`
  - Assign the `grafana` volume to `/var/lib/grafana`
  - Set the `Restart policy` to `Always`
  - Add the following Environment Variables  
    [Configuration Options](https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana)  
    ```text
    GF_SMTP_ENABLED: true
    GF_SMTP_HOST: {smtp host}
    ```

# HAProxy Setup
Modify your config with the following settings
```text
backend {backend_name} 
    mode http
    server grafana {ip}:{port}
```
