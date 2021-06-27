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
9. 
```