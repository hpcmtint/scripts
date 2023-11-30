# bash or anything oneliners 

```bash

### Find largest file and directories

 (sudo  du -a /var/log/ |sort -nr|head -n20 |awk '{print $NF}'|while read l ;do du -csh $l|grep -vi total;done ) 2> /dev/null

### Recursively search all files in current directory, printing file name, line number, and match line:

```
grep -rnw ./ -e '$search_for'
```

### Generate a random number between 1 and 10:

```
grep -m1 -ao '[0-9]' /dev/urandom | sed s/0/10/ | head -n1
```

### Copy a file/folder with visible progress and checksum confirmation:

```
rsync -avz --progress $from $to
```

### Send a packet over TCP or UDP on a specific port

```
echo "Message" > /dev/udp/$ip/$port
echo "Message" > /dev/tcp/$ip/$port
```

### List all zombie process ID’s:

```
ps aux | awk '{if ($8=="Z") { print $2 }}'
```

### Get the name and parent of those processes:

```
pstree -p -s `ps aux | awk '{if ($8=="Z") { print $2 }}'`
```

### Paste the current contents of the buffer:

```
xsel --clipboard --output
```

### Print each line of a script as it runs:

```
bash -x $script
```

### Repeat previous command with sudo privileges:

```
sudo !!
```

### Breakdown of init systems:
