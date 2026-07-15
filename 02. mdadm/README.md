Домашнее задание
Работа с mdadm
Цель:
Научиться использовать утилиту для управления программными RAID-массивами в Linux;
Описание/Пошаговая инструкция выполнения домашнего задания:
🎯 Задание
• Добавьте в виртуальную машину несколько дисков
• Соберите RAID-0/1/5/10 на выбор
• Сломайте и почините RAID
• Создайте GPT таблицу, пять разделов и смонтируйте их в системе.
__________________________________________________________________________________

21:28:32 deb-otus [root] [~] # lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0   30G  0 disk
|-sda1   8:1    0   29G  0 part /
|-sda2   8:2    0    1K  0 part
|-sda5   8:5    0  975M  0 part [SWAP]
sdb      8:16   0    1G  0 disk
sdc      8:32   0    1G  0 disk
sdd      8:48   0    1G  0 disk
sr0     11:0    1  632M  1 rom
21:28:40 deb-otus [root] [~] # mdadm --zero-superblock --force /dev/sd{b,c,d}
mdadm: Unrecognised md component device - /dev/sdb
mdadm: Unrecognised md component device - /dev/sdc
mdadm: Unrecognised md component device - /dev/sdd
21:28:44 deb-otus [root] [~] # mdadm --create --verbose /dev/md127 -l 1 -n 2 /dev/sd{b,c}
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md127 started.
21:30:58 deb-otus [root] [~] # cat /proc/mdstat
Personalities : [raid1]
md127 : active raid1 sdc[1] sdb[0]
1046528 blocks super 1.2 [2/2] [UU]
unused devices: <none>
21:31:15 deb-otus [root] [~] # mdadm -D /dev/md127
/dev/md127:
Version : 1.2
Creation Time : Wed Jul 15 21:30:58 2026
Raid Level : raid1
Array Size : 1046528 (1022.00 MiB 1071.64 MB)
Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
Raid Devices : 2
Total Devices : 2
Persistence : Superblock is persistent
Update Time : Wed Jul 15 21:31:03 2026
State : clean
Active Devices : 2
Working Devices : 2
Failed Devices : 0
Spare Devices : 0
Consistency Policy : resync
Name : deb-otus:127  (local to host deb-otus)
UUID : 4264a215:895ab1f9:a75b4764:9dae625f
Events : 17
Number   Major   Minor   RaidDevice State
0       8       16        0      active sync   /dev/sdb
1       8       32        1      active sync   /dev/sdc
21:32:13 deb-otus [root] [~] # lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINTS
sda       8:0    0   30G  0 disk
|-sda1    8:1    0   29G  0 part  /
|-sda2    8:2    0    1K  0 part
|-sda5    8:5    0  975M  0 part  [SWAP]
sdb       8:16   0    1G  0 disk
-md127   9:127  0 1022M  0 raid1
sdc       8:32   0    1G  0 disk
-md127   9:127  0 1022M  0 raid1
sdd       8:48   0    1G  0 disk
sr0      11:0    1  632M  1 rom
21:38:11 deb-otus [root] [~] # mdadm /dev/md127 --fail /dev/sdc
mdadm: set /dev/sdc faulty in /dev/md127
21:38:18 deb-otus [root] [~] # cat /proc/mdstat
Personalities : [raid1]
md127 : active raid1 sdc[1](F) sdb[0]
1046528 blocks super 1.2 [2/1] [U_]
unused devices: <none>
21:38:26 deb-otus [root] [~] # mdadm -D /dev/md127
/dev/md127:
Version : 1.2
Creation Time : Wed Jul 15 21:30:58 2026
Raid Level : raid1
Array Size : 1046528 (1022.00 MiB 1071.64 MB)
Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
Raid Devices : 2
Total Devices : 2
Persistence : Superblock is persistent
Update Time : Wed Jul 15 21:38:18 2026
State : clean, degraded
Active Devices : 1
Working Devices : 1
Failed Devices : 1
Spare Devices : 0
Consistency Policy : resync
Name : deb-otus:127  (local to host deb-otus)
UUID : 4264a215:895ab1f9:a75b4764:9dae625f
Events : 19
Number   Major   Minor   RaidDevice State
0       8       16        0      active sync   /dev/sdb
-       0        0        1      removed
1       8       32        -      faulty   /dev/sdc
21:40:31 deb-otus [root] [~] # mdadm /dev/md127 --remove /dev/sdc
mdadm: hot removed /dev/sdc from /dev/md127
21:40:38 deb-otus [root] [~] # mdadm /dev/md127 --add /dev/sdd
mdadm: added /dev/sdd
21:41:06 deb-otus [root] [~] # cat /proc/mdstat
Personalities : [raid1]
md127 : active raid1 sdd[2] sdb[0]
1046528 blocks super 1.2 [2/2] [UU]
unused devices: <none>
21:41:16 deb-otus [root] [~] # mdadm -D /dev/md127
/dev/md127:
Version : 1.2
Creation Time : Wed Jul 15 21:30:58 2026
Raid Level : raid1
Array Size : 1046528 (1022.00 MiB 1071.64 MB)
Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
Raid Devices : 2
Total Devices : 2
Persistence : Superblock is persistent
Update Time : Wed Jul 15 21:41:11 2026
State : clean
Active Devices : 2
Working Devices : 2
Failed Devices : 0
Spare Devices : 0
Consistency Policy : resync
Name : deb-otus:127  (local to host deb-otus)
UUID : 4264a215:895ab1f9:a75b4764:9dae625f
Events : 39
Number   Major   Minor   RaidDevice State
0       8       16        0      active sync   /dev/sdb
2       8       48        1      active sync   /dev/sdd
21:41:49 deb-otus [root] [~] # lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINTS
sda       8:0    0   30G  0 disk
|-sda1    8:1    0   29G  0 part  /
|-sda2    8:2    0    1K  0 part
-sda5    8:5    0  975M  0 part  [SWAP]
sdb       8:16   0    1G  0 disk
|-md127   9:127  0 1022M  0 raid1
sdc       8:32   0    1G  0 disk
sdd       8:48   0    1G  0 disk
|-md127   9:127  0 1022M  0 raid1
sr0      11:0    1  632M  1 rom
