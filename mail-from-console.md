# mail-from-console

**First method**

Sending mail from console with attachment pictures in the body of the letter

Create a pic.html file in the home directory with a content where picture.jpg is a image file
```html
<html>
<head></head>
<body>
  <img src="cid:picture.jpg"/>
</body>
</html>
```


Next, run from the console command, where "recipient@mailserver.com" recipient
```bash
mutt -e "set content_type=text/html" recipient@mailserver.com -s "Subject" -a picture.jpg  < pic.html
```


**Second method**


```bash
sendmail -t <<EOT
TO: recipient@mailserver.com
FROM: sender@mailserver.com
SUBJECT: Test picture mail 
MIME-Version: 1.0
Content-Type: multipart/related;boundary="TESTMAIL"
--TESTMAIL
Content-Type: text/html; charset=ISO-8859-15
Content-Transfer-Encoding: 7bit
<html>
<head>
<meta http-equiv="content-type" content="text/html; charset=ISO-8859-15">
</head>
<body>
<img src="cid:image" alt=""><br/>
</body>
</html>
--TESTMAIL
Content-Type: image/jpeg;name="picture.jpg"
Content-Transfer-Encoding: base64
Content-ID: <image>
Content-Disposition: inline; filename="picture.jpg"
--TESTMAIL--
EOT
```


**Third method**

```bash
sendmail -t <<EOT
TO: recipient@mailserver.com
FROM: sender@mailserver.com
SUBJECT: Test picture mail 
MIME-Version: 1.0
Content-Type: multipart/related;boundary="TESTMAIL"
--TESTMAIL
Content-Type: text/html; charset=ISO-8859-15
Content-Transfer-Encoding: 7bit
<html>
<head>
<meta http-equiv="content-type" content="text/html; charset=ISO-8859-15">
</head>
<body>
<img src="data:image/jpeg;base64,iVBORw0KGg.......CYII==" alt=""><br/>
</body>
</html>
--TESTMAIL
Content-Type: image/jpeg;name="picture.jpg"
Content-Transfer-Encoding: base64
Content-ID: <image>
Content-Disposition: inline; filename="picture.jpg"
--TESTMAIL--
EOT
```

**fourth method**
```bash
#!/bin/bash

(
echo "To: recipient@mailserver.com"
echo "Subject: Test Status"
echo "Content-Type: text/html"
echo
echo "<html>
<head>
<title>Test Status</title>
<style>

</style>
</head>
<body>
</body></html>"
echo
) | /usr/sbin/sendmail -t
```

**Fifth method**
```bash
(   echo "Subject: Log Test ${RANDOM}"
    echo "Mime-Version: 1.0"
    echo "Content-Type: multipart/mixed; boundary=\"d29a0c638b540b23e9a29a3a9aebc900aeeb6a82\""
    echo "Content-Transfer-Encoding: 7bit"
    echo ""
    echo "--d29a0c638b540b23e9a29a3a9aebc900aeeb6a82"
    echo "Content-Type: text/html; charset=\"UTF-8\""
    echo "Content-Transfer-Encoding: 7bit"
    echo "Content-Disposition: inline"
    echo ""
    echo "HELLO"
    echo ""
    echo "--d29a0c638b540b23e9a29a3a9aebc900aeeb6a82"
    echo "Content-Type: text/plain"
    echo "Content-Transfer-Encoding: base64"
    echo "Content-Disposition: attachment; filename=\"log.txt\""
    echo ""
    base64 "${__LOG}"
    echo "--d29a0c638b540b23e9a29a3a9aebc900aeeb6a82--"

  ) | sendmail root
  ```
