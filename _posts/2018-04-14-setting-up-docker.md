---
layout: post
title: Настраиваем Docker
comments: true
---

Перво-наперво - обустроим сервер и для хостинга и для разработки. А для этого будем всё оборачивать в Docker. Итак, берём убунтушную виртуалку и начинаем колдовать.

<!--more-->

Сносим PHP, ибо [богомерзкий фрактал](https://eev.ee/blog/2012/04/09/php-a-fractal-of-bad-design/):

{% highlight shell %}
sudo apt-get remove -y php-common
{% endhighlight %}

Сносим Apache, MySQL, PostgreSQL, ибо всё это будет жить в контейнерах:

{% highlight shell %}
sudo apt-get remove -y apache2 mysql-common postgresql-common
{% endhighlight %}

Ставим Python и PIP в него, потому что надо же на чём-то скриптеть:

{% highlight shell %}
sudo apt-get install -y python python-pip
sudo pip install --upgrade pip
{% endhighlight %}

Приступаем к главному - устанавливаем Docker:

{% highlight shell %}
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update && apt-cache policy docker-ce
sudo apt-get install -y docker-ce
sudo usermod -aG docker ${USER}
{% endhighlight %}

На всякий случай - ставим ещё и Docker Compose:

{% highlight shell %}
DCVER=`curl -s "https://api.github.com/repos/docker/compose/releases/latest" | grep '"tag_name":' | sed -E 's/.*"([^"]+)".*/\1/'`
sudo curl -L https://github.com/docker/compose/releases/download/${DCVER}/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo curl -L https://raw.githubusercontent.com/docker/compose/${DCVER}/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose  
{% endhighlight %}

Кажется, всё. Перезагружаемся и проверяем:

{% highlight shell %}
sudo systemctl status docker
docker run hello-world
docker-compose --version
{% endhighlight %}

Видим ожидаемые результаты, зачищаем остатки нашей деятельности:

{% highlight shell %}
docker system prune -fa
sudo apt-get clean
sudo apt-get autoremove
{% endhighlight %}

Удовлетворённо крякаем, сохраняем снэпшот на будущее, и идём за баночкой Diet Coke.
