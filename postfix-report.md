# postfix-report

**Insert in /etc/crontab**
```bash
59 23 * * * /bin/perl /usr/sbin/pflogsumm -d today /var/log/maillog > /var/log/mailstat_`date +%Y-%m-%d
```
