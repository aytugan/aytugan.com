# Usefull Linux commands and snippets

## Copy files betweeen servers

### Using nc (nectat)

Copy folder from one linux server to another. Recommended to use terminal window managers such as [byobu](https://www.byobu.org/) or [tmux](https://github.com/tmux/tmux/wiki) to prevent interruptions if your laptop get disconnected from the server's ssh session.

On target server

```
# Before transfer
iptables -A INPUT -p tcp --dport 12345 -j ACCEPT
cd targetdirectory
nc -l 12345 |tar xvf -

# After transfer
iptables -D INPUT -p tcp --dport 12345 -j ACCEPT
```

On source server

```
cd sourcedirectory
tar cvf - . | nc <destination_host> 12345
```

### Using rsync

Recommended to perform bulk sync using netcat first, and then sync the difference using rsynk. Required enabled ssh agent locally and configured ssh forwarding on the source server.

Run on source server

```
# Set remote server
SERVER="1.1.1.1"
PORT="22"
LOGIN="user"

# Set PATHS
SRCPATH="/tmp/data"
DSTPATH="/tmp/data"

# Set ssh parameters
export RSYNC_RSH="ssh -p $PORT -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null"

# Run rsync
rsync --rsync-path="sudo rsync" --recursive --compress --delete --verbose --progress $SRCPATH $LOGIN@$SERVER:$DSTPATH
```

There is nothing should be run on the target server

## Files

### Create tar archive using numeric ID of owner and groups

Useful to backup container's data folders

```bash
tar --numeric-owner --no-acls --no-selinux --no-xattrs --same-owner \
-cvzf file.tgz folder
```

## Security

### Secure free space wipe

```bash
apt -y install secure-delete
sfill -fllv /
```

### LUKS disk encryption

https://www.howtoforge.com/tutorial/how-to-encrypt-a-linux-partition-with-dm-crypt-luks/

```bash
# Create disk:
cryptsetup --verbose --cipher aes-xts-plain64 --key-size 512 --hash sha512 --iter-time 5000 --use-random luksFormat /dev/sda3

# Open disk (create device /dev/mapper/data2):
cryptsetup open --type luks /dev/sda1 data2
```

## SSH

### SSH port forward

```bash
ssh -L 192.168.0.10:8080:10.0.0.10:80 root@10.0.0.10
```

### Using TPM for ssh private key storage

<https://github.com/Foxboron/ssh-tpm-agent>
