# bash or anything oneliners 

### Find largest file and directories

```bash
 (sudo  du -a /var/log/ |sort -nr|head -n20 |awk '{print $NF}'|while read l ;do du -csh $l|grep -vi total;done ) 2> /dev/null
```

### Recursively search all files in current directory, printing file name, line number, and match line:

```bash
grep -rnw ./ -e '$search_for'
```

### Generate a random number between 1 and 10:

```bash
grep -m1 -ao '[0-9]' /dev/urandom | sed s/0/10/ | head -n1
```

### Copy a file/folder with visible progress and checksum confirmation:

```bash
rsync -avz --progress $from $to
```

### Send a packet over TCP or UDP on a specific port

```bash
echo "Message" > /dev/udp/$ip/$port
echo "Message" > /dev/tcp/$ip/$port
```

### List all zombie process ID’s:

```bash
ps aux | awk '{if ($8=="Z") { print $2 }}'
```

### Get the name and parent of those processes:

```bash
pstree -p -s `ps aux | awk '{if ($8=="Z") { print $2 }}'`
```

### Paste the current contents of the buffer:

```bash
xsel --clipboard --output
```

### Print each line of a script as it runs:

```bash
bash -x $script
```

### Repeat previous command with sudo privileges:

```bash
sudo !!
```

## 1\. Apply a command on files with different names

The shell `{}` operator is great for this. Here's an example with three directories enclosed in `{}`:

```
$ mkdir -p -v /home/josevnz/tmp/{dir1,anotherdir,similardir}
```

## 2\. Edit a file in place

Do you want to replace a string on one or more files without using an editor? Sure, `sed` to the rescue:

```
$ sed -i 's#ORIGINAL_VALLUE#NEW_VALUE#g' myfile1 myfile2
```

But wait, Perl lovers will tell you they can do the same:

```
$ perl -p -i -e 's#ORIGINAL#NEW_VALUE#' myfile1 myfile2
```

## 3\. Share a file quickly using a web server

Raise your hand if you haven't used this at least _once_ to share a directory quickly:

```
$ cd $mydir && python3 -m http.server 8888
```

## 4\. Find failures with journalctl

Sometimes things break. You can find the most recent errors using a combination of `journalctl`, along with the classic tools `sort` and `uniq`:

```
$ journalctl --no-pager --since today \
--grep 'fail|error|fatal' --output json|jq '._EXE' | \
sort | uniq -c | sort --numeric --reverse --key 1

898172 "/usr/bin/dockerd"
    752 "/usr/local/sbin/node_exporter"
     30 "/usr/bin/gnome-shell"
     26 "/usr/bin/cat"
     22 "/usr/libexec/gsd-media-keys"
[...]
```

In this case, it seems that the Docker daemon is unhappy.

