# Create a volume to house portainer data
```shell
docker volume create portainer_data
```

# Install portainer
```shell
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

# Install portainer agent
To be able to manage multiple docker installs you can use the portainer agent  
Go to `Environments` > `Add environment` > `Docker Standalone` > `Start Wizard` and follow the instructions from there
