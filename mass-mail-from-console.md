```bash
#!/bin/bash
###########
# Author: Ildar Khusyainov
# 
# Description: Mass mail send script. You need change count "i<=10" then wait  in recipient mailbox 
###########
for  (( i=1; i<=10; i++ ))
	do
		echo "Test message" | mail -s "Test message" -r sender@domain.ru recipienr@domain.ru
	done
  ```
