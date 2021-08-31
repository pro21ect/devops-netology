```
1. Ошибка отсутствие кавычек в 9-ой строке
{ "info" : "Sample JSON output from our service\t",
    "elements" :[
        { "name" : "first",
        "type" : "server",
        "ip" : 7175 
        },
        { "name" : "second",
        "type" : "proxy",
        "ip” : “71.78.22.43”
        }
    ]
}
```
```
2.  Мой вариант допиливания:
#!/usr/bin/env python3

import time
import sys
import json
import yaml
import socket

site = {"drive.google.com": "", "mail.google.com": "", "google.com": ""}
while True:
    exp_site = []
    for url, ip in site.items():
        ip_new = socket.gethostbyname(url)
        if ip != "" and ip != ip_new:
            sys.stdout.write("[ERROR] " + url + " IP mismatch: " + ip + " " + ip_new + "\n")
        else:
            sys.stdout.write(url + "  " + ip_new + "\n")
        site[url] = ip_new
        exp_site.append({url: ip_new})
    with open("ip_list.json", "w") as json_file:
        json_file.write(json.dumps(export_array, indent=2))
    with open("ip_list.yaml", "w") as yaml_file:
        yaml_file.write(yaml.dump(export_array, explicit_start=True, explicit_end=True))
    time.sleep(60)

```
```



