Цель домашнего задания
Научиться обновлять ядро в ОС Linux.
Описание домашнего задания
1) Запустить ВМ c Ubuntu.
2) Обновить ядро ОС на новейшую стабильную версию из mainline-репозитория.
3) Оформить отчет в README-файле в GitHub-репозитории
__________________________________________________________________________

Last login: Wed Jul 15 18:52:04 2026 from 172.22.22.23
18:54:08 deb-otus [root] [~] # uname -r
6.1.0-50-amd64
19:32:12 deb-otus [root] [~] # apt update
Hit:1 http://security.debian.org/debian-security bookworm-security InRelease
Hit:2 http://deb.debian.org/debian bookworm InRelease
Hit:3 http://deb.debian.org/debian bookworm-updates InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
19:34:26 deb-otus [root] [~] # wget -q https://pkgs.zabbly.com/key.asc -O /etc/apt/keyrings/zabbly.asc
19:34:40 deb-otus [root] [~] # ls -la /etc/apt/keyrings/
total 12
drwxr-xr-x 2 root root 4096 Jul 15 19:34 .
drwxr-xr-x 9 root root 4096 Jul 15 18:34 ..
-rw-r--r-- 1 root root 2460 Aug 18  2025 zabbly.asc
19:40:42 deb-otus [root] [~] # cat <<'EOF' | sudo tee /etc/apt/sources.list.d/zabbly-kernel-stable.sources
Enabled: yes
Types: deb
URIs: https://pkgs.zabbly.com/kernel/stable
Suites: bookworm
Components: main
Architectures: amd64
Signed-By: /etc/apt/keyrings/zabbly.asc
EOF
19:41:11 deb-otus [root] [~] # cat /etc/apt/sources.list.d/zabbly-kernel-stable.sources
Enabled: yes
Types: deb
URIs: https://pkgs.zabbly.com/kernel/stable
Suites: bookworm
Components: main
Architectures: amd64
Signed-By: /etc/apt/keyrings/zabbly.asc
19:41:34 deb-otus [root] [~] # apt update
Hit:1 http://security.debian.org/debian-security bookworm-security InRelease
Hit:2 http://deb.debian.org/debian bookworm InRelease
Hit:3 http://deb.debian.org/debian bookworm-updates InRelease
Get:4 https://pkgs.zabbly.com/kernel/stable bookworm InRelease [15.4 kB]
Get:5 https://pkgs.zabbly.com/kernel/stable bookworm/main amd64 Packages [5422 B]
Fetched 20.9 kB in 2s (13.8 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
19:41:41 deb-otus [root] [~] # apt install linux-zabbly
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  linux-headers-7.1.3-zabbly+ linux-image-7.1.3-zabbly+
The following NEW packages will be installed:
  linux-headers-7.1.3-zabbly+ linux-image-7.1.3-zabbly+ linux-zabbly
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 166 MB of archives.
After this operation, 701 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
19:42:57 deb-otus [root] [~] # dpkg -l | grep -E 'linux-image|linux-headers|zabbly'
ii  linux-headers-7.1.3-zabbly+  7.1.3-amd64-202607042033-debian12 amd64        Linux kernel headers for 7.1.3-zabbly+ on amd64
ii  linux-image-6.1.0-29-amd64   6.1.123-1                         amd64        Linux 6.1 for 64-bit PCs (signed)
ii  linux-image-6.1.0-50-amd64   6.1.176-1                         amd64        Linux 6.1 for 64-bit PCs (signed)
ii  linux-image-7.1.3-zabbly+    7.1.3-amd64-202607042033-debian12 amd64        Linux kernel, version 7.1.3-zabbly+
ii  linux-image-amd64            6.1.176-1                         amd64        Linux for 64-bit PCs (meta-package)
ii  linux-zabbly                 202607050703~amd64~debian12       amd64        Mainline Linux kernel by Zabbly
19:44:50 deb-otus [root] [~] # update-grub
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-7.1.3-zabbly+
Found initrd image: /boot/initrd.img-7.1.3-zabbly+
Found linux image: /boot/vmlinuz-6.1.0-50-amd64
Found initrd image: /boot/initrd.img-6.1.0-50-amd64
Found linux image: /boot/vmlinuz-6.1.0-29-amd64
Found initrd image: /boot/initrd.img-6.1.0-29-amd64
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
done
19:57:33 deb-otus [root] [~] # reboot
19:58:33 deb-otus [root] [~] # uname -r
7.1.3-zabbly+
