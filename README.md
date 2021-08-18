```
1. Добавляю необходимую строку в vagrantfile
Далее ставлю Vault:
root@vagrant:~$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
root@vagrant:~$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
root@vagrant:~$ sudo apt-get update && sudo apt-get install vault
Всё установлено
```
```
2.  Заппускаю сервер в dev режиме
 vault server -dev -dev-listen-address="0.0.0.0:8200" &
 Проверяю запуск через браузер с хостовой машины на адрес localhost:8200
 
```
```
3. Создаю Root CA
root@vagrant:/home/vagrant# vault secrets enable pki
2021-08-18T17:52:00.510Z [INFO]  core: successful mount: namespace="" path=pki/ type=pki
Success! Enabled the pki secrets engine at: pki/
root@vagrant:/home/vagrant# vault secrets tune -max-lease-ttl=8760h pki
2021-08-18T17:54:39.863Z [INFO]  core: mount tuning of leases successful: path=pki/
Success! Tuned the secrets engine at: pki/
root@vagrant:/home/vagrant# vault write -field=certificate pki/root/generate/internal common_name="example.com" ttl=876
00h > CA_cert.crt
root@vagrant:/home/vagrant# vault write pki/config/urls issuing_certificates="$VAULT_ADDR/v1/pki/ca" crl_distribution_p
oints="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls

Создаю Intermediate CA
root@vagrant:~$ vault secrets enable -path=pki_int pki
2021-08-18T17:58:00.326Z [INFO]  core: successful mount: namespace= path=pki_int/ type=pki
Success! Enabled the pki secrets engine at: pki_int/
root@vagrant:~$ vault secrets tune -max-lease-ttl=43800h pki_int
2021-08-18T17:59:10.664Z [INFO]  core: mount tuning of leases successful: path=pki_int/
Success! Tuned the secrets engine at: pki_int/
root@vagrant:~$ sudo apt install jq
root@vagrant:~$ vault write -format=json pki_int/intermediate/generate/internal common_name="example.com Intermediate Authority" | jq -r '.data.csr' > pki_intermediate.csr
root@vagrant:~$ vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr format=pem_bundle ttl="43800h" | jq -r '.data.certificate' > intermediate.cert.pem
root@vagrant:~$ vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
Success! Data written to: pki_int/intermediate/set-signed
```
```
4. Подписываю Intermediate CA csr на сертификат для тестового домена netology.example.com
root@vagrant:/home/vagrant# vault write pki_int/roles/example-dot-com allowed_domains="example.com" allow_subdomains=tr
ue max_ttl="7200h"
Success! Data written to: pki_int/roles/example-dot-com
root@vagrant:/home/vagrant# vault write pki_int/issue/example-dot-com common_name="netology.example.com" ttl="2400h"
Получаю:
ca_chain            [-----BEGIN CERTIFICATE-----
certificate         -----BEGIN CERTIFICATE-----
issuing_ca          -----BEGIN CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----

Далее сохраняю полученные сертификаты в файлы.
```
```
5. Устанавливаю nginx
apt-get install nginx
sudo vim /etc/nginx/sites-enabled/default
Раскоменчиваю и добавляю:
listen 443 ssl default_server;
listen [::]:443 ssl default_server;
ssl_certificate /home/vagrant/netology.example.com.crt;
ssl_certificate_key /home/vagrant/netology.example.com.key;
Затем
nginx -t
Получаю
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
Перезапускаю сервис
root@vagrant:~$ systemctl reload nginx
```
```
6. Правлю /etc/hosts
vim /etc/hosts
Добавляю
127.0.0.1 localhost netology.example.com

root@vagrant:~# ln -s /home/vagrant/intermediate.ca.cert.crt /usr/share/ca-certificates/mozilla/intermediate.ca.cert.crt
root@vagrant:~# echo mozilla/intermediate.ca.cert.crt >> /etc/ca-certificates.conf
root@vagrant:~# update-ca-certificates
Updating certificates in /etc/ssl/certs...
1 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
Проверяю:
root@vagrant:~# curl -I -s https://netology.example.com |head -1
HTTP/1.1 200 OK
```
```
7. Протокол ACME применяется для организации взаимодействия удостоверяющего центра и web-сервера,
например, для автоматизации получения и обслуживания сертификатов. Запросы передаются в формате JSON поверх HTTPS.
Проект разработан некоммерческим удостоверяющим центром Let’s Encrypt, контролируемым сообществом и предоставляющим
сертификаты безвозмездно всем желающим.

К счастью домена без сертификата SSL у  меня нет. По некоторым соображениям компания не использует бесплатные сертификаты  
Однако мне доводилось ранее их устанавливать и сложностей не возникало
```

