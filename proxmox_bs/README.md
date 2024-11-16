# Storage
Edit the fstab and add the following configuration
```shell
nano /etc/fstab
```
```text
10.17.1.3:/volume1/pve-backups /mnt/synology/pve-backups nfs vers=3,nouser,atime,auto,retrans=2,rw,dev,exec 0 0
```
![fstab_nfs](./assets/fstab_nfs.png)

# Datastore Configuration
If you need to add an existing datastore, you can modify the following file using the syntax below.  
> This happened to me when the NFS share was not present and pbs created a physical folder filling the drive instead of the NFS.  
> I needed to remove it and re-add the NFS location after rebooting.
```shell
vi /etc/proxmox-backup/datastore.cfg
```

```text
datastore: synology
        path /mnt/synology/pve-backups
```
