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

