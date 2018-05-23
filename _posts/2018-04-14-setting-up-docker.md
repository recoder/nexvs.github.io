---
layout: post
title: Настраиваем Docker
comments: true
---

Перво-наперво - обустроим сервер и для хостинга и для разработки. А для этого будем всё оборачивать в Docker. Итак, берём убунтушную виртуалку и начинаем колдовать.

Сносим PHP, ибо [богомерзкий фрактал](https://eev.ee/blog/2012/04/09/php-a-fractal-of-bad-design/):

~~~ bash
sudo apt-get remove -y php-common
~~~

Сносим Apache, MySQL, PostgreSQL, ибо всё это будет жить в контейнерах:

~~~ bash
sudo apt-get remove -y apache2 mysql-common postgresql-common
~~~

Ставим Python и PIP в него, потому что надо же на чём-то скриптеть:

~~~ bash
sudo apt-get install -y python python-pip
sudo pip install --upgrade pip
~~~

Приступаем к главному - устанавливаем Docker:

~~~ bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update && apt-cache policy docker-ce
sudo apt-get install -y docker-ce
sudo usermod -aG docker ${USER}
~~~

На всякий случай - ставим ещё и Docker Compose:

~~~ bash
DCVER=`curl -s "https://api.github.com/repos/docker/compose/releases/latest" | grep '"tag_name":' | sed -E 's/.*"([^"]+)".*/\1/'`
sudo curl -L https://github.com/docker/compose/releases/download/${DCVER}/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo curl -L https://raw.githubusercontent.com/docker/compose/${DCVER}/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose  
~~~

Кажется, всё. Перезагружаемся и проверяем:

~~~ bash
sudo systemctl status docker
docker run hello-world
docker-compose --version
~~~

Видим ожидаемые результаты, зачищаем остатки нашей деятельности:

~~~ bash
docker system prune -fa
sudo apt-get clean
sudo apt-get autoremove
~~~

Удовлетворённо крякаем, сохраняем снэпшот на будущее, и идём за баночкой Diet Coke.
