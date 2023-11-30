# bash or anything oneliners 

'''bash

 (sudo  du -a /var/log/ |sort -nr|head -n20 |awk '{print $NF}'|while read l ;do du -csh $l|grep -vi total;done ) 2> /dev/null

```
