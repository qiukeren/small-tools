#inotify monitor
#####stores inotify messages in mysql


```shell
#!/bin/bash
#HOSTNAME="127.0.0.1"
#Fuck localhost and 127.0.0.1
HOSTNAME="localhost"
PORT="3306"
USERNAME="inotify"
PASSWORD="*****************"
DBNAME="inotify_monitor"
TABLENAME="data"

echo 104857600 > /proc/sys/fs/inotify/max_user_watches
path="/home/www/bbs"

inotifywait -mrq --exclude "(sendmail.lock|.*cache_vanfon_geyan.*|.swp|.jpg|.jpeg|.gif|.png.rar|.tar.gz|.gz|.txt|.zip|.bak)" --timefmt '%Y/%m/%d %H:%M:%S' --format '%T %e %w%f' -e modify,delete,create,attrib,move $path |(while read date time method file ;do mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD} ${DBNAME} -e "insert into ${TABLENAME} values('','${date}','${time}','${method}','${file}') " ;done)

```


the corresponding SQL is

```SQL
CREATE TABLE IF NOT EXISTS `data` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `date` date NOT NULL,
  `time` time NOT NULL,
  `method` varchar(20) NOT NULL,
  `file` varchar(100) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=utf8 ;
```

what's more,

a crontab script in order of Duplicate removal are necessary.