_**\[ Download this eBook to [get ready for your Red Hat remote exam](https://www.redhat.com/en/resources/remote-exams-preparation-ebook?intcmp=701f20000012ngPAAQ). \]**_

## 5\. Make a backup via encrypted file transfer

Use `ssh` and `tar` to make secure backups. They go together like peanut butter and jelly:

```
$ tar --create --directory /home/josevnz/tmp/ --file - *| \
ssh raspberrypi "tar --directory /home/josevnz \
--verbose --list --file -"
```

You can add flavor to the backup job with compression and encryption—just like adding ingredients to your sandwich.

## 6\. Write instantaneous files

This is a great trick when you need to write multiline documents:

```
$ cat<<DOC>/my/new/file
Line1
Line2
A $VARIABLE
DOC
```

You can also just `cat > file`, and when you are done editing, just input the EOF character (**Ctrl+D**):

```
[josevnz@dmaf5 temp]$ cat > testfile
This is a test
multiple lines
and here we go
[josevnz@dmaf5 temp]$ 
```

## 7\. Search for a file, and include some extensions and exclude others

This example uses the `grep` way to search for specific files. It's pretty fast and easy to remember:

```
$ grep -R 'import' --include='*.java' --color MySourceCodeDir
```

Or you can try the `find` way (use `xargs` to handle a large number of matches properly):

```
$ find MySourceCodeDir/ -name '*.java' -type f -print| xargs \
grep --color 'import
```

Why `find`, you may ask? You can combine `find` with `-exec` to execute actions on your files first and then pass the results to the filter. The processing possibilities are endless here.

## 8\. Monitor memory without top or htop

This one is almost cheating. It repeats a command, such as `free`, every five seconds and highlights the differences:

```
$ watch -n 5 -d '/bin/free -m'
```

## 9\. Display disk partition sizes

Use `lsbk` (list block) and `jq` (to manipulate a JSON on the command line) to display partition information:

```
$ lsblk --json | jq -c '.blockdevices[]|[.name,.size]'
["loop0","55.5M"]
["loop1","156M"]
["loop2","32.3M"]
["zram0","4G"]
["nvme0n1","476.9G"]
```

## 10\. Quickly display a file type

The **What is** function is called with `wi`. It quickly tells you a file's type.

To check a single file:

```
$ function wi { test -n "$1" && stat --printf "%F\n" "$1"; }
```

To check multiple files:

```
$ function wi { test "$#" -gt 0 && stat --printf "%n: %F\n" "$@"; }
```

**NOTE**: Functions are superior and can do the same work as an alias.

## 11\. Display the size of an installed RPM

If you have an RPM-based system, sooner or later, you will format your queries. Here's an example:

```
$ rpm --queryformat='%12{SIZE} %{NAME}\n' \
-q java-11-openjdk-headless
```

**_\[ Train and test on our latest courses and exams from [Red Hat Training & Certification: Red Hat Enterprise Linux skills path](https://www.redhat.com/en/resources/red-hat-enterprise-linux-skills-path-infographic?intcmp=701f20000012ngPAAQ). \]_**

## 12\. Display the total size of a group of files

In this case, the `find` command acts as a filter, displays the size of each file in bytes, and finally, shows the total size:

```
$ t=0; for n in $(find ~/Documents -type f -name '*.py' -print | xargs \
stat --printf "%s "); do ((t+=n)); done; echo $t
```

Or, if you want a function (better), try this approach:

```
$ function size { t=0; test -d "$1" && for n in $(find $1 \
-type f -name '*.py' -print| \
xargs stat --printf "%s "); do ((t+=n)); done; echo $t; }

size $mydir
```

## 13\. Update all Git repositories on a directory

You already know how useful [Git](https://opensource.com/resources/what-is-git) is. Here's a trick to be more efficient with your updates:

```
$ for i in */.git; do cd $(dirname $i); git pull; cd ..; done
```

## 14\. Expose a web directory using containers

[Containers](https://www.redhat.com/en/topics/containers?intcmp=701f20000012ngPAAQ) are critical today. This one-liner exposes a directory via [Podman](https://www.redhat.com/sysadmin/search/node?keys=podman):

```
$ podman run --rm -v .:/usr/share/nginx/html:ro,Z \
-p 30080:80 -d nginx
```

## 15\. Check the weather

Use this function to find out whether you need a jacket today:

```
weather() { curl -s --connect-timeout 3 -m 5 http://wttr.in/$1; }
```

## 16\. Display the top 10 IP addresses hitting a webserver

Here's a task web admins may use frequently with Nginx (it may also work with Apache) to grab the top 10 internet protocol addresses hitting a webserver from the access log:

```
$ cat /var/log/nginx/access.log | cut -f 1 -d ' ' | sort | \
uniq -c | sort -hr | head -n 10
```

## 17\. Round floats in Bash with Python's help

You can do pretty cool stuff with Python, but this example just rounds numbers:

```
$ echo "22.67892" | python3 -c "print(f'{round(float(input()))}')"
23
```

## 18\. Run a mini calculator

This function defines a quick calculator on the command line with variable precision (the default is 2). It uses [bc](https://man7.org/linux/man-pages/man1/bc.1p.html). Create the function like this:

```
$ function qqbc() { echo "scale=${2:-2}; $1" | bc -l
```

Next, perform a quick calculation:

```
$ qqbc "2/3"
.66
```

In case you need additional precision, just define a second parameter:

```
$ qqbc "2/3" 4
.6666
```

This tool is called `qqbc` because it's an improvement on the old function `qbc`.

## 19\. Convert a CSV to JSON

This trick is a modification of [this popular recipe](https://wiki.python.org/moin/Powerful%20Python%20One-Liners) to convert CSV files to the JSON format:

```
$ python3 -c \
"import csv,json,sys;print(json.dumps(list(csv.reader(open(sys.argv[1])))))" \
covid19-vaccinations-town-age-grp.csv
```

## 20\. Install and run commands with Docker

If you have Docker installed and you want to run a command without installing a bunch of dependencies on your system (while doing a quick run), this may be all you need:

```
$ docker run --rm --interactive curlimages/curl curl \
--verbose --location --fail --silent --output - \
https://example.com
```

### Weather

```
weather(){ curl -s "wttr.in/$1?m1"} 
```

### Timer

A timer which counts till the specified number of seconds, and then rings a bell. Equivalent of ‘set timer x’ on google on terminal (without requiring internet connection).

```
function count() { 
  total=$1 
  for ((i=total; i>0; i--)); do sleep 1; printf "Time remaining $i secs \r"; done 
  echo -e "\a" 
} 
```

### Up
```
function up() { 
  times=$1 
  wh 
```
