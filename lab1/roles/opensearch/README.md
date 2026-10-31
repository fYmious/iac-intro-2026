# Роль opensearch

Роль ставит и настраивает OpenSearch и OpenSearch Dashboards из официальных пакетов `.deb` на одну машину с Debian или Ubuntu. Кластер получается из одного узла. Полный отчёт с проверками и скриншотами лежит в `README.md` в корне проекта.

## Что нужно

* Целевая машина с Debian или Ubuntu, процессор x86_64 или aarch64, не меньше 4 ГБ памяти.
* Доступ по SSH и права sudo.
* Коллекция `ansible.posix`, её ставит команда `ansible-galaxy collection install ansible.posix`.
* Доступ машины к `artifacts.opensearch.org` или свой адрес зеркала в параметре `opensearch_download_base_url`.

## Обязательный параметр

Нужно задать `opensearch_admin_password`. Пароль должен быть сильным, не короче 8 символов, с заглавными и строчными буквами, цифрой и спецсимволом. Без него OpenSearch 3 не запускается. Лучше хранить его в зашифрованном файле.

```bash
ansible-vault create group_vars/all/vault.yml
```

В файл нужно записать строку `opensearch_admin_password: "ваш пароль"`.

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
| `opensearch_version` | `3.8.0` | Версия обоих продуктов |
| `opensearch_dashboards_install` | `true` | Ставить ли веб интерфейс |
| `opensearch_heap_size` | `1g` | Память Java, не больше половины памяти машины |
| `opensearch_network_host` | `127.0.0.1` | Адрес, на котором слушает OpenSearch |
| `opensearch_http_port` | `9200` | Порт OpenSearch |
| `opensearch_dashboards_port` | `5601` | Порт интерфейса |
| `opensearch_extra_config` | пусто | Любые дополнительные настройки OpenSearch |
| `opensearch_wait_retries` | `30` | Число проверок готовности |
| `opensearch_wait_delay` | `10` | Пауза между проверками в секундах |

## Теги

Этапы можно запускать по отдельности. Доступны теги `opensearch_assert`, `opensearch_prereqs`, `opensearch_install`, `opensearch_configure`, `opensearch_service`, `opensearch_verify` и `opensearch_dashboards`.

## Проверка

```bash
ansible-playbook -i roles/opensearch/tests/inventory roles/opensearch/tests/test.yml
```

Плейбук запустит роль, проверит, что службы включены и работают, и что статус кластера `green` или `yellow`.

## Зависимости

Других ролей нет.

## Лицензия

MIT
