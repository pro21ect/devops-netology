```
1. sparse file -  файл, в котором последовательности нулевых байтов[1] заменены на информацию об этих последовательностях (список дыр)
Если своими словами, то реальный размер файла будет равняться тому размеру в котором реально записана информация.
Такое применяется например в виртуальных дисках.
```
```
2. Жёсткая ссылка на объект не может иметь отличные  права доступа и владельца от файла на который она указывает. По сути это синоним или второе имя файла.
```
```
3. Проделано.
Проверить что всё норм можно командой lsblk
```
```
4. Для разбиения использовал интерактивную утилиту, позволившую пошагово провести все действия.
sudo fdisk /dev/sdb
В итоге получил
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0    2G  0 part
└─sdb2                 8:18   0  511M  0 part
```
```
5. Переношу таблицу разделов с sdb на sdc
sfdisk -d /dev/sdb | sfdisk /dev/sdc
Проверяю lsblk
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0    2G  0 part
└─sdb2                 8:18   0  511M  0 part
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0    2G  0 part
└─sdc2                 8:34   0  511M  0 part
```
```
6. Создаю Raid 1
mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
Проверяю
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdb2                 8:18   0  511M  0 part
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdc2                 8:34   0  511M  0 part
```
```
7. Создаю raid 0
mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2
Проверяю
sdb                    8:16   0  2.5G  0 disk
├─sdb1                 8:17   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdb2                 8:18   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
sdc                    8:32   0  2.5G  0 disk
├─sdc1                 8:33   0    2G  0 part
│ └─md0                9:0    0    2G  0 raid1
└─sdc2                 8:34   0  511M  0 part
  └─md1                9:1    0 1018M  0 raid0
```
```
8. Создаю PV
pvcreate /dev/md0 /dev/md1
Оцениваю результат
sudo pvdisplay
 "/dev/md0" is a new physical volume of "<2.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/md0
  VG Name
  PV Size               <2.00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               zrIe8R-mYfJ-ZbCl-337y-r8rB-F4Jo-edbPFq

  "/dev/md1" is a new physical volume of "1018.00 MiB"
  --- NEW Physical volume ---
  PV Name               /dev/md1
  VG Name
  PV Size               1018.00 MiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               z8UVPQ-6Z22-XVYa-ZR0x-E6Cz-Zl2I-YTA2mk
```
```
9. Создаю VG
vgcreate vg01 /dev/md0
vgcreate vg02 /dev/md1
Проверяю
vgdisplay
 --- Volume group ---
  VG Name               vg02
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               1016.00 MiB
  PE Size               4.00 MiB
  Total PE              254
  Alloc PE / Size       0 / 0
  Free  PE / Size       254 / 1016.00 MiB
  VG UUID               4XKtR1-aG03-qZxx-iW4b-IUJH-QBOd-A5bnaF

  --- Volume group ---
  VG Name               vg01
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <2.00 GiB
  PE Size               4.00 MiB
  Total PE              511
  Alloc PE / Size       0 / 0
  Free  PE / Size       511 / <2.00 GiB
  VG UUID               XtsliU-gf0j-wgpG-tyLS-DN8s-KSvu-Yw0XhX
```
```
10. Создаю  LV 100Мб на RAID0
lvcreate --name lv_test1 --size 100M vg02
Проверяю
lvdisplay
 --- Logical volume ---
  LV Path                /dev/vg02/lv_test1
  LV Name                lv_test1
  VG Name                vg02
  LV UUID                AOYoeA-uDNu-X0aF-XEfk-WTEa-weuU-LkAiXG
  LV Write Access        read/write
  LV Creation host, time vagrant, 2021-06-27 09:42:58 +0000
  LV Status              available
  # open                 0
  LV Size                100.00 MiB
  Current LE             25
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     4096
  Block device           253:2
```
```
11. Создаю ФС
mkfs.ext4 /dev/vg02/lv_test1
Вижу
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```
```
12. Создаю директорию /tmp/new и монтирую созданный логический том
mount /dev/vg02/lv_test1 /tmp/new
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
16. 
```