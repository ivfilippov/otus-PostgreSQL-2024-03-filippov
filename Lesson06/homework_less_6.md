# Физический уровень PostgreSQL  



**Cоздаем ВМ с Ubuntu 20.04/22.04**  

в облаке Яндекс с помощью  CLI

```
➜  otus-PostgreSQL-2024-03-filippov git:(lesson3) ✗ curl -sSL https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash

Downloading yc 0.123.0
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  115M  100  115M    0     0  39.4M      0  0:00:02  0:00:02 --:--:-- 39.4M
Yandex Cloud CLI 0.123.0 linux/amd64

yc PATH has been added to your '/home/adm-ivfilippov/.zshrc' profile
yc zsh completion has been added to your '/home/adm-ivfilippov/.zshrc' profile.
To complete installation, start a new shell (exec -l $SHELL) or type 'source "/home/adm-ivfilippov/.zshrc"' in the current one

yc init

Welcome! This command will take you through the configuration process.
Please go to https://oauth.yandex.ru/authorize?response_type=token&client_id=************************** in order to obtain OAuth token.

Please enter OAuth token:**************************
You have one cloud available: 'cloud-ivfilippovv' (id = **************************). It is going to be used by default.
Please choose folder to use:
 [1] default (id = b1gb3q44hkuclcoe2i82)
 [2] Create a new folder
Please enter your numeric choice: 1

Your current folder has been set to 'default' (id = b1gb3q44hkuclcoe2i82).
Do you want to configure a default Compute zone? [Y/n] Y
Which zone do you want to use as a profile default?

 [1] ru-central1-a
 [2] ru-central1-b
 [3] ru-central1-c
 [4] ru-central1-d
 [5] Don't set default zone
Please enter your numeric choice: 1

Your profile default Compute zone has been set to 'ru-central1-a'.
 yc vpc network create \
    --name otus-net \
    --description "otus-net" \
 yc vpc network list
+----------------------+----------+
|          ID          |   NAME   |
+----------------------+----------+
| enpbvcgb2k3vkbiab175 | default  |
| enpkg9m5a6ms5m82ge6d | otus-net |
+----------------------+----------+

There is a new yc version '0.124.0' available. Current version: '0.123.0'.
See release notes at https://cloud.yandex.ru/docs/cli/release-notes
You can install it by running the following command in your shell:
        $ yc components update

➜  ~ yc vpc subnet create \
    --name otus-subnet \
    --range 192.168.0.0/24 \
    --network-name otus-net \
    --description "otus-subnet" \
>
id: e9bmr9d6uhu8tkam14id
folder_id: b1gb3q44hkuclcoe2i82
created_at: "2024-05-12T14:07:33Z"
name: otus-subnet
description: otus-subnet
network_id: enpkg9m5a6ms5m82ge6d
zone_id: ru-central1-a
v4_cidr_blocks:
  - 192.168.0.0/24

yc vpc subnet list
+----------------------+-----------------------+----------------------+----------------+---------------+------------------+
|          ID          |         NAME          |      NETWORK ID      | ROUTE TABLE ID |     ZONE      |      RANGE       |
+----------------------+-----------------------+----------------------+----------------+---------------+------------------+
| b0c7h4fbormm4dare21d | default-ru-central1-c | enpbvcgb2k3vkbiab175 |                | ru-central1-c | [10.130.0.0/24]  |
| e2ldhssd6q22er4ovqv2 | default-ru-central1-b | enpbvcgb2k3vkbiab175 |                | ru-central1-b | [10.129.0.0/24]  |
| e9bmr9d6uhu8tkam14id | otus-subnet           | enpkg9m5a6ms5m82ge6d |                | ru-central1-a | [192.168.0.0/24] |
| e9bsf0raihothqkknog8 | default-ru-central1-a | enpbvcgb2k3vkbiab175 |                | ru-central1-a | [10.128.0.0/24]  |
| fl8dho657l8ar8t6iduv | default-ru-central1-d | enpbvcgb2k3vkbiab175 |                | ru-central1-d | [10.131.0.0/24]  |
+----------------------+-----------------------+----------------------+----------------+---------------+------------------+

ssh-keygen -t rsa -b 2048

 yc compute instance create \
    --name otus-vm \
    --hostname otus-vm \
    --cores 2 \
    --memory 4 \
    --create-boot-disk size=15G,type=network-hdd,image-folder-id=standard-images,image-family=ubuntu-2004-lts \
    --network-interface subnet-name=otus-subnet,nat-ip-version=ipv4 \
    --ssh-key ~/yc_k.pub
done (33s)
id: fhm3k1d9gl8qnpu5efa4
folder_id: b1gb3q44hkuclcoe2i82
created_at: "2024-05-12T14:25:28Z"
name: otus-vm
zone_id: ru-central1-a
platform_id: standard-v2
resources:
  memory: "4294967296"
  cores: "2"
  core_fraction: "100"
status: RUNNING
metadata_options:
  gce_http_endpoint: ENABLED
  aws_v1_http_endpoint: ENABLED
  gce_http_token: ENABLED
  aws_v1_http_token: DISABLED
boot_disk:
  mode: READ_WRITE
  device_name: fhmechj4vumd4dnf0q93
  auto_delete: true
  disk_id: fhmechj4vumd4dnf0q93
network_interfaces:
  - index: "0"
    mac_address: d0:0d:3a:05:a9:85
    subnet_id: e9bmr9d6uhu8tkam14id
    primary_v4_address:
      address: 192.168.0.14
      one_to_one_nat:
        address: 178.154.207.36
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: otus-vm.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

➜  ~ yc compute instances show otus-vm
id: fhm3k1d9gl8qnpu5efa4
folder_id: b1gb3q44hkuclcoe2i82
created_at: "2024-05-12T14:25:28Z"
name: otus-vm
zone_id: ru-central1-a
platform_id: standard-v2
resources:
  memory: "4294967296"
  cores: "2"
  core_fraction: "100"
status: RUNNING
metadata_options:
  gce_http_endpoint: ENABLED
  aws_v1_http_endpoint: ENABLED
  gce_http_token: ENABLED
  aws_v1_http_token: DISABLED
boot_disk:
  mode: READ_WRITE
  device_name: fhmechj4vumd4dnf0q93
  auto_delete: true
  disk_id: fhmechj4vumd4dnf0q93
network_interfaces:
  - index: "0"
    mac_address: d0:0d:3a:05:a9:85
    subnet_id: e9bmr9d6uhu8tkam14id
    primary_v4_address:
      address: 192.168.0.14
      one_to_one_nat:
        address: 178.154.207.36
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: otus-vm.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}
➜  ~ yc compute instances list
+----------------------+---------+---------------+---------+----------------+--------------+
|          ID          |  NAME   |    ZONE ID    | STATUS  |  EXTERNAL IP   | INTERNAL IP  |
+----------------------+---------+---------------+---------+----------------+--------------+
| fhmrvjontn0asit02dm2 | otus-vm | ru-central1-a | RUNNING | 178.154.205.62 | 192.168.0.26 |
+----------------------+---------+---------------+---------+----------------+--------------+
```
___


**Устанавливаем Postgresql**   


```
 sudo apt update && sudo apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt -y install postgresql-15
```  

**Проверяем кластер, подключаемся к нему. Добавим таблицу с парой строк**

```
yc-user@otus-vm:~$ sudo pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
yc-user@otus-vm:~$ sudo -u postgres psql
psql (15.7 (Ubuntu 15.7-1.pgdg20.04+1))
Type "help" for help.

postgres=#  create table test(c1 text);
CREATE TABLE
postgres=# insert into test values('1');
INSERT 0 1
postgres=#
```

___

**Добавим новый диск в облако**

```
➜  otus-PostgreSQL-2024-03-filippov git:(Lesson06) ✗ yc compute disk create \
  --name data \
  --size 10 \
  --description "otus_data"
done (10s)
id: fhmslth634nvk2q0meqs
folder_id: b1gb3q44hkuclcoe2i82
created_at: "2024-05-27T18:35:23Z"
name: data
description: otus_data
type_id: network-hdd
zone_id: ru-central1-a
size: "10737418240"
block_size: "4096"
status: READY
disk_placement_policy: {}
```

**Подключим диск к машине**  

```
c compute instance attach-disk otus-vm \
  --disk-name data \
  --mode rw \
  --auto-delete
done (10s)
id: fhmrvjontn0asit02dm2
folder_id: b1gb3q44hkuclcoe2i82
created_at: "2024-05-12T14:39:55Z"
name: otus-vm
zone_id: ru-central1-a
platform_id: standard-v2
resources:
  memory: "4294967296"
  cores: "2"
  core_fraction: "100"
status: RUNNING
metadata_options:
  gce_http_endpoint: ENABLED
  aws_v1_http_endpoint: ENABLED
  gce_http_token: ENABLED
  aws_v1_http_token: DISABLED
boot_disk:
  mode: READ_WRITE
  device_name: fhmpus7rllf9abjsvu6j
  auto_delete: true
  disk_id: fhmpus7rllf9abjsvu6j
secondary_disks:
  - mode: READ_WRITE
    device_name: fhmslth634nvk2q0meqs
    auto_delete: true
    disk_id: fhmslth634nvk2q0meqs
network_interfaces:
  - index: "0"
    mac_address: d0:0d:1b:fc:f1:7e
    subnet_id: e9bmr9d6uhu8tkam14id
    primary_v4_address:
      address: 192.168.0.26
      one_to_one_nat:
        address: 51.250.90.222
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: otus-vm.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}
```  

**Разметим диск, добавим файловую систему, и примонтируем его**  

```
 yc-user@otus-vm:~$ sudo apt-get install lvm2

yc-user@otus-vm:~$ sudo lsblk
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
vda    252:0    0  15G  0 disk
├─vda1 252:1    0   1M  0 part
└─vda2 252:2    0  15G  0 part /
vdb    252:16   0  10G  0 disk
yc-user@otus-vm:~$ sudo vgcreate otus-data /dev/vdb
  Physical volume "/dev/vdb" successfully created.
  Volume group "otus-data" successfully created
yc-user@otus-vm:~$ sudo lvcreate -l +100%FREE -n pg_db otus-data
  Logical volume "pg_db" created.
yc-user@otus-vm:~$ sudo mkfs.ext4 /dev/mapper/otus--data-pg_db
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 2620416 4k blocks and 655360 inodes
Filesystem UUID: 28c50265-98d4-4dad-b37b-8a511dbecb15
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

yc-user@otus-vm:~$ sudo mkdir /mnt/data
yc-user@otus-vm:~$ sudo echo "/dev/mapper/otus--data-pg_db /mnt/data ext4 defaults 0 0" | sudo tee -a /etc/fstab
/dev/mapper/otus--data-pg_db /mnt/data ext4 defaults 0 0
yc-user@otus-vm:~$ mount -a
mount: only root can use "--all" option
yc-user@otus-vm:~$ sudo mount -a
yc-user@otus-vm:~$ df -h
Filesystem                    Size  Used Avail Use% Mounted on
udev                          1.9G     0  1.9G   0% /dev
tmpfs                         392M  848K  392M   1% /run
/dev/vda2                      15G  4.8G  9.3G  35% /
tmpfs                         2.0G  1.1M  2.0G   1% /dev/shm
tmpfs                         5.0M     0  5.0M   0% /run/lock
tmpfs                         2.0G     0  2.0G   0% /sys/fs/cgroup
tmpfs                         392M     0  392M   0% /run/user/1000
/dev/mapper/otus--data-pg_db  9.8G   24K  9.3G   1% /mnt/data
```  
___

**Переносим базу на диск**

```
yc-user@otus-vm:~$ sudo -u postgres psql
psql (15.7 (Ubuntu 15.7-1.pgdg20.04+1))
Type "help" for help.

postgres=# show data_directory;
       data_directory
-----------------------------
 /var/lib/postgresql/15/main
(1 row)

postgres=# \q

yc-user@otus-vm:~$ sudo pg_ctlcluster 15 main stop
yc-user@otus-vm:~$ sudo -u postgres mv /var/lib/postgresql/15/main /mnt/data
```

**Пробуем запустить**

```
yc-user@otus-vm:~$ sudo -u postgres pg_ctlcluster 15 main start
Error: /var/lib/postgresql/15/main is not accessible or does not exist
```
Кластер не запускается, так как мы переместили (move) каталог с базой, и  postgres  его не видит.
Для запуска требуется изменить конфигурационный файл /etc/postgresql/15/main/postgresql.conf

```
yc-user@otus-vm:~$ sudo su
root@otus-vm:/home/yc-user# ls -l /mnt/data/15/main/
root@otus-vm:/home/yc-user# exit
c-user@otus-vm:~$ sudo vi /etc/postgresql/15/main/postgresql.conf

data_directory = '/mnt/data/15/main/'          # use data in another directory
:wq
```
**Проверяем работу кластера после изменения конфига**
```
yc-user@otus-vm:~$ sudo -u postgres pg_ctlcluster 15 main start
Warning: the cluster will not be running as a systemd service. Consider using systemctl:
  sudo systemctl start postgresql@15-main

yc-user@otus-vm:~$ pg_lsclusters
Ver Cluster Port Status Owner    Data directory     Log file
15  main    5432 online postgres /mnt/data/15/main/ /var/log/postgresql/postgresql-15-main.log
  ```

  **Проверяем таблицу**
  ```
  yc-user@otus-vm:~$ sudo -u postgres psql
psql (15.7 (Ubuntu 15.7-1.pgdg20.04+1))
Type "help" for help.

postgres=# select * from test;
 c1
----
 1
(1 row)

postgres=#
```

Все работает, так как кластер теперь знает где лежат данные на новом диске