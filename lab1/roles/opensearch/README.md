# Роль opensearch

Роль ставит и настраивает opensearch и opensearch dashboards из официальных пакетов `.deb` на одну машину с debian или ubuntu. Кластер получается из одного узла. Полный отчёт с проверками и скриншотами лежит в `README.md` в корне проекта.

## Что нужно

* целевая машина с debian или ubuntu, процессор x86_64 или aarch64, не меньше 4 гб памяти.
* доступ по ssh и права sudo.
* коллекция `ansible.posix`, её ставит команда `ansible-galaxy collection install ansible.posix`.
* доступ машины к `artifacts.opensearch.org`.

## Обязательный параметр

Нужно задать `opensearch_admin_password`. Пароль должен быть сильным, не короче 8 символов, с заглавными и строчными буквами, цифрой и спецсимволом. Без него opensearch 3 не запускается. Лучше хранить его в зашифрованном файле.

```bash
ansible-vault create group_vars/all/vault.yml
```

В файл нужно записать строку `opensearch_admin_password: "your_password"`.

## Пример плейбука

```yaml
- name: Развернуть OpenSearch
  hosts: all
  become: true
  roles:
    - role: opensearch
      vars:
        opensearch_heap_size: "2g"
        opensearch_network_host: "0.0.0.0"
```

## Основные параметры

Полный список с обоснованием лежит в корневом `README.md`, а значения по умолчанию в `defaults/main.yml`.

| Параметр | По умолчанию | Смысл |
|---|---|---|
| `opensearch_version` | `3.8.0` | версия обоих продуктов |
| `opensearch_dashboards_install` | `true` | ставить ли веб интерфейс |
| `opensearch_heap_size` | `1g` | память java, не больше половины памяти машины |
| `opensearch_network_host` | `127.0.0.1` | адрес, на котором слушает openSearch |
| `opensearch_http_port` | `9200` | порт opensearch |
| `opensearch_dashboards_port` | `5601` | порт интерфейса |
| `opensearch_wait_retries` | `30` | число проверок готовности |
| `opensearch_wait_delay` | `10` | пауза между проверками в секундах |

## Теги

Этапы можно запускать по отдельности. Доступны теги `opensearch_assert`, `opensearch_prereqs`, `opensearch_install`, `opensearch_configure`, `opensearch_verify` и `opensearch_dashboards`.

## Проверка

```bash
ansible-playbook -i roles/opensearch/tests/inventory roles/opensearch/tests/test.yml
```

Плейбук запустит роль, проверит, что службы включены и работают, и что статус кластера `green` или `yellow`.

## Зависимости

Других ролей нет.

## Лицензия

MIT
