```
1. Предположу что это связано с особенностью режимов DR или TUN. В отличии от режима NAT пакеты в данном режиме от клиента к серверу
 летят минуюя ipvs. У ipvs есть собственная таблица тайм-аутов, в которой согласно таймингу есть время ожидания ответа от клиента.
Отсюда вывод ipvsadm -Ln будут висеть ещё некоторое время.
```
```
2.  Создаю 5 машин:
boxes = {
    "client" => "10",
    "bal1" => "21",
    "bal2" => "22",
    "real1" => "31",
    "real2" => "32",
}

Vagrant.configure("2") do |config|
    config.vm.network "private_network", virtualbox__intnet: true, auto_config: false
    config.vm.box = "bento/ubuntu-20.04"

    boxes.each do |hostname, addr|
        config.vm.define hostname do |node|
            node.vm.hostname = hostname

            node.vm.provider "virtualbox" do |v|
                v.name = hostname + "(Vagrant)"
            end

            node.vm.provision "shell" do |s|
                s.inline = "hostname $1;" \
                "ip addr add $2 dev eth1;" \
                "ip link set dev eth1 up;" \
                "if [[ $1 == *\"client\"* ]]; then apt -y install arping; fi;" \
                "if [[ $1 == *\"bal\"* ]]; then apt -y install keepalived ipvsadm; fi;" \
                "if [[ $1 == *\"real\"* ]]; then apt -y install nginx; fi;"
                s.args = [hostname, "172.28.128.#{addr}/24"]
            end
        end
    end
end
Из них 1 клиент, два баланстровщика и 2 реала

б) Далее конфиг /etc/keepalived/keepalived.conf для bal1
vrrp_instance vrrp_1 {
    state MASTER
    interface eth1
    virtual_router_id 200
    priority 100
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass buka17
    }

    virtual_ipaddress {
        172.28.128.200/24 dev eth1 label eth1:200
    }
}

virtual_server 172.28.128.200 80 {
    delay_loop 10
    lb_algo rr
    lb_kind DR
    protocol TCP

    real_server 172.28.128.31 80 {
        weight 1
        TCP_CHECK {
            connect_timeout 10
            connect_port    80
        }
    }

    real_server 172.28.128.32 80 {
        weight 1
        TCP_CHECK {
            connect_timeout 10
            connect_port    80
        }
    }
в) Затем конфиг /etc/keepalived/keepalived.conf для bal2   
В нём изменяю параметры относительно представленного выше
state BACKUP
priority 50

г) Настройки на реалах для DR:
sudo iptables -t nat -A PREROUTING -p TCP -d 172.28.128.0/24 --dport 80 -j REDIRECT
затем
sudo sysctl -w net.ipv4.ip_forward=1

д) На балансировщиках выполняю
sudo systemctl start keepalived

е) Проверяю на клиенте:
for i in {1..50}; do curl -I -s http://172.28.128.200>/dev/null; done
На bal1 :
sudo ipvsadm -Ln --stats
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Conns InPkts OutPkts InBytes Outbytes
TCP  172.28.128.200:80               50    300       0   19950        0
  -> 172.28.128.31:80                25    150       0    9975        0
  -> 172.28.128.32:80                25    150       0    9975        0
Соединения видны

Затем останавливаю bal1
sudo systemctl stop keepalived

На клиенте ещё раз
for i in {1..50}; do curl -I -s http://172.28.128.200>/dev/null; done

Проверяю соединения на 2-ом балансировщике:
sudo ipvsadm -Ln --stats
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Conns InPkts OutPkts InBytes Outbytes
TCP  172.28.128.200:80               50    300       0   19950        0
  -> 172.28.128.31:80                25    150       0    9975        0
  -> 172.28.128.32:80                25    150       0    9975        0


```
```
3.  Из того что я понял из условий задачи я бы увеличил кол-во балансировщиков в 2-ое на каждом их хостов.
Потеря любого из них не привела бы к полной утилицации сети, т.к. оставшиеся 4 вполне справляются.
Итого 6 балансировщиков на 3-х хостах с 6 VIP

```

