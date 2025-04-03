# Mailpit Docker Container
## Setup Volume
- Click `Volumes` in the navigation menu
- Click `Add Volume` and name it `mailpit`
- Select the appropriate settings for your needs and `Create the volume`

## Install Mailpit
- Click `Add Container`
- Use the `axllent/mailpit` image from docker.io
- Add the ports `1025:1025` and `8025:8025` to the mappings
- Make the following changes under `Advanced container settings`
  - Assign the `mailpit` volume to `/data`
  - Set the `Restart policy` to `Unless Stopped`
  - Add a new environment variable for persistance `MP_DATABASE` with the value `/data/mail.db`

# HAProxy Setup
Modify your config with the following settings
```text
backend {backend_name} 
    mode http
    server mailpit {ip}:{port}
```
