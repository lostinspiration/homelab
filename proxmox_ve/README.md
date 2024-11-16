# Resources
[Reference](https://forum.proxmox.com/threads/how-do-i-find-the-dhcp-ip-address-for-a-container.125330/)
```shell
lxc-info -i -n <container id>
```

# Add mountpoint to Host Directory
```shell
vi /etc/pve/nodes/shinryuu/lxc/{ContainerId}.conf
```

MountId: HostPath,GuestPath,Opts
```text
mp0: /mnt/data/sql,mp=/mnt/sql,shared=1
```
