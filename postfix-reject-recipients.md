# postfix-reject-recipients


```bash
sudo vim /etc/postfix/header_checks
```

```bash
/^To:*recipient1@somedomain.com/ REJECT 
/^To:*recipient2@somedomain.com/ REJECT 
/^To:.*recipient3@somedomain.com/ OK 
/^To:.*@yandex.ru/ REJECT 
/^To:.*gmail.com/ REJECT 
/^To:.*outlook.com/ REJECT 
```

```bash
sudo vim /etc/postfix/main.cf
```

Add line to file /etc/postfix/main.cf
```bash
header_checks = pcre:/etc/postfix/header_checks 
```
Restart postfix server
```bash
sudo systemctl restart postfix
```
