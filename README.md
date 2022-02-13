# hw1_otus_vagrant
На виртуальную машину Ubuntu 18 было установленны:
* VirtualBox 6.0
* Vagrant
* Packer
* Git

## Kernal update

Склонировав репозиторий к себе на машину, запустил виртуальную машину и проверил версию  ядра командой 
```uname -r```, результат выполнения команды: ```3.10.0-1127.el7.x86_64.```
Подлючил необходимы репозиторий для обновления версии ядра командой:
```
sudo yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
```
Затем обнолвил ядро командой
```
sudo yum --enablerepo elrepo-kernel install kernel-ml -y
```
Обновил конфигурацию загрузчика:
```
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```
Выбираем загрузку с новым ядром по-умолчанию:
```
sudo grub2-set-default 0
```
После перезагрузил гостевую машину и выполнил команду ```uname -r```. Вывод:```5.16.4-1.el7.elrepo.x86_64```. Ядро обновилось.

## Packer

Для установки пакера пришлось перелогинить на root.
Изменил скачаный файл конфигурации ```centos.json```, вписав следующие строки:
```
 "iso_url": "https://mirror.yandex.ru/centos/7.9.2009/isos/x86_64/CentOS-7-x86_64-Minimal-2009.iso",
 "iso_checksum": "sha256:07b94e6b1a0b0260b94c83d6bb76b26bf7a310dc78d7a9c7432809fb9bc6194a",

```
Затем запустил сборку командой:
```
packer build centos.json
```
