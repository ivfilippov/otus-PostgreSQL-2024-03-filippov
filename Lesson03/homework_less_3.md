# Установка PostgreSQL  



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

**Ставим Docker Engine**  
И настраиваем
```
yc-user@otus-vm:~$ curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh && rm get-docker.sh && sudo usermod -aG docker $USER && newgrp docker
# Executing docker install script, commit: e5543d473431b782227f8908005543bb4389b8de
+ sh -c apt-get update -qq >/dev/null
+ sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
+ sh -c install -m 0755 -d /etc/apt/keyrings
+ sh -c curl -fsSL "https://download.docker.com/linux/ubuntu/gpg" | gpg --dearmor --yes -o /etc/apt/keyrings/docker.gpg
+ sh -c chmod a+r /etc/apt/keyrings/docker.gpg
+ sh -c echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu focal stable" > /etc/apt/sources.list.d/docker.list
+ sh -c apt-get update -qq >/dev/null
+ sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq docker-ce docker-ce-cli containerd.io docker-compose-plugin docker-ce-rootless-extras docker-buildx-plugin >/dev/null
+ sh -c docker version
Client: Docker Engine - Community
 Version:           26.1.2
 API version:       1.45
 Go version:        go1.21.10
 Git commit:        211e74b
 Built:             Wed May  8 13:59:58 2024
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          26.1.2
  API version:      1.45 (minimum version 1.24)
  Go version:       go1.21.10
  Git commit:       ef1912d
  Built:            Wed May  8 13:59:58 2024
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.31
  GitCommit:        e377cd56a71523140ca6ae87e30244719194a521
 runc:
  Version:          1.1.12
  GitCommit:        v1.1.12-0-g51d5e94
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

================================================================================

To run Docker as a non-privileged user, consider setting up the
Docker daemon in rootless mode for your user:

    dockerd-rootless-setuptool.sh install

Visit https://docs.docker.com/go/rootless/ to learn about rootless mode.


To run the Docker daemon as a fully privileged service, but granting non-root
users access, refer to https://docs.docker.com/go/daemon-access/

WARNING: Access to the remote API on a privileged Docker daemon is equivalent
         to root access on the host. Refer to the 'Docker daemon attack surface'
         documentation for details: https://docs.docker.com/go/attack-surface/

================================================================================

yc-user@otus-vm:~$ sudo docker network create pg-net
4810f914e1ce198c6e2d1d452ede41098b369f20cf89d144da95064e9a96a0d9
```
___


**Разворачиваем контейнер с PostgreSQL 15, смонтировав в него /var/lib/postgresql**   


```
yc-user@otus-vm:~$ sudo docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:15
Unable to find image 'postgres:15' locally
15: Pulling from library/postgres
b0a0cf830b12: Pull complete
bba71d614e9c: Pull complete
6f2e6d5ebad3: Pull complete
d0c0d42ce84d: Pull complete
14f5a23e0031: Pull complete
7d14fce4c0c7: Pull complete
bc6be004018d: Pull complete
c8fd86656ac0: Pull complete
219a2703456d: Pull complete
c07c2e84ef9b: Pull complete
e4d84cc0094d: Pull complete
bb1a7eb4d877: Pull complete
0fb515267889: Pull complete
a11b69383d8b: Pull complete
Digest: sha256:723e6d150f7d0871727266de86b448fe07f149fbf874883afe0c5ab4414c88a8
Status: Downloaded newer image for postgres:15
3d406eaa485bf851d7c95b6821e3e4fe2fbcd3b4d22ef3cc42f138c28cc30aa0
```  

**Разворачиваем контейнер с клиентом postgres, подключаемся к контейнеру с posgreSQL**

```
yc-user@otus-vm:~$ sudo docker run -it --rm --network pg-net --name pg-client postgres:15 psql -h pg-server -U postgres
Password for user postgres:
psql (15.7 (Debian 15.7-1.pgdg120+1))
Type "help" for help.

postgres=# \conninfo
You are connected to database "postgres" as user "postgres" on host "pg-server" (address "172.18.0.2") at port "5432".
postgres=#

```

**Добавим таблицу с парой строк**

```
postgres=# CREATE DATABASE otus;
CREATE DATABASE
postgres=# \c otus
You are now connected to database "otus" as user "postgres".
otus=# create table tst (id int)
otus-# ;
CREATE TABLE
otus=# insert into tst (id) values (1),(2);
INSERT 0 2
otus=# select * from tst;
 id
----
  1
  2
(2 rows)

otus=#
```

**Подключаемся к контейнеру с сервером извне инстансов GCP/ЯО/места установки докера**  

```
➜  Lesson03 git:(lesson3) ✗ psql -p 5432 -U postgres -h 178.154.205.62 -d otus -W
Password:
psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
Type "help" for help.

otus=#
```  

**Удаляем контейнер**  

```
yc-user@otus-vm:~$ sudo docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                       NAMES
3d406eaa485b   postgres:15   "docker-entrypoint.s…"   59 minutes ago   Up 55 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pg-server
yc-user@otus-vm:~$ sudo docker stop 3d406eaa485b
3d406eaa485b
yc-user@otus-vm:~$ sudo docker rm 3d406eaa485b
3d406eaa485b
yc-user@otus-vm:~$ sudo docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
yc-user@otus-vm:~$
```  

**Создаем его заново**

```
yc-user@otus-vm:~$ sudo docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:15
2e06252f0595f2c781ec37f000f3f4562d8ef1ec6cd6527735d91f5c798f98c1

```

**Подключаемся к контейнеру и проверяем что данные остались**

```
yc-user@otus-vm:~$ sudo docker run -it --rm --network pg-net --name pg-client postgres:15 psql -h pg-server -U postgres
Password for user postgres:
psql (15.7 (Debian 15.7-1.pgdg120+1))
Type "help" for help.

postgres=# \c otus
You are now connected to database "otus" as user "postgres".
otus=# select * from tst;
 id
----
  1
  2
(2 rows)

otus=#
```