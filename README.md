# Домашнее задание к занятию 4 «Работа с roles»

## Подготовка к выполнению

1. * Необязательно. Познакомьтесь с [LightHouse](https://youtu.be/ymlrNlaHzIY?t=929).
2. Создайте два пустых публичных репозитория в любом своём проекте: vector-role и lighthouse-role.
3. Добавьте публичную часть своего ключа к своему профилю на GitHub.

## Основная часть

Ваша цель — разбить ваш playbook на отдельные roles. 

Задача — сделать roles для ClickHouse, Vector и LightHouse и написать playbook для использования этих ролей. 

Ожидаемый результат — существуют три ваших репозитория: два с roles и один с playbook.

**Что нужно сделать**

#### 1. Создайте в старой версии playbook файл `requirements.yml` и заполните его содержимым:

   ```yaml
   ---
     - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
       scm: git
       version: "1.13"
       name: clickhouse 
   ```

#### 2. При помощи `ansible-galaxy` скачайте себе эту роль.

<details>
<summary>Вывод консоли:</summary>

```sh
[skvorchenkov@localhost playbook]$ ansible-galaxy install -r requirements.yml -p roles
- extracting clickhouse to /home/skvorchenkov/ansible-04/playbook/roles/clickhouse
- clickhouse (1.13) was installed successfully
```   
</details>

#### 3. Создайте новый каталог с ролью при помощи `ansible-galaxy role init vector-role`.

<details>
<summary>Вывод консоли:</summary>

```sh
[skvorchenkov@localhost playbook]$ ansible-galaxy role init vector-role
- Role vector-role was created successfully
```   
</details>

#### 4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. 
#### 5. Перенести нужные шаблоны конфигов в `templates`.
#### 6. Опишите в `README.md` обе роли и их параметры. Пример качественной документации ansible role [по ссылке](https://github.com/cloudalchemy/ansible-prometheus).

Документация:  
- [Документация к Vector-role](playbook/roles/vector-role/README.md)

#### 7. Повторите шаги 3–6 для LightHouse. Помните, что одна роль должна настраивать один продукт.

- [Документация к Lighthouse-role](playbook/roles/lighthouse-role/README.md)

#### 8. Выложите все roles в репозитории. Проставьте теги, используя семантическую нумерацию. Добавьте roles в `requirements.yml` в playbook.

 ```yaml
   ---
---
- src: https://github.com/AlexeySetevoi/ansible-clickhouse.git
  scm: git
  version: "1.13"
  name: clickhouse

- src: https://github.com/Mrachneyshiy/vector-role.git
  scm: git
  version: "v1.1"
  name: vector-role

- src: https://github.com/Mrachneyshiy/lighthouse-role.git
  scm: git
  version: "1.1"
  name: lighthouse-role
 ```

#### 9. Переработайте playbook на использование roles. Не забудьте про зависимости LightHouse и возможности совмещения `roles` с `tasks`.

<details>
<summary>Вывод консоли:</summary>

```sh
[skvorchenkov@localhost playbook]$ ansible-playbook site.yml -i inventory/prod.yml

PLAY [Install Clickhouse] ******************************************************

TASK [Gathering Facts] *********************************************************
ok: [clickhouse-01]

TASK [clickhouse : Include OS Family Specific Variables] ***********************
ok: [clickhouse-01]

TASK [clickhouse : include_tasks] **********************************************
included: /home/skvorchenkov/ansible-04/playbook/roles/clickhouse/tasks/precheck.yml for clickhouse-01

TASK [clickhouse : Requirements check | Checking sse4_2 support] ***************
ok: [clickhouse-01]

TASK [clickhouse : Requirements check | Not supported distribution && release] ***
skipping: [clickhouse-01]

TASK [clickhouse : include_tasks] **********************************************
included: /home/skvorchenkov/ansible-04/playbook/roles/clickhouse/tasks/params.yml for clickhouse-01

TASK [Set clickhouse_service_enable] *******************************************
ok: [clickhouse-01]

TASK [Set clickhouse_service_ensure] *******************************************
ok: [clickhouse-01]

TASK [clickhouse : include_tasks] **********************************************
included: /home/skvorchenkov/ansible-04/playbook/roles/clickhouse/tasks/install/yum.yml for clickhouse-01

TASK [Install by YUM | Ensure clickhouse repo installed] ***********************
ok: [clickhouse-01]

TASK [Install by YUM | Ensure clickhouse package installed (latest)] ***********
ok: [clickhouse-01]

TASK [Install by YUM | Ensure clickhouse package installed (version latest)] ***
skipping: [clickhouse-01]

TASK [clickhouse : include_tasks] **********************************************
included: /home/skvorchenkov/ansible-04/playbook/roles/clickhouse/tasks/configure/sys.yml for clickhouse-01

TASK [Check clickhouse config, data and logs] **********************************
ok: [clickhouse-01] => (item=/var/log/clickhouse-server)
ok: [clickhouse-01] => (item=/etc/clickhouse-server)
ok: [clickhouse-01] => (item=/var/lib/clickhouse/tmp/)
ok: [clickhouse-01] => (item=/var/lib/clickhouse/)

TASK [clickhouse : Config | Create config.d folder] ****************************
ok: [clickhouse-01]

TASK [clickhouse : Config | Create users.d folder] *****************************
ok: [clickhouse-01]

TASK [clickhouse : Config | Generate system config] ****************************
ok: [clickhouse-01]

TASK [clickhouse : Config | Generate users config] *****************************
ok: [clickhouse-01]

TASK [clickhouse : Config | Generate remote_servers config] ********************
skipping: [clickhouse-01]

TASK [clickhouse : Config | Generate macros config] ****************************
skipping: [clickhouse-01]

TASK [clickhouse : Config | Generate zookeeper servers config] *****************
skipping: [clickhouse-01]

TASK [clickhouse : Config | Fix interserver_http_port and intersever_https_port collision] ***
skipping: [clickhouse-01]

TASK [clickhouse : include_tasks] **********************************************
included: /home/skvorchenkov/ansible-04/playbook/roles/clickhouse/tasks/service.yml for clickhouse-01

TASK [Ensure clickhouse-server.service is enabled: True and state: started] ****
ok: [clickhouse-01]

TASK [clickhouse : Wait for Clickhouse Server to Become Ready] *****************
ok: [clickhouse-01]

TASK [clickhouse : include_tasks] **********************************************
included: /home/skvorchenkov/ansible-04/playbook/roles/clickhouse/tasks/configure/db.yml for clickhouse-01

TASK [clickhouse : Set ClickHose Connection String] ****************************
ok: [clickhouse-01]

TASK [clickhouse : Gather list of existing databases] **************************
ok: [clickhouse-01]

TASK [clickhouse : Config | Delete database config] ****************************

TASK [clickhouse : Config | Create database config] ****************************

TASK [clickhouse : include_tasks] **********************************************
included:
/home/skvorchenkov/ansible-04/playbook/roles/clickhouse/tasks/configure/dict.yml for clickhouse-01

TASK [clickhouse : Config | Generate dictionary config] ************************
skipping: [clickhouse-01]

TASK [clickhouse : include_tasks] **********************************************
skipping: [clickhouse-01]

PLAY [Install Vector] **********************************************************

TASK [Gathering Facts] *********************************************************
ok: [clickhouse-02]

TASK [vector-role : Download vector packages] **********************************
ok: [clickhouse-02]

TASK [vector-role : Install vector packages] ***********************************
ok: [clickhouse-02]

TASK [vector-role : Apply vector template] *************************************
ok: [clickhouse-02]

TASK [vector-role : Change vector systemd unit] ********************************
ok: [clickhouse-02]

PLAY [Install lighthouse] ******************************************************

TASK [Gathering Facts] *********************************************************
ok: [lighthouse-03]

TASK [lighthouse-role : Install git (Lighthouse)] ******************************
ok: [lighthouse-03]

TASK [lighthouse-role : Install epel-release (Lighthouse)] *********************
ok: [lighthouse-03]

TASK [lighthouse-role : Install nginx (Lighthouse)] ****************************
ok: [lighthouse-03]

TASK [lighthouse-role : Apply nginx config (Lighthouse)] ***********************
ok: [lighthouse-03]

TASK [lighthouse-role : Clone repository (Lighthouse)] *************************
ok: [lighthouse-03]

TASK [lighthouse-role : Apply config (Lighthouse)] *****************************
ok: [lighthouse-03]

PLAY RECAP *********************************************************************
clickhouse-01 : ok=23 changed=0 unreachable=0 failed=0 skipped=10 rescued=0 ignored=0
clickhouse-02 : ok=5 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
lighthouse-03 : ok=7 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```   
</details>

#### 10. Выложите playbook в репозиторий.
#### 11. В ответе дайте ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.

1. - [Lighthouse-role](https://github.com/Mrachneyshiy/lighthouse-role.git) 

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---


