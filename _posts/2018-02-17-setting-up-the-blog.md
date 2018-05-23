---
layout: post
title: "Setting up the blog"
#date: "2013-01-01"
#slug: "example_content"
#description: "Example content from lanyon. If page description is more than 140 words, it will be shown as post summary on home page and blog index else post excerpt will be shown. Same rule is for html meta description: >140 words in description or first 50 words of posts will be shown as summary. Page excerpt supports markdown formatted summary."
category: 
  - meta
# tags will also be used as html meta keywords.
tags:
  - blog
  - jekyll
  - lanyon
show_meta: true
comments: true
#mathjax: true
gistembed: true
published: true
noindex: false
nofollow: false
## for twitter summary card with squared image and page description or page excerpt:
# imagesummary: foo.png
## for twitter card with large image:
# imagefeature: "http://img.youtube.com/vi/VEIrQUXm_hY/0.jpg"
## for twitter video card: (active for this page)
---

Что же это за проект и без блога? Так что идём и создаём блог прямо на Github'e,
благо там есть [встроенная](https://help.github.com/articles/about-github-pages-and-jekyll/) поддержка [Jekyll](https://jekyllrb.com/).

Повыбирав тему оформления, я остановился на [Lanyon](https://github.com/poole/lanyon), но ненадолго.
Показалось что [Lanyon+](https://github.com/dyndna/lanyon-plus) будет чуть покруче, так что всё-таки он стал окончательной базой для блога.

<!--more-->

Настройка в принципе несложная, хотя иногда всё-таки магия GitHub'a даёт сбои.
Итак:

1. Форкаем себе `https://github.com/poole/lanyon`
2. Переименовываем `CNAME` обратно в `CNAME.sample`, потому что мне достаточно `recoder.github.com`
3. Редактируем конфигурационные файлики:
  * `_config.yml`
  * `robots.txt`
  * `_data/socialmedia.html`
  * `_includes/footer.html`
  * `blog/index.html`
  * `pages/about.md`
  * `pages/contact.md`
  * `pages/cv.md`
  * `pages/publications.md`
  * `pages/disclosure.md`
  * `syspages/*`
  * `humans.txt`
  * `_prose.yml`

Добавляем комментарии на [Disqus](https://disqus.com/):
* регистрируемся там, добавляем новый сайт на Jekyll
* прописываем имя блога в `_config.yml` как `disqus_shortname: {name}`

Добавляем [Google Analytics](https://www.google.com/analytics/):
* регистрируем там блог
* прописываем ID в `_config.yml` как `google_analytics: {id}`

И наконец переименовываем репозиторий в `recoder.github.io`, пьём кофе пять минут, и смотрим на результат
(ну или на письмо от GitHub'а об обнаруженных ошибках).




