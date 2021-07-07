```
1.  Ответ вычисляется так:
задержка * пропускную способность канала = 300*1Гбит/с = 36621.1 KByte
так получается потому что полученное произведение необходимо поделить на 8 дабы получить KByte 
```
```
2.  Я высчитал в 1000  раз. Завимость также есть от задержек.
Считается на основе формулы Матиса. 
```
```
3.  Для начала прикину накладные расходы одного кадра (Ethernet Header + Ethernet trailer + TCP + IP)
Итого: 14+4+20+20 =58 байт служебной информации. Если отнять от Ethrnet frame IP и TCP, то полезного объёма останется 6 и 1460 соответственно.
Получается такая схема:
            Минимально   Максимально
      общая  64          1518 
   полезная  6           1460
  служебная  58          58
 % полезной  9,37%       96,18%
 Вывод : размер фрейма влияет на передачу данных.
```
```
4. Будет выглядеть примерно так: 
а. Поиск IP по имени 
   - поиск в КЭШЕ соответсвия имени хоста/ также файле hosts ( по условиям задачи отсутствует)
   - Запрос ARP на ДНС, если в КЭШ ДНС IP нет, то рекурсивный запрос, который проходит по списку вышестоящих DNS-серверов.
   - Запрос на корневой ДНС  перенаправление  на top level domain -> .ru.
   - Запрос на top level domain .ru  перенаправление на netology.ru
   - запрос на netology.ru возврашает IP
б. При получении IP-адреса конечного сервера, берётся также данные
   об используемом порте из URL (80 порт для HTTP, 443 для HTTPS)
   Получение и обмен происходят по системе "3-х рукопожатий":
   - FIN WAIT 1 (active close)   FIN->   CLOSE WAIT (passive close)
   - FIN WAIT 2                  <-ACK   CLOSE WAIT (passive close)
   - TIME WAIT                   <-FIN   LAST ACK
   - TIME WAIT                   ACK->   CLOSED   
```
```
5. Для ответа исользую следующую команду
 dig +trace google.co.uk

; <<>> DiG 9.16.1-Ubuntu <<>> +trace google.co.uk
;; global options: +cmd
.                       6306    IN      NS      l.root-servers.net.
.                       6306    IN      NS      k.root-servers.net.
.                       6306    IN      NS      j.root-servers.net.
.                       6306    IN      NS      a.root-servers.net.
.                       6306    IN      NS      i.root-servers.net.
.                       6306    IN      NS      h.root-servers.net.
.                       6306    IN      NS      g.root-servers.net.
.                       6306    IN      NS      f.root-servers.net.
.                       6306    IN      NS      e.root-servers.net.
.                       6306    IN      NS      d.root-servers.net.
.                       6306    IN      NS      c.root-servers.net.
.                       6306    IN      NS      b.root-servers.net.
.                       6306    IN      NS      m.root-servers.net.
;; Received 262 bytes from 127.0.0.53#53(127.0.0.53) in 0 ms

uk.                     172800  IN      NS      nsa.nic.uk.
uk.                     172800  IN      NS      nsb.nic.uk.
uk.                     172800  IN      NS      nsc.nic.uk.
uk.                     172800  IN      NS      nsd.nic.uk.
uk.                     172800  IN      NS      dns1.nic.uk.
uk.                     172800  IN      NS      dns2.nic.uk.
uk.                     172800  IN      NS      dns3.nic.uk.
uk.                     172800  IN      NS      dns4.nic.uk.
uk.                     86400   IN      DS      43876 8 2 A107ED2AC1BD14D924173BC7E827A1153582072394F9272BA37E2353 BC659603
uk.                     86400   IN      RRSIG   DS 8 1 86400 20210720050000 20210707040000 26838 . mB/MYyJD+OA+3VVzlZpY4EfABSQP6Po2a2pXQYhAtl9ZMUvhGoRpIF9v g1vt3OB1grL/5wjih/lKfufDRjmvwUXPeD870foHmVxnooOQoDd+r7gV 9MpUo8rrS1+wx5DEU2AW5AQPkXpoq3JUM89r+i6iJHatnrr+HOWFbyLg IqXr+YO25dazhQ+UEFyjbVpfAiUzZOna6aRgOxmFfub+OG+4NHxbtH7v XsBgUPrA5JgXkyFYtigsH2NDjpfvNqly2lASoTpgqogvrpiPPw2UAwE9 s/MuFIEeWNAcxKT1zz6+BYXjXnRRnpVE7aVZqxJMWpkYnOnSHow9U78N dsTygw==
;; Received 796 bytes from 198.41.0.4#53(a.root-servers.net) in 71 ms

google.co.uk.           172800  IN      NS      ns1.google.com.
google.co.uk.           172800  IN      NS      ns2.google.com.
google.co.uk.           172800  IN      NS      ns3.google.com.
google.co.uk.           172800  IN      NS      ns4.google.com.
g9f1kiihm8m9vhjk7lrvetbqceogjiqp.co.uk. 10800 IN NSEC3 1 1 0 - G9F5O8Q1LBTUKBV4FRD3PU0HUIPAP422 NS SOA RRSIG DNSKEY NSEC3PARAM TYPE65534
g9f1kiihm8m9vhjk7lrvetbqceogjiqp.co.uk. 10800 IN RRSIG NSEC3 8 3 10800 20210811073752 20210707065809 33621 co.uk. QY+igz4QzQZs1Z0Ev4QKC5uDHOGavqgWrLxiiBQs996mWNFO9AfMVo88 ukEQh4u2LwAP//DcewI4u0pzBWNaV6NfYCl/HRNlkgCBTlMKnLvNbudw IU7fKTu+4UIyNtHSuonmRg5UxUsF2ronCq5P+AuuEpE8OuOwxyGTlcuq ERk=
6qek9gotc8rl190u20rppm84phaaco0t.co.uk. 10800 IN NSEC3 1 1 0 - 6QF16S089GRU386I3JOL1T2E5CV61060 NS DS RRSIG
6qek9gotc8rl190u20rppm84phaaco0t.co.uk. 10800 IN RRSIG NSEC3 8 3 10800 20210807201552 20210703193214 33621 co.uk. r1Qo4uR6up+H+Usb8BsG2bCpQzpbUCsz9zj5cISpIoPGJCZCHclOdNAr K5k8VPD3y3wZ/xPjWEsWwm9DYgLKlVHx+AtZbFO6gUy6BW7Ntk3We8i+ F1OnsCvSNqsxFKXjHPwDnRPyr/SkMEDuu+x4Xp2BIYl96sM17htv6kvw gKE=
;; Received 646 bytes from 43.230.48.1#53(dns4.nic.uk) in 83 ms

google.co.uk.           300     IN      A       64.233.165.94
;; Received 57 bytes from 216.239.34.10#53(ns2.google.com) in 87 ms
Сначала опрошены корневые сервера, затем переадресованы запросы на uk оттуда на ns2.google.com, где и был найден искомы адрес.  
```
```
6. В подсети /25 доступно 126 адресов. 
   Подсеть 255.248.0.0 доступно 524286 адресов.
```
```
7. Чем иеньше число после черты, тем больше доступно адресов.
В /23 в 2 раза адресов больше, чем /24 (256)

```
```
8.  Получится. Подсети будут иметь маску /15. Выглядеть будут так: 10.0.0.0/15 и далее.
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
14. Флаг IP будет отображаться в таком виде
 Flags: 0x40, Don't fragment
        0... .... = Reserved bit: Not set   - всегда 0, т.к. зарезервировано
        .1.. .... = Don't fragment: Set   - если 1 то не фрагментирован, при 0 наоборот
        ..0. .... = More fragments: Not set  - 0 означает что фрагмент последний, 1 - есть ещё фрагмент

Ethernet будет называться Ethernet II
Organizationally Unique Identifier можно увидеть например в таком виде  -  12:35:02 (52:54:00:12:35:02)
```
