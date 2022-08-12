# new-linux-server-setup
```
When you create a server, it has some limits espacially on mysql. so this is not efficient too much.
To fix it, make this setup and you can use maximum of you hardware.

FOR CENTOS 7
1- yum update
2- install vestacp (it is most efficient control panel.)
(
  1- curl -O http://vestacp.com/pub/vst-install.sh
  2- bash vst-install.sh
)
3-Delete user backup command from cron section on vestacp panel.
4-Edit /etc/my.cnf file with this code. replace completely
(

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
symbolic-links=0
skip-external-locking
key_buffer_size = 4G
max_allowed_packet = 2G
table_open_cache = 1000
sort_buffer_size = 1024M
net_buffer_length = 62K
read_buffer_size = 1024M
read_rnd_buffer_size = 1024M
myisam_sort_buffer_size = 4096M
innodb_buffer_pool_size = 42G
tmp_table_size = 8G
#innodb_use_native_aio = 0
innodb_file_per_table
max_connections=1000000000
max_user_connections=0
wait_timeout=31536000
interactive_timeout=500000000
long_query_time=99999999999

#slow_query_log=1
#slow_query_log_file=/var/log/mysql-slow-queries.log



[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

!includedir /etc/my.cnf.d

)
(
Also we made too high conf for mysql so we need more ram, to supply it we will create swap 100gb.
Create swap with google. Because it can changeable by OS. If you
dont create swap, your mysql will be crash according to our new confs.
)


5- Delete this file => /usr/share/httpd/noindex/index.html
6- Go to server section on vestacp panel, and change php.ini under httpd section, with 
(
make max_execution_time = -1;
make memory_limit = -1;
)
7- Now we are breaking mysql chains, it has too much limits
(

To modify the limits, do the following:

mkdir -p /etc/systemd/system/mariadb.service.d/

Inside that directory, create new file limits.conf and add the following to that file:

[Service]
  LimitNOFILE = 65535

finally reload systemd with:

systemctl daemon-reload
and restart mysqld to enable the change:

systemctl restart mariadb
Now validate that the change was successful by using the following query:

mysql> show variables like '%file%';
You should find a line like this:

| open_files_limit                      | 65535
That's it, this way your changes survive MySQL updates.

REBOOT


)


THATS ALL.
```
