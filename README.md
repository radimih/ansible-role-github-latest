# Ansible-роль `github_latest`

Роль для установки последнего релиза бинарника из **публичного** GitHub-репозитория.

## Аргументы роли

### Обязательные

| Аргумент                | Описание
| ----------------------- | ---
| `asset_filename_ending` | Окончание в имени файла в качестве критерия выбора для скачивания бинарника или архива из assets-файлов последнего релиза.</br> Скачивается первый найденный файл. Если это архив, то ожидается, что бинарник располагаться в корне архива под именем как указано в параметре `dest_binary`.
| `dest_binary`           | Полный путь и имя, под которым бинарник из репозитория будет установлен в систему
| `repo`                  | Имя GitHub-репозитория
| `user`                  | Пользователь/владелец GitHub-репозитория

### Необязательные

| Аргумент | Описание | Default
| --- | --- | ---

## Примеры

- установка [helmfile](https://github.com/helmfile/helmfile):

  ```yaml
  - role: github_latest
    vars:
      user: helmfile
      repo: helmfile
      asset_filename_ending: _linux_amd64.tar.gz
      dest_binary: ~/.local/bin/helmfile
  ```

- установка [Docker Compose](https://github.com/docker/compose):

  ```yaml
  - role: github_latest
    vars:
      user: docker
      repo: compose
      asset_filename_ending: -linux-aarch64
      dest_binary: /usr/local/bin/docker-compose
    become: true
  ```

## Ограничения

Роль при своей работе анонимно обращается к API GitHub. Для таких запросов со стороны
GitHub выставлен лимит - _60 запросов в час_.

Посмотреть текущие значения лимита:

```bash
curl -Is https://api.github.com/users/github | grep x-ratelimit
```

```yaml
x-ratelimit-limit: 60
x-ratelimit-remaining: 48
x-ratelimit-reset: 1676352080  # Unix time
x-ratelimit-resource: core
x-ratelimit-used: 12
```

 Во сколько лимит сбросится:

```bash
date -d @<число из HTTP-заголовка x-ratelimit-reset>
```

## TODO

- удалять предыдущие установленные версии бинарников
