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

5) Запустил ansible-lint site.yml и исправил ошибки.

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
  
6) Запустил playbook на этом окружении с флагом --check 
7) Повторно запустил playbook с флагом --diff и убедился, что playbook идемпотентен.
```
root@Debian-New12:/home/byzgaev12new/ansible-yandex# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse] **********************************************************************************************************************************************************************************************************

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************************************
ok: [clickhouse] => (item=clickhouse-client)
ok: [clickhouse] => (item=clickhouse-server)
failed: [clickhouse] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-v24.3.12.75-lts.rpm", "elapsed": 0, "gid": 1000, "group": "centos", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "centos", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-v24.3.12.75-lts.noarch.rpm"}

TASK [Get clickhouse distrib] ******************************************************************************************************************************************************************************************************
ok: [clickhouse] => (item=clickhouse-client)
ok: [clickhouse] => (item=clickhouse-server)
ok: [clickhouse] => (item=clickhouse-common-static)

TASK [Install clickhouse packages] *************************************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Modify Clickhouse config.xml] ************************************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Flush handlers] **************************************************************************************************************************************************************************************************************

TASK [Wait for clickhouse-server to become available] ******************************************************************************************************************************************************************************
Pausing for 30 seconds (output is hidden)
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse]

TASK [Create database] *************************************************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [Create Clickhouse table] *****************************************************************************************************************************************************************************************************
ok: [clickhouse]

PLAY [Vector] **********************************************************************************************************************************************************************************************************************

TASK [Create vector work directory] ************************************************************************************************************************************************************************************************
ok: [vector]

TASK [Get Vector distrib] **********************************************************************************************************************************************************************************************************
ok: [vector]

TASK [Unzip Vector archive] ********************************************************************************************************************************************************************************************************
ok: [vector]

TASK [Install Vector binary] *******************************************************************************************************************************************************************************************************
ok: [vector]

TASK [Check Vector installation] ***************************************************************************************************************************************************************************************************
changed: [vector]

TASK [Create Vector etc directory] *************************************************************************************************************************************************************************************************
ok: [vector]

TASK [Create Vector config vector.yaml] ********************************************************************************************************************************************************************************************
ok: [vector]

TASK [Create vector.service daemon] ************************************************************************************************************************************************************************************************
changed: [vector]

TASK [Modify Vector.service file ExecStart] ****************************************************************************************************************************************************************************************
--- before: /lib/systemd/system/vector.service
+++ after: /lib/systemd/system/vector.service
@@ -7,7 +7,7 @@
 [Service]
 User=vector
 Group=vector
-ExecStartPre=/usr/bin/vector validate
+ExecStartPre=/usr/bin/vector validate --config-yaml /etc/vector/vector.yaml
 ExecStart=/usr/bin/vector
 ExecReload=/usr/bin/vector validate
 ExecReload=/bin/kill -HUP $MAINPID

changed: [vector]

TASK [Modify Vector.service file ExecStartPre] *************************************************************************************************************************************************************************************
--- before: /lib/systemd/system/vector.service
+++ after: /lib/systemd/system/vector.service
@@ -8,7 +8,7 @@
 User=vector
 Group=vector
 ExecStartPre=/usr/bin/vector validate --config-yaml /etc/vector/vector.yaml
-ExecStart=/usr/bin/vector
+ExecStart=/usr/bin/vector --config-yaml /etc/vector/vector.yaml
 ExecReload=/usr/bin/vector validate
 ExecReload=/bin/kill -HUP $MAINPID
 Restart=no

changed: [vector]

TASK [Create user vector] **********************************************************************************************************************************************************************************************************
ok: [vector]

TASK [Create Vector data_dir] ******************************************************************************************************************************************************************************************************
ok: [vector]

RUNNING HANDLER [Start Vector service] *********************************************************************************************************************************************************************************************
changed: [vector]

PLAY [Lighthouse] ******************************************************************************************************************************************************************************************************************

TASK [Pre-install Nginx & Git client] **********************************************************************************************************************************************************************************************
ok: [lighthouse]

TASK [Clone Lighthouse source code by Git] *****************************************************************************************************************************************************************************************
ok: [lighthouse]

TASK [Prepare nginx config] ********************************************************************************************************************************************************************************************************
ok: [lighthouse]

PLAY RECAP *************************************************************************************************************************************************************************************************************************
clickhouse             : ok=6    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
lighthouse             : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector                 : ok=13   changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```























   













































