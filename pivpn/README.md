# Prepare Raspberry PI
in bootfs create a file called `userconf` and put the following in it
```
pi:$6$c70VpvPsVNCG0YR5$l5vWWLsLko9Kj65gcQ8qvMkuOoRkEagI90qi3F/Y7rm8eNYZHW8CY6BOIKwMH7a3YYzZYL90zf304cAHLFaZE0
```
this will give you a user and password of `pi:raspberry`

run `passwd` to update your password

in bootfs create an empty file called `ssh`

in bootfs find `cmdline.txt` and add ip=10.17.1.1 to the end
```
ip=<YOUR_IP>::<GATEWAY>:<NETMASK>:<HOSTNAME>:<INTERFACE>:off
```

# Install piHole
```
curl -sSL https://install.pi-hole.net | bash
```

# Install piVPN
install pivpn
```
curl -L https://install.pivpn.io | bash
```

