# AB-Logstash
This role installs following packages and configures them
- ntp
- monit
- elasticsearch 
- logstash

## Role parameters
### NTP
ntp is configured using the crontab which updates system time on midnight.

There is ```time.google.com``` time server is set by default you can give another time server by parameter ```ntp_server```:

```yml
- role: ab-logstash
  ntp_server: ntp.server.url
```

### ini file path
We can give id file and path and it's download path to local machine

```vmid_path``` path may be specified during vagrant image intalization

```vmid_fetch_dir```'s value is ```/tmp``` directory by default

```yml
- role: ab-logstash
  vmid_path: /path/to/id/file # this maybe configured during vagrant initalization
  vmid_fetch_dir: /path/to/fetch/file.ini 
```

### monit
monit is configured so, if there is any problem with elasticsearch and logstash, then restarts the servers

```yml
check process elasticsearch with pidfile /var/run/elasticsearch/elasticsearch.pid
  start program = "/etc/init.d/elasticsearch start"
  stop program  = "/etc/init.d/elasticsearch stop"
  if failed host 127.0.0.1 port 9200 protocol http
    and request "/_cluster/health"
    with timeout 10 seconds
    then restart
  if 3 restarts within 5 cycles then timeout

check process logstash with pidfile /var/run/logstash/logstash.pid
  start program = "/etc/init.d/logstash start"
  stop program  = "/etc/init.d/logstash stop"
  if failed host 127.0.0.1 port 9600 protocol tcp
    then restart
  if 3 restarts within 5 cycles then timeout
```