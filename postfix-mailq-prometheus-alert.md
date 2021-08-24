# Postfix mail queue alerting to prometheus

Need use node_exporter

```bash
sudo mkdir -p /var/lib/node_exporter/textfile_collector
sudo chown -R node_exporter: /var/lib/node_exporter
sudo vim /usr/local/sbin/postfix-mail-queue.sh
```
**postfix-mail-queue.sh**

```bash
#!/bin/bash
###########
# Author: Ildar Khusyainov
# 
# Description: This Script check count mail in postfix mail queue and report to prometheus
###########

PROMDIR="/var/lib/node_exporter/textfile_collector"
PROMFILE="${PROMDIR}/postfix-mail-queue."

TMP_FILE=$(mktemp)

trap "rm -f ${TMP_FILE}" EXIT KILL SIGTERM TERM SIGKILL

code=$(mailq | grep -c '^[0-9A-Z]')
>${PROMFILE}$$

echo -e "# HELP Postfix Mail Queue\n# TYPE postfix_mail_queue gauge\npostfix_mail_queue ${code}" >> ${PROMFILE}$$

mv ${PROMFILE}$$ ${PROMFILE}prom
```

Next we need create directory
```bash
sudo chmod 700 /usr/local/sbin/postfix-mail-queue.sh
sudo bash /usr/local/sbin/postfix-mail-queue.sh
sudo ls -l /var/lib/node_exporter/textfile_collector
-rw-r--r-- 1 root root  81 May 28 09:56 postfix-mail-queue.prom
```
Next we need look at file postfix-mail-queue.prom
```bash
sudo cat /var/lib/node_exporter/textfile_collector/postfix-mail-queue.prom
 
# HELP Postfix Mail Queue
# TYPE postfix_mail_queue gauge
postfix_mail_queue 677
```

Create crontab job
```bash
sudo vim /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
*/5    *       *       *       *       root    /usr/local/sbin/postfix-mail-queue.sh
```

Go to prometheus config directory

**prometheus.yml**
```yaml
scrape_configs:
  - job_name: 'node_exporter'
    file_sd_configs:
      - files:
        - /etc/prometheus/scrapers/node_exporter/*.yml
    relabel_configs:
      - source_labels:  [__address__]
        regex:  '([^:]*)(:\d+)?'
        replacement:  '$1'
        target_label: alias
 ```
 ```bash
sudo ls -l /etc/prometheus/scrapers/node_exporter
-rw-r--r-- 1 prometheus prometheus 146 Aug  5  2020 info.company.local.yml
```
**info.company.local.yml**
```yaml
- targets:
  - "info.company.local:9100"
  labels:
    "alias":  "info.company.local"
    "environment":  "dev"
```

**alert.rules.yml**
```yaml
  - alert: "Postfix mail queue"
    expr: postfix_mail_queue{job="node_exporter"} > 100
    for: 1m
    labels:
      severity: warning
      priority: P2
    annotations:
      summary: "Postfix mail queue is: {{ $value }}"
      description: "{{ $labels.alias }}"
```

Thats all
