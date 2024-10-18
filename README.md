# Домашнее задание "`Использование Ansible`"   

---

### Подготовка к выполнению

Подготовьте в Yandex Cloud три хоста: для clickhouse, для vector и для lighthouse.
Репозиторий LightHouse находится по ссылке.

### Основная часть

1) Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает LightHouse.
2) При создании tasks рекомендую использовать модули: get_url, template, yum, apt.
3) Tasks должны: скачать статику LightHouse, установить Nginx или любой другой веб-сервер, настроить его конфиг для открытия LightHouse, запустить веб-сервер.
4) Подготовьте свой inventory-файл prod.yml.
5) Запустите ansible-lint site.yml и исправьте ошибки, если они есть.
6) Попробуйте запустить playbook на этом окружении с флагом --check.
7) Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.
8) Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.
9) Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
10) Готовый playbook выложите в свой репозиторий, поставьте тег 08-ansible-03-yandex на фиксирующий коммит, в ответ предоставьте ссылку на него.

### Выполнение подготовки

-  Подготовил в Yandex Cloud три хоста: для clickhouse, для vector и для lighthouse.

   ![image.jpg](https://github.com/Byzgaev-I/Ansible-yandex/blob/main/VM%20Yandex.png)


### Выполнение заданий

1) Дописал playbook и сделал ещё один play, который устанавливает и настраивает LightHouse.
2) При создании tasks использовал модули: get_url, template, yum, apt.
3) Tasks должны: скачать статику LightHouse, установить Nginx или любой другой веб-сервер, настроить его конфиг для открытия LightHouse, запустить веб-сервер.
4) Подготовил свой inventory-файл prod.yml.
   
   [site.yml](https://github.com/Byzgaev-I/Ansible-yandex/blob/main/site.yml)

5) Запустиk ansible-lint site.yml и исправил ошибки.

```
root@Debian-New12:/home/byzgaev12new/ansible-yandex# ansible-lint site.yml
WARNING  Listing 2 violation(s) that are fatal  
site.yml:48 Task/Handler: Modify Clickhouse config.xml  
yaml[line-length]: Line too long (187 > 160 characters)  
site.yml:72  
Read documentation for instructions on how to ignore specific rule violations.    
Rule Violation Summary                     
 count tag               profile rule associated tags           
     1 yaml[line-length] basic   formatting, yaml               
     1 args[module]              syntax, experimental (warning)   

Failed after min profile: 1 failure(s), 1 warning(s) on 1 files.  
```
   
![image.jpg](https://github.com/Byzgaev-I/Ansible-yandex/blob/main/2ansible-lint%20site%20.png)  
  


























   













































