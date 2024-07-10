## System Setup
- Create an NFS share on synology for storage
- Add NFS storage pool to proxmox pointed to the NFS share
- Passthrough GPU to VM
  - Make sure that the processor type is `x86-64-v3` or some type that supports `avx` and `avx512` otherwise
  ollama will disable gpu support. `x86-64-v2-AES` which is usually the default does not support these features
- Install & Setup Ubuntu Server 
- Assign a static IP address to the VM
- Install QEMU guest agent
    ```shell
    sudo apt install qemu-guest-agent
    ```
- Install Nvidia Drivers
    ```shell
    sudo ubuntu-drivers install
    ```
- Setup NFS stored disk
  - Find the disk
    ```shell
    lsblk
    ```
  - Create partition
    ```shell
    sudo fdisk /dev/sdb
    ```
    Whilw in `fsidk` use the following keystrokes  
    `n` > `p` > `defaults for the rest` > `p` to confirm settings > `w` to write
  - format disk
    ```shell
    sudo mkfs.ext4 /dev/sdb1
    ```
  - mount disk
    ```shell
    mkdir /mnt/llm-data
    ```
    ```shell
    sudo mount /dev/sdb1 /mnt/llm-data
    ```
    ```shell
    chown ollama:ollama /mnt/llm-data
    ```
    ```shell
    sudo nano /etc/fstab
    /dev/sdb1  /mnt/llm-data  ext4  defaults  0  1
    ```

## Ollama Installation
- Install Ollama `https://ollama.com/`
    ```shell
    curl -fsSL https://ollama.com/install.sh | sh
    ```
- Modify Ollama systemd configuration
    ```shell
    chown ollama:ollama /mnt/llm-data
    ```
    ```shell
    sudo systemctl edit ollama.service
    ```
    ```
    [Service]
    Environment="OLLAMA_HOST=0.0.0.0"
    Environment="OLLAMA_MODELS=/mnt/llm-data"
    ```
    ```shell
    sudo systemctl daemon-reload
    ```
    ```shell
    sudo systemctl restart ollama
    ```
- Install a model to use
    ```shell
    ollama pull llama3
    ```
- Test Ollama is using GPU
    ```shell
    watch -n 1 nvidia-smi
    ```
    ```shell
    ollama run llama3
    ```
- Install Docker
    ```shell
    # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```
    ```shell
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
- Install OpenWebUI
    ```shell
    sudo docker run -d --network=host -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 \
    --name open-webui --restart always ghcr.io/open-webui/open-webui:main
    ```
- Use OpenWebUI
    Open web ui can be loaded by going to `http://localhost:8080`

- Update OpenWebUI
    ```shell
    sudo docker pull ghcr.io/open-webui/open-webui:main
    docker stop open-webui
    docker rm open-webui
    ```
    Run the same `Install OpenWebUI` command from above


## Stable Diffusion Setup
- Install and Setup `pyenv`
    ```shell
    sudo apt install -y make build-essential libssl-dev zlib1g-dev \
    libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev \
    libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python3-openssl git
    ```
    ```shell
    curl https://pyenv.run | bash
    ```
    ```shell
    export PYENV_ROOT="$HOME/.pyenv"
    [[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
    eval "$(pyenv init -)"
    ```
    ```shell
    source .bashrc
    ```
- Install Python
    ```shell
    pyenv install 3.10
    ```
    ```shell
    pyenv global 3.10
    ```
- Install Automatic1111
    ```shell
    mkdir stablediff && cd stablediff
    ```
    ```shell
    wget -q https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/webui.sh
    ```
    ```shell
    chmod +x webui.sh
    ```
    ```shell
    ./webui.sh --listen --api
    ```
- Setup Automatic1111 as a service
File: `sudo vim /etc/systemd/system/automatic1111.service`
```text
[Unit]
Description=Stable Diffusion AUTOMATIC1111 Web UI service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=user
ExecStart=/usr/bin/env bash /home/user/stablediff/webui.sh --api --api-auth username:password
WorkingDirectory=/home/user/stablediff
StandardOutput=append:/var/log/sdwebui.log
StandardError=append:/var/log/sdwebui.log

[Install]
WantedBy=multi-user.target
```
Note: In my case the user account is simply "user" and path is '/home/user/stablediff'. Please adjust this based on your setup.

Add `export PYTHONUNBUFFERED="1"` to `/home/user/stablediff/stable-diffusion-webui/webui-user.sh`

Then create the `/var/log/sdwebui.log` file.
`sudo touch /var/log/sdwebui.log`

Finally enable the service.
`sudo systemctl enable automatic1111.service`

### Change Model Location
Go to models location, remove the existing `Stable-diffusion` folder, symlink to your new location.  
You can do this for all the other model types as needed
```shell
sudo mkdir /mnt/llm-data/sd
cd /home/user/stablediff/stable-diffusion-webui/models
rm -r Stable-diffusion
ln -s /mnt/llm-data/sd Stable-diffusion
sudo systemctl restart automatic1111
```
Unlink by
```shell
cd /home/user/stablediff/stable-diffusion-webui/models
unlink Stable-diffusion
```

### Get More Models


## Notes
#### Ports
- Ollama: 11434
- OpenWebUI: 8080
- Automatic1111: 7680

#### Ollama FAQ
[Ollama FAQ](https://github.com/ollama/ollama/blob/main/docs/faq.md)

#### Docker Volume Mounts
[Reference](https://docs.docker.com/storage/volumes/#create-and-manage-volumes)
List docker volumes
```shell
sudo docker volume ls
```

To get docker volume details and thus their mount points use the name of the volume with the `inspect` sub command
```shell
sudo docker volume inspect open-webui
```
