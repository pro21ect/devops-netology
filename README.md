```
1. Фильм детсва в новом прочтении))
```
```
2. Диапазону ISM (2,4) соответствует 13 каналов, но непересекающимися являются только 3 - 1-ый, 6-ой и 11.
Для нелицензируемой работы полосы частот 5 ГГц называются UNII (Unlicensed National Information Infrastructure, Нелицензируемая национальная информационная инфраструктура)
Известны UNII-1,2,3,UNII-2 Extended,ISM. В России из-за ограничений законодательства разрешены 17 непересекающихся каналов для гражданского использования.
```
```
3. Organizationally Unique Identifier или Уникальный идетификатор организаций присваивается регистрационной администрацией IEEE.
Указанный mac принадлежит Apple, Inc.
```
```
4. payload — полезная нагрузка (без заголовков)
Расчитывается по формуле Max TCP Payload= (MTU–TCP–IP) / (MTU+Ethernet+IFG) = (9001-32-20) / (9001+26+12) = чуть более 99%
```
```
5. Комбинация флага SYN и FIN, устанавливаемого в заголовке TCP, является недопустимой и относится к категории комбинации
недопустимого / ненормального флага, поскольку она требует как установления соединения (через SYN), так и прекращения соединения (через FIN).
```
```
6. Данная команда касается UDP порта. О чём говрит флаг -u.
time-wait - присуще tcp, такак это состояние когда порт отправил подтверждение о завершении соединения и ожидает ответа, чего udp не делает. Значит и в состояние такое не придёт.
unconn - состояние аналогичное tcp listenning, т.е порт открыт, но в силу особенностей протокола, будет отображаться как состояние не известно. Подключения на такой порт будут проходить.
```
```
7. Схема будет выглядеть так:
                   Client                 Server 
                 ESTABLESHED             ESTABLESHED
1.  FIN WAIT 1 (active close)   FIN->   CLOSE WAIT (passive close)
2.  FIN WAIT 2                  <-ACK   CLOSE WAIT (passive close)
3.  TIME WAIT                   <-FIN   LAST ACK
4.  TIME WAIT                   ACK->   CLOSED
                  CLOSED                CLOSED

```
```
8. Теоретическое кол-во соединений  2^16=65536.
Используются 0-65535, порт из всех бинарных единиц, т.е. 65536 не используется
Поэтому чило на 1 клиента 65535
Если клиентов больше одного, то в теории (кол-во клиентов) * (кол-во портов клиента (65536))  
```
```
9. Да, такая ситуация возможна. Возникает при активном подключении и отключении клиента к удаленной службе.
Предположу что большое число соединений time-wait 
имеет негативный характер. Поскольку оба IP и удаленный порт остаются неизменными,
то на каждое новое соединение выделяется новый локальный порт. Если клиент был активной
стороной завершения TCP-сессии, то это соединение будет заблокировано какое-то время в состоянии TIME_WAIT.
Если соединения устанавливаются быстрее чем порты выходят из карантина, то это в теории может быть причиной
ошибки в соединении.
Даже если приложения обращаются к разным службам, и ошибка не происходит, очередь TIME_WAIT будет расти,
используя системные ресурсы.

```
```
10. Проблема с фрагментацией пакетов например возникает в SIP телефонии. Если по каким-то причинам фрагмент 
не дойдёт, в случае TCP, вопрос решается повторной отправкой. В UPD пакет попросту будет потерян.
  
```
```
11. Мой выбор за TCP по причине гарантированной доставки пакетов.
Syslog начинал с UDP, в более свежих его реализациях присутсвует поддержка TCP/
Причины мне кажется очевидны.

```
```
12. Ответ:
ss state listening -t -p -s
Total: 131
TCP:   6 (estab 1, closed 0, orphaned 0, timewait 0)

Transport Total     IP        IPv6
RAW       1         0         1
UDP       4         3         1
TCP       6         4         2
INET      11        7         4
FRAG      0         0         0

Recv-Q  Send-Q   Local Address:Port       Peer Address:Port  Process                                                    
0       4096           0.0.0.0:sunrpc          0.0.0.0:*      users:(("rpcbind",pid=592,fd=4),("systemd",pid=1,fd=35))  
0       4096     127.0.0.53%lo:domain          0.0.0.0:*      users:(("systemd-resolve",pid=593,fd=13))                 
0       128            0.0.0.0:ssh             0.0.0.0:*      users:(("sshd",pid=1169,fd=3))                            
0       4096              [::]:sunrpc             [::]:*      users:(("rpcbind",pid=592,fd=6),("systemd",pid=1,fd=37))  
0       128               [::]:ssh                [::]:*      users:(("sshd",pid=1169,fd=4))
Как видно 6 TCP - 4 IPv4 и 2 IPv6
Процессы: systemd,sshd                 
```
```
13. 
1. tcpdump -A -c 1
где ключ -A и отвечает за вывод в тексте
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
15:51:52.683395 IP vagrant.ssh > _gateway.57392: Flags [P.], seq 3426544232:3426544332, ack 34123123, win 62780, length 100
E...~.@.@...
...
......0.<.h...sP..<....Q.+*..6..%....CIe".{.W.........m....
..5GH.*..+_-.~<r'k.....\......0..e>Q...-I.|?[.U...W.nO.U.).r.lZ
1 packet captured
34 packets received by filter
1 packet dropped by kernel
2. sudo tcpdump -x -c 1
-x - позволит увидеть данные в HEX формате
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
15:58:25.884633 IP vagrant.ssh > _gateway.57392: Flags [P.], seq 3426545316:3426545352, ack 34123203, win 62780, length 36
        0x0000:  4510 004c 7ecb 4000 4006 a3c0 0a00 020f
        0x0010:  0a00 0202 0016 e030 cc3c eea4 0208 adc3
        0x0020:  5018 f53c 184f 0000 f65f ff5c c60d 7dca
        0x0030:  c7b0 a0b0 b967 ad76 74ae 59d1 f1fd 81cd
        0x0040:  ebfa fc21 74d4 afa4 e1c6 1d81
1 packet captured
50 packets received by filter
18 packets dropped by kernel

```
```
14. 
```
```
15. Для тестирования архивирую созданный файл
gzip /tmp/new/test
Тестирую
root@vagrant:/# gzip -t /tmp/new/test.gz
root@vagrant:/# echo $?
0
```
```
16. Для переонса необходимо чтоб физические тома были в одной одной логической группе
Поэтому вначале удаляю логическу группу томов vg01
vgremove vg01
Вижу
Volume group "vg01" successfully removed
Затем добавляю том в vg02
vgextend vg02 /dev/md0
Вижу
Volume group "vg02" successfully extended
Затем перемещаю физические тома
pvmove /dev/md1 /dev/md0
Результат
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
│   └─vg02-lv_test1  253:2    0  100M  0 lvm   /tmp/new
└─sdb2                 8:18   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
│   └─vg02-lv_test1  253:2    0  100M  0 lvm   /tmp/new
└─sdc2                 8:34   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
Логический том vg02 переехал с одного рэйда на другой.
```
```
17. Помечаю один из дисков в рэйде 1 неисправным
mdadm --fail /dev/md0 /dev/sdb1
Проверяю
cat /proc/mdctat
Вижу
md0 : active raid1 sdc1[1] sdb1[0](F)
      2094080 blocks super 1.2 [2/1] [_U]
Пометка фэйл присутствует      
```
```
18. Для проверки Raid использую
dmesg|grep -i raid
Одна из строк гласит
[110330.695420] md/raid1:md0: Disk failure on sdb1, disabling device.
Диск в ауте
```
```
19. Проверяю целостность файла
root@vagrant:/tmp/new# gzip -t /tmp/new/test.gz
root@vagrant:/tmp/new# echo $?
0
Всё в порядке
```
```
20. Выполнено.
```