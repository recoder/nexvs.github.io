---
layout: post
title: "Настраиваем Traefik"
#date: "2018-04-15"
#slug: "traefik"
#description: "Example content from lanyon. If page description is more than 140 words, it will be shown as post summary on home page and blog index else post excerpt will be shown. Same rule is for html meta description: >140 words in description or first 50 words of posts will be shown as summary. Page excerpt supports markdown formatted summary."
category: 
  - server
  - architecture
tags:
  - traefik
  - http
show_meta: true
comments: true
mathjax: true
gistembed: true
published: true
## for twitter summary card with squared image and page description or page excerpt:
# imagesummary: foo.png
## for twitter card with large image:
# imagefeature: "http://img.youtube.com/vi/VEIrQUXm_hY/0.jpg"
## for twitter video card: (active for this page)
---

Для того чтобы на одном домашнем сервере можно было поднять несколько виртуальных хостов
мы попробуем настроить [Træfik](https://traefik.io) как основной обратный HTTP-прокси.
Он же поможет нам с поддержкой HTTPS через [Let's Encrypt](https://letsencrypt.org/).

<!--more-->

Итак, пробуем поднять несколько сервисов одновременно на разных доменах.
Идём к нашему регистратору и регистрируем DNS на `recoder.rocks` и `*.recoder.rocks`,
где у нас будут жить как минимум `status.recoder.rocks`, `test.recoder.rocks`, и `www.recoder.rocks`.

Создаём внутреннюю Docker'овскую сетку `whisper`:

```shell
docker network create whisper
```
Берём такую конфигурацию:

```toml
# traefik.toml
defaultEntryPoints = ["http", "https"]

[web]
address = ":8080"
# http://www.htaccesstools.com/htpasswd-generator/
  [web.auth.basic]
    users = ["recoder:$xxxxxxxxxxxxxxxxxxxxxx"]

[entryPoints]
  [entryPoints.http]
    address = ":80"
  [entryPoints.http.redirect]
    entryPoint = "https"
  [entryPoints.https]
    address = ":443"
  [entryPoints.https.tls]

[acme]
email = "recoder@gmail.com"
storage = "acme.json"
entryPoint = "https"
onHostRule = true

[acme.httpChallenge]
entryPoint = "http"

[traefikLog]
# stdout
[accessLog]
# stdout
```

Разложим файлы: конфигурация будет жить в `/etc/traefik/`,
а сертификационные данные - в `/var/traefik/acme.json`.

```shell
sudo mkdir -p /var/traefik/
sudo touch /var/traefik/acme.json
sudo chmod 600 /var/traefik/acme.json
sudo mkdir -p /etc/traefik/
sudo mv traefik.toml /etc/traefik/
```

Проверяем конфигурацию, делая пробный запуск:

```shell
docker run -v /var/run/docker.sock:/var/run/docker.sock -v /etc/traefik/traefik.toml:/traefik.toml -v /var/traefik/acme.json:/acme.json -p 80:80 -p 443:443 -l traefik.frontend.rule=Host:test.recoder.rocks -l traefik.port=8080 --network whisper traefik --docker --api
```

Проверяем - поднялся ли https://test.recoder.rocks/ ?
Если поднялся - запускаем его уже демоном:

```shell
docker run -d --net whisper -v /var/run/docker.sock:/var/run/docker.sock -v /etc/traefik/traefik.toml:/traefik.toml -v /var/traefik/acme.json:/acme.json -p 80:80 -p 443:443 -l traefik.frontend.rule=Host:status.recoder.rocks -l traefik.port=8080 traefik --docker --api
```
Для крутизны - попробуем поднять [Portainer](https://portainer.io) как UI для Docker'a:

```shell
docker run --net whisper -v /var/run/docker.sock:/var/run/docker.sock -v /opt/portainer:/data -l traefik.port=9000 -l "traefik.frontend.rule=Host:test.recoder.rocks" portainer/portainer
```

Проверяем https://test.recoder.rocks/ и создаём пользователей.
Как обычно, если получилось - запускаем уже демоном на https://mgmt.recoder.rocks/

```shell
docker run -d --net whisper -v /var/run/docker.sock:/var/run/docker.sock -v /opt/portainer:/data -l traefik.port=9000 -l "traefik.frontend.rule=Host:mgmt.recoder.rocks" portainer/portainer
```

Запускаем тестовый контейнер и смотрим, появился ли он на https://test.recoder.rocks/:

```shell
docker run -p 8000:8000 --network whisper -l traefik.frontend.rule=Host:test.recoder.rocks --name whoami jwilder/whoami
```

Если всё сработало и все домены отвечают по HTTPS - считаем свою задачу выполненной,
наливаем себе ванильного чая, перезагружаемся и... идём курить мануалы по `systemd`.



