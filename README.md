![img.png](banner.png)
# Задание финала номинация DevOps 2023

## Фабула
> В организации есть разработчик, который хочет диктовать свою линию. На его машине обнаружен код, который надо скомпилировать с учетом вносимых в него изменений. Простейшие действия показывают, что в систему был внесен ряд изменений и программа по прежнему компилируется без учета изменений. Найдите их и покажите, что все тайное становится явным.
>
> Убедитесь в том, что не смотря на внесенные изменения результат работы программы остался прежним.
## Задача
> Найти где упрямый разработчик вносит смуту и игнорирует изменения внесенные в исходный код. Скомпилировать программу с учетом внесенных изменений, указав в качестве имени c.out Составьте текстовый документ - сценарий: вам нужно указать цепь рассуждений, и изменений, которые необходимо внести в систему, чтобы код был скомпилированы верно.

## Решение
1. Поиск программы в дереве каталогов:
``` 
Work
| - DevSecOps
    |- .vs
    |- app.cpp
    |- LICENSE
    |- Math.cpp
    |- Math.h
```

2. Проверка наличия компилятора.
```shell
>> ubuntu $ which g++
/usr/local/bin/g++
```

3. Попытка собрать
```shell
>>> g++ -o c.out Math.cpp
```

4. Проверка работы собранной программы:
```shell
>>> ubuntu $ ls
LICENSE  Math.cpp  Math.h  app.cpp  c.out
>>> ubuntu $ ./c.out
Usage: program_name num1 operator(+,-,*,/) num2
>>> ubuntu $ ./c.out 1+3
Usage: program_name num1 operator(+,-,*,/) num2
>>> ubuntu $ ./c.out 1 + 5
6
>>> ubuntu $ ./c.out 1 - 5
6
>>> ubuntu $ ./c.out 1 * 5
terminate called after throwing an instance of 'std::invalid_argument'
  what():  stod
Aborted (core dumped)
>>> ubuntu $ ./c.out 10 / 5
2
>>> ubuntu $ ./c.out 10 * 5
terminate called after throwing an instance of 'std::invalid_argument'
  what():  stod
Aborted (core dumped)
ubuntu $ 
```
> Ошибка связана с символом `*` кидает std::invalid_argument, соответственно нужно починить.

5. Посмотрим версию либы
> https://stackoverflow.com/questions/10354636/how-do-you-find-what-version-of-libstdc-library-is-installed-on-your-linux-mac
```shell
ubuntu $ /sbin/ldconfig -p | grep stdc++
        libstdc++.so.6 (libc6,x86-64) => /lib/x86_64-linux-gnu/libstdc++.so.6
```

6. Попробуем обновить все зависимости
```shell
>>> ubuntu $ sudo apt update
Hit:1 http://security.ubuntu.com/ubuntu focal-security InRelease
Hit:2 http://archive.ubuntu.com/ubuntu focal InRelease
Hit:3 http://archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:4 http://archive.ubuntu.com/ubuntu focal-backports InRelease
Reading package lists... Done
Building dependency tree       
Reading state information... Done
116 packages can be upgraded. Run 'apt list --upgradable' to see them.
>>> ubuntu $ sudo apt install g++
Reading package lists... Done
Building dependency tree       
Reading state information... Done
g++ is already the newest version (4:9.3.0-1ubuntu2).
0 upgraded, 0 newly installed, 0 to remove and 116 not upgraded.
```

7. Попробуем найти изменение в файловойсистеме за последние 7 дней
```shell
>>> ubuntu $ find ./filesystem  -type f -mtime -7 ! -mtime -3
```

```shell
>>> ubuntu $ find /etc -type f -printf '%TY-%Tm-%Td %TT %p\n' | sort -r
2023-02-23 12:41:05.0602464550 /etc/systemd/resolved.conf
2023-02-23 12:41:04.9362731000 /etc/systemd/timesyncd.conf
2023-02-23 12:41:02.0929175580 /etc/killercoda/host
2023-02-23 12:40:28.5877413600 /etc/sysctl.conf
2023-02-23 12:39:40.3435959270 /etc/ld.so.cache
2023-02-23 12:39:40.2395773910 /etc/mailcap
2023-02-23 12:38:32.6695510490 /etc/apt/trusted.gpg
2023-02-23 12:38:32.2814044700 /etc/crictl.yaml
2023-02-23 12:38:32.2734014480 /etc/containerd/config.toml
2023-02-23 12:38:32.2493923820 /etc/sysctl.d/99-kubernetes-cri.conf
2023-02-23 12:38:32.2213818030 /etc/modules-load.d/containerd.conf
2023-02-23 12:38:30.7448281770 /etc/docker/daemon.json
2023-02-23 12:38:21.2414414620 /etc/network/fan
2023-02-23 12:38:21.0653820580 /etc/passwd
2023-02-23 12:38:21.0293699070 /etc/shadow
2023-02-23 12:38:21.0000000000 /etc/shadow-
2023-02-23 12:38:20.0810547960 /etc/docker/key.json
2023-02-23 12:38:20.0000000000 /etc/passwd-
2023-02-23 12:38:17.0800919630 /etc/gshadow
2023-02-23 12:38:17.0640869350 /etc/group
2023-02-23 12:37:15.3143788690 /etc/netplan/kc-config.yaml
2023-02-23 12:37:14.9660471260 /etc/hosts
2023-02-23 12:37:14.9660471260 /etc/hostname
2023-02-23 12:37:13.2444668530 /etc/apt/apt.conf.d/10cloudinit-disable
2023-02-23 12:37:10.1154747680 /etc/default/motd-news
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/tokenizer.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/sysvshm.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/sysvsem.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/sysvmsg.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/sockets.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/shmop.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/readline.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/posix.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/phar.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/pdo.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/opcache.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/json.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/iconv.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/gettext.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/ftp.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/fileinfo.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/ffi.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/exif.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/ctype.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/mods-available/calendar.ini
2023-01-10 15:37:44.0000000000 /etc/php/7.4/cli/php.ini
2022-12-02 14:05:28.0000000000 /etc/libnl-3/pktloc
<более старый вывод опущен>
```

8. Попробуем найти историю изменений, разработчиком
```
ubuntu $ git config --list --show-origin
file:.git/config        core.repositoryformatversion=0
file:.git/config        core.filemode=true
file:.git/config        core.bare=false
file:.git/config        core.logallrefupdates=true
file:.git/config        remote.origin.url=https://github.com/Sehktel/DevSecOps.git
file:.git/config        remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
file:.git/config        branch.main.remote=origin
file:.git/config        branch.main.merge=refs/heads/main
ubuntu $
```

## Вывод
Вероятно разработчки внедрил изменения libstdc++.so.6 который нужно починить root'м