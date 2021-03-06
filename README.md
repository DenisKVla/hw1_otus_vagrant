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
После завершения установки в дирректории появилась сборка ```centos-7.7.1908-kernel-5-x86_64-Minimal.box``` (Имя *7.7.1908* надо было тоже поменять на *7.9.2009*, забыл)

## vagrant init (тестирование)

Проверил работоспособность созданого образа 
```
vagrant box add --name centos-7-5 centos-7.7.1908-kernel-5-x86_64-Minimal.box 
```
```
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'centos-7-5' (v0) for provider: 
    box: Unpacking necessary files from: file:///home/otus/otus_hw/manual_kernel_update/packer/centos-7.7.1908-kernel-5-x86_64-Minimal.box
==> box: Successfully added box 'centos-7-5' (v0) for 'virtualbox'!
```
Проверил наличие образа выполнив:
```
vagrant box list
```
Увидел что образ добавился в список:
```
centos-7-5 (virtualbox, 0)

```

Создав директорию ```test/``` и перейдя туда выполнил инициализацию командой:
```
vagrant init centos-7-5
```
В этой директории создался Vagrantfile
Далее я выполнил команды:
```
vagrant up
vagrant ssh

```
Тем самым оказался на гостевой машине. Проверил версию ядра ```uname -r```, ядро:```5.16.5-1.el7.elrepo.x86_64```

Затем удалил образ из хранилища командой:
```vagrant box remove centos-7-5```

## Vagrant cloud

Создав учетную запись на VagrantCloud и обновив vagrant до версии 2.2.19 получилось опубликовать созданный образ командой:
```
vagrant cloud publish --release denisordkol/centos-7-5 1.0 virtualbox centos-7.7.1908-kernel-5-x86_64-Minimal.box
```
Результат выполнения:
```
Releasing box...
Complete! Published denisordkol/centos-7-5
Box:              denisordkol/centos-7-5
Description:      
Private:          yes
Created:          2022-02-07T19:45:24.136Z
Updated:          2022-02-07T19:45:24.136Z
Current Version:  N/A
Versions:         1.0
Downloads:        0

```
