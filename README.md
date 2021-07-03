```
2. Диапазону ISM (2,4) соответствует 13 каналов, но непересекающимися являются только 3 - 1-ый, 6-ой и 11.
Для нелицензируемой работы полосы частот 5 ГГц называются UNII (Unlicensed National Information Infrastructure, Нелицензируемая национальная информационная инфраструктура)
Известны UNII-1,2,3,UNII-2 Extended,ISM. В России из-за ограничений законодательства разрешены 17 непересекающихся каналов для гражданского использования.
```
```
3. 
```
```
4. 
```
```
5. 
```
```
6. 
```
```
7. 

```
```
8. 
 
```
```
9. 

```
```
10. 
  
```
```
11. 

```
```
12. 
```
```
13. Создаю тестовый файл test и копирую в логический том.
copy test /tmp/new
Проверяю
ll /tmp/new
drwxr-xr-x  3 root root  4096 Jun 27 11:04 ./
drwxrwxrwt 10 root root  4096 Jun 27 10:57 ../
drwx------  2 root root 16384 Jun 27 09:54 lost+found/
-rw-r--r--  1 root root     4 Jun 27 11:04 test
```
```
14. Вывод lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                    8:0    0   64G  0 disk
├─sda1                 8:1    0  512M  0 part  /boot/efi
├─sda2                 8:2    0    1K  0 part
└─sda5                 8:5    0 63.5G  0 part
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdb2                 8:18   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
    └─vg02-lv_test1  253:2    0  100M  0 lvm   /tmp/new
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdc2                 8:34   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
    └─vg02-lv_test1  253:2    0  100M  0 lvm   /tmp/new
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