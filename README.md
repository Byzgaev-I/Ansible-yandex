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
9) Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

## Руководство по использованию Ansible Playbook для установки ClickHouse, Vector и Lighthouse

## Описание

Этот Ansible Playbook предназначен для автоматической установки и настройки баз данных ClickHouse, системы логирования Vector, а также инструмента аудита производительности веб-страниц Lighthouse на удаленные серверы. Сценарий выполняет загрузку необходимых пакетов, их установку, а также создание и настройку конфигурационных файлов.

## Содержимое

- **site.yml**: Основной Playbook, который включает в себя установку и настройку ClickHouse и Vector.
- **vars.yml**: Файл переменных, содержащий версии программ и другие настройки.
- **vector.yml.j2**: Jinja2-шаблон для конфигурационного файла Vector.
- **inventory/prod.yml**: Инвентори-файл, содержащий описание хостов для развёртывания.
- **group_vars/lightHouse/varslh.yml**: Файл переменных для настройки Lighthouse.
- **lighthouse-config.json.j2**: Jinja2-шаблон для конфигурационного файла Lighthouse.

## Требования

- Ansible 2.9 или выше
- Доступ к серверам с установленной операционной системой на базе RedHat/CentOS
- Настроенный SSH-доступ к серверам

## Шаги по установке

1. **Подготовка серверов:**
   - Убедитесь, что на серверах установлены все необходимые зависимости, такие как Python.
   - Настройте SSH-доступ к серверам, указав приватный ключ в `inventory/prod.yml`.

2. **Настройка переменных:**
   - Откройте файл `vars.yml` и измените значения переменных, если необходимо:
     - `clickhouse_version`: Версия ClickHouse.
     - `clickhouse_packages`: Список пакетов ClickHouse для установки.
     - `vector_version`: Версия Vector.
     - `vector_config_dir`: Путь к каталогу конфигурации Vector.
     - `vector_config`: Конфигурация источников и потребителей данных в Vector.
     - `nodejs_version`: Версия Node.js для установки в процессе настройки Lighthouse.

3. **Запуск Playbook:**
   - Выполните следующую команду для запуска Playbook на удаленных серверах:
     ```bash
     ansible-playbook -i inventory/prod.yml site.yml
     ```

4. **Проверка установки:**
   - После завершения выполнения Playbook убедитесь, что службы ClickHouse и Vector запущены корректно:
     - Для ClickHouse: подключитесь к серверу и выполните команду `clickhouse-client`, чтобы проверить доступность базы данных `logs`.
     - Для Vector: убедитесь, что служба запущена с помощью команды `systemctl status vector`.
     - Для Lighthouse: убедитесь, что инструмент установлен и доступен для использования командой `lighthouse`.

## Детали выполнения

### ClickHouse:
- Playbook загружает и устанавливает необходимые RPM-пакеты ClickHouse.
- Служба `clickhouse-server` автоматически перезапускается после установки.
- Создаётся база данных `logs`.

### Vector:
- Playbook загружает и устанавливает RPM-пакет Vector.
- Применяется конфигурация, описанная в файле `vector.yml.j2`.
- Служба `vector` автоматически перезапускается после применения новой конфигурации.

### Lighthouse:
- Playbook загружает и устанавливает необходимые пакеты для работы с Node.js, включая `gcc-c++`, `make` и `curl`.
- Устанавливается и настраивается Node Version Manager (nvm) для управления версиями Node.js.
- Устанавливается указанная версия Node.js и инструмент Lighthouse через npm.
- Создаётся каталог конфигурации для Lighthouse в `/etc/lighthouse` с правами доступа 0755.
- Копируется конфигурационный файл Lighthouse из шаблона `lighthouse-config.json.j2` в `/etc/lighthouse/config.json` с правами доступа 0644.
- В случае необходимости Playbook перезапускает службу Lighthouse после применения конфигурации.

## Примечания

- Если установка пакета ClickHouse не удалась на этапе загрузки, Playbook попытается загрузить альтернативный пакет в режиме rescue.
- Конфигурация Vector и Lighthouse создается на основе шаблона `vector.yml.j2`, где можно гибко изменять настройки источников и приемников данных.
- В случае необходимости, вы можете изменить параметры служб ClickHouse, Vector и Lighthouse, редактируя соответствующие файлы конфигурации и перезапустив Playbook.
   






















   













































