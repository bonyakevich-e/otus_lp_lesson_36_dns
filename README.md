### OTUS Linux Professional Lesson #36 | Subject: DNS. Настройка и обслуживание

#### ЦЕЛЬ:
Изучить основы DNS, научиться работать с технологией Split-DNS в Linux-based системах

#### Описание домашнего задания:
1. развернуть стенд https://github.com/erlong15/vagrant-bind 
- добавить еще один сервер client2
- завести в зоне dns.lab имена:
    - web1, смотрит на клиент1
    - web2,  смотрит на клиент2
- завести еще одну зону newdns.lab
- завести в ней запись
    - www, смотрит на обоих клиентов

2. настроить split-dns
- клиент1 видит обе зоны, но в зоне dns.lab только web1
- клиент2 видит только dns.lab

#### ОПИСАНИЕ ВЫПОЛНЕНИЯ
После развёртывания стенда из Vagrantfile будут сделаны все необходимые настройки для успешного выполнения заданий (с помощью Ansible).

Проверка на client:
```
[root@client ~]# ping www.newdns.lab
[root@client ~]# ping web1.dns.lab
[root@client ~]# ping web2.dns.lab
```
![homework dns 1](https://github.com/user-attachments/assets/2bbce893-8f8e-438d-95ed-c9454e6dc71c)

Видим, что client видит обе зоны (dns.lab и newdns.lab), однако информацию о хосте web2.dns.lab он получить не может. 

Проверка на client2: 

[root@client2 ~]# ping www.newdns.lab
[root@client2 ~]# ping web1.dns.lab
[root@client2 ~]# ping web2.dns.lab

![homework dns 2](https://github.com/user-attachments/assets/0a57a69b-da78-459c-a785-d0cfca1016a1)

Видим, что client2 видит всю зону dns.lab и не видит зону newdns.lab

#### ЗАМЕЧАНИЯ
1. Для синхронизации зон между master и slave нужно:
   - обновить SOA serial
   - сделать `rndc reload` на мастере
   - мастер отправит notify о изменении зоны
   - слейв запросит zone transfer
   BIND не отслеживает изменения файлов. Это нормально, что он не делает этого автоматически. Нужно запускать перезагрузку rndc на ведущем устройстве 
   после каждого изменения. Ведомое устройство не может заставить ведущего перезагрузить конфигурацию/зоны.

