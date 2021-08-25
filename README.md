```
1.
- Будет ошибка, т.к. разные типы переменных
- c=str(a)+b
- c=a+int(b)
```
```
2.  Мой вариант допиливания:
#!/usr/bin/env python3

import os
path='~/devops-netology'

bash = ["cd " +path, "git status"]
result_os = os.popen(' && '.join(bash)).read()
# Убираю строку is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prep = result.replace('\tmodified:   ', '')
        sep='/'
        out=path+sep+prep
        print(out)
#  Убираю строку  break
```
```
3. Решение:
!/usr/bin/env python3

import os
import sys

#Проверяем параметр входа, если такого нет испозью  текущую папку
path = os.getcwd()
if len(sys.argv)>=2:
  path = sys.argv[1]

bash_command = ["cd " +path, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prep = result.replace('\tmodified:   ', '')
        sep='/'
        out=path+sep+prep
        print(out)
```
```
4.
#!/usr/bin/env python3

import time
import sys
import socket

site = {"drive.google.com":"", "mail.google.com":"", "google.com":""}
while True:
    for url, ip in site.items():
        new_ip = socket.gethostbyname(url)
        if ip != "" and ip != new_ip:
            sys.stdout.write("[ERROR] " + url + " IP mismatch: " + ip + " " + new_ip + "\n")
        else:
            sys.stdout.write(url + "  " + new_ip + "\n")
        site[url] = new_ip
    time.sleep(30) 
``` 


