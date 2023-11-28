# script to list all remote repos

Since I had shell access to the remote machine I ended up writing a small fabric script that could be run from the terminal to list all remote repositories:

```python
#!/bin/python

from fabric.api import run, env
from fabric.colors import green 

env.hosts = ['<hostname>'];
env.user = '<user>';
env.password = '<password>';

def lr():
  "Lists all remote repositories"
  print(green("listing remote repos"))
  run('cd /var/git; ls -al')
```

I can now list all remote repositores by issuing the following command from the terminal:

```fab lr
