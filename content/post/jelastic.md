---
title: "Jelastic"
date: 2020-03-15T10:18:10+02:00
lastmod: 2020-03-15T10:18:10+02:00
tags: ["tech"]
---

>PaaS и инфраструктура для людей — одна из немногих технических тем, которые меня действительно заботят и где я вижу много места для инноваций. Убежден, что работа с инфраструктурой должна быть простой и понятной большинству разработчиков, а современное состояние дел просто ужасно, невероятно переусложнено и абсолютно не для людей. Jelastic пытается в некотором смысле решить эту проблему.

На просторах интернетов довольно мало не-маркетинговых материалов о Jelastic и я, как пользователь со стажем, хочу заполнить этот вакуум.

Первый раз о [Jelastic](https://jelastic.com/) я услышал на конференции JavaDay в 2014 году. CEO компании, Руслан Синицкий, выступал с keynote[^1] на открытии, а в эндорсеры контора подписала одного из авторов Java — Джеймса Гослинга. На самой конфе я не особо понял что это вообще такое, в память врезалось только сравнение с AWS, утверждение “вы не платите за неиспользуемые ресурсы”, а так же “мы начали поддерживать Docker, когда это еще не было мейнстримом”. Я тогда еще не сильно соображал в инфраструктуре, контейнерах и облаках, поэтому не проникся, однако название запомнил.

Спустя пару лет, когда я уже стал относительно хорошо разбираться в облачных вещах, у меня возникла задача где-то хостить свой свежеразрабатываемый стартап. AWS хотелось, тем более я в него тогда уже умел, но было жалко денег, я задумчиво перебирал другие решения и вдруг вспомнил о Jelastic. Быстрый поиск нашел мне одного из провайдеров (в Нидерландах), я подключился и мы поехали. Спустя некоторое время я перенес значительную часть инфраструктуры одного из моих заказчиков тоже на Jelastic, но уже у другого провайдера ([reg.ru](https://www.reg.ru/cloud-services/jelastic/)). В сумме в продакшене оно крутится уже 3 года.

### Итак, что же такое Jelastic, простыми словами и без маркетингового буллшита?

Это система управления инфраструктурой (виртуальными машинами и контейнерами) с большим количеством предконфигурированных приложений

>Одним из основных терминов Jelastic является "контейнер". Контейнер — это не Docker-контейнер, а [контейнер](https://docs.jelastic.com/what-are-system-containers), сделанный на технологии Virtuozzo[^2]. При этом Jelastic умеет разворачивать любой Docker-образ. Для конечного пользователя все выглядит как полноценный VPS или EC2-инстанс, к которому можно получить доступ по SSH, подкрутить настройки фаервола и даже запустить внутри cron-джобы, но на самом деле это не виртуальная машина. Виртуализация это сложно, да.

Вы заходите в систему и создаете “окружение”. В его рамках можно добавлять множество контейнеров + балансировщик над ними (который тоже — контейнер). Каждый контейнер может быть как “пустым”, то есть обычной "VPS" с CentOS или Ubuntu на борту, так и созданным из определенного шаблона, например базой MariaDB. UI предполагает несколько “типов” контейнеров — балансировщик, сервер приложений, sql или nosql база данных, кэш. Для каждой из “коробочек” можно выбрать нужный тип софта и версию. Так же есть возможность просто построить окружение из любых Docker-образов. Все контейнеры находятся в одной подсети и видят друг друга. Можно делать микросервисы.

{{< figure src="/images/jelastic/1.png" title="Создаем окружение" >}}

Каждый контейнер внутри окружения можно независимо масштабировать как вертикально[^3] — добавлять больше памяти или процессора (диска, сети), так и горизонтально — добавлять больше нод. Предусмотрен настраиваемый автоскейлинг по событиям поедания памяти, CPU, диска, сети (и других конфигурируемых параметров[^4]). Деньги вы платите только за те ресурсы которые фактически использовали ваши приложения. В этом смысле ценовая политика отличается от традиционных облачных провайдеров, которые лупят деньги за весь инстанс сразу, пока он был включён.

Идея платформы возникла во время работы Руслана Синицкого на каком-то производстве (связанном с геофизическими делами) где нужно было управлять инфраструктурой. В то далёкое время, когда еще не придумали ни докеров, ни кубернетесов, ни хотя бы анзиблов с паппетами, ребята разработали свой специфичный софт, а потом создали стартап где занялись разработкой полноценного продукта.

Jelastic не продается как чистый IaaS, то есть это не совсем аналог AWS, Azure или DigitalOcean. Сам управляющий софт может разворачиваться на любой инфраструктуре. Соответственно можно поставить все на свое железо или воспользоваться предложением одного из [провайдеров-партнёров](https://jelastic.cloud/), датацентры которых расположены по всему миру. Я пользуюсь вторым вариантом — часть проектов работает в ДЦ в Нидерландах, часть — в РФ.

Когда контейнеры были еще не модными, а приложения нужно было деплоить через залив варника на FTP и последующий рестарт, Jelastic давал возможность триггерить деплой из svn. Сейчас я понимаю что в то время это был полный космос, по сравнению с тем, как деплоил остальной мир.

Потом появился Docker и Jelastic начал поддерживать и его.

Как я уже говорил — в платформе есть большое количество предконфигурированных приложений — базы данных, аппликейшен сервера, эластиксёрч и тд, сотни их. Это позволяет в один клик поднять например инстанс базы, то есть такой себе аналог RDS. Так же есть [маркетплейс](https://docs.jelastic.com/marketplace) где представлена куча прикладного софта, в том числе предконфигурированные кластера баз данных (PostgreSQL, MySQL, MariaDB, MongoDB), предконфигурированный кластер Kubernetes, Jenkins, GitLab, Wordpress, Docker Container Registry и так далее. Я этим всем не пользовался, поэтому не могу сказать как оно, но собираюсь потестить кластера баз данных.[^5]

{{< figure src="/images/jelastic/2.png" title="Куча предконфигурированных приложений" >}}

Каждое окружение можно склонировать одной кнопкой, при этом будут сохранены данные и конфигурация — удобно для бекапа и для разворачивания тестовых окружений.

### Теперь о том, как этим всем пользуюсь конкретно я:

Итак, у меня есть проект, традиционно он состоит из серверов приложений и базы данных. Сервера приложений — это stateless контейнеры развёрнутые из Docker-образов. У меня создано два идентичных окружения, в каждом из которых есть по одной ноде с приложением. Перед ними установлен балансировщик (это встроенная штука которая есть в Jelastic) который работает по round-robin и делает SSL терминацию. Сбоку крутится еще одно окружение с MariaDB. Такая же конфигурация настроена для dev окружения.

{{< figure src="/images/jelastic/3.jpg" title="\"Архитектура\"" >}}

Как это все деплоится? База данных и балансировщик создаются вручную и далее не меняются, сервера приложений разворачиваются из Docker образов которые собирает GitLab CI (раньше Jenkins). В панели можно указать путь к приватному хранилищу, подсунуть ему пароли, и Jelastic сам будет подтягивать доступные версии образов. Редеплой можно делать [вручную через кнопочку](https://jelastic.com/blog/keep-your-dockerized-app-up-to-date-with-one-click-container-redeploy/) или через API. Раньше у меня был настроен автоматический редеплой (используя API) но потом что-то сломалось, и сейчас я, к своему стыду, деплою кнопочкой. Благо, это надо делать нечасто, но каждый раз у меня никак не хватает мотивации написать скрипт. Shame on me.

Важная штука — в отличие от, например, ECS или K8S с Docker-драйвером, ваши контейнеры это не машины на которых запущен Docker и какой-то оркестрационный софт. Насколько я понял, Jelastic берет Docker образ и буквально разворачивает его в физическую файловую систему, то есть ваш контейнер — не контейнер, а живая машина. С этим связано несколько особенностей, в частности то, что контейнеры не являются эфемерными и при деплое нового образа не теряются старые данные. Пути к файлам, которые нужно сохранять при редеплое, можно конфигурировать[^7].

На каждую машину можно [заsshиться](https://jelastic.com/blog/ssh-to-container/) и посмотреть что там внутри, поделать какие-то команды или подкрутить конфигурацию.

Из [UI](https://docs.jelastic.com/dashboard-guide) так же можно походить по файловой системе, посмотреть статистику использования ресурсов и логи (которые автопрокручиваются). Логи довольно удобная вещь если что-то сломалось и надо быстро посмотреть.

### С какими проблемами я столкнулся за годы эксплуатации?

Все проблемы, с которыми я сталкивался в основном связаны с надежностью хостинг-провайдера, то есть это не проблемы софта Jelastic. Конкретно мой провайдер (reg.ru) страдает кратковременными отвалами DNS раз в месяц.

### Шо по деньгам? 

Значительно дешевле амазона. За всю инфру мы платим примерно 300$, это за 20-30 серверов. Ресурсы гипервизор выделяет автоматически в зависимости от потребностей приложения, как оно там работает внутри не знаю но в целом ок.

### Чего мне не хватает?

Jelastic это что-то посредине между full-managed платформой как Heroku и IaaS как AWS/CGP/Azure/и тд.

Если говорить про первый подход, то мне капитально не хватает простоты деплоя и managed баз данных. В Heroku все сильно проще (но и дороже).

Если про второй — то не хватает дополнительных сервисов типа стореджа, очередей, лямбд (можно реализовать, используя Jelastic [Cloud Scripting](https://docs.cloudscripting.com/), но нужно разбираться), то есть Jelastic — это больше про Compute и приложения из маркетплейса.

CLI/API очень топорный и не возникает желания ним пользоваться. Я даже было хотел начать писать своего клиента, более человеческого, но потом забил на эту историю.

### Рекомендую ли я вам эту штуку? 

Если бюджет сильно ограничен то можно выбрать дешевого провайдера и платить за всю инфру десяток евро. Так же некоторых может заинтересовать быстрое разворачивание K8S кластера[^8]. В остальном для простых вещей мне гораздо больше нравится Heroku, для сложных — уже хочется вращать терраформом амазон.

Ещё Jelastic очень удобен для создания инстансов баз данных. Такой себе RDS на минималках, но при этом стоит копейки.

Мир идет в сторону кубера, кубер можно развернуть и на Jelastic (там прям кнопочка есть), но я не пробовал. Вообще мне кубер сильно не нравится, но про это потом.

### Послесловие

На мой взгляд Jelastic очень сильно опередил своё время, но по какой-то причине не получил хорошего traction внутри сообщества, несмотря на маркетинговые усилия компании. Насколько я понимаю, они неплохо себя чувствуют в энтерпрайзе, но вот так чтобы на рандомном девопс митапе спросить человека — “ты знаешь про Jelastic?” я думаю что 95% ответят “что? ElasticSearch?”

Вот такая история, задавайте ответы в комментариях + я уверен что сюда придут ребята из Jelastic и добавят что-то сверху.

[^1]: [JavaDay Kiev 2014 Keynote](https://youtu.be/NrKgO6w6QUI?list=PLlhpyJD4TzMbYWHgSJb2kydmCMnem6YIk&t=6615)
[^2]: [https://www.virtuozzo.com/][https://www.virtuozzo.com/]
[^3]: [Automatic Vertical Scaling](https://docs.jelastic.com/automatic-vertical-scaling)
[^4]: [Automatic Horizontal Scaling](https://jelastic.com/blog/horizontal-scaling-of-cloud-environments-stateless-stateful-automatic)
[^5]: [MariaDB/MySQL Auto-Сlustering with Load Balancing and Replication for High Availability and Performance](https://jelastic.com/blog/mysql-mariadb-database-auto-clustering-cloud-hosting/)
[^6]: [https://www.virtuozzo.com/](https://www.virtuozzo.com/)
[^7]: [Container Redeploy](https://docs.jelastic.com/container-redeploy#save-data)
[^8]: [Kubernetes Cluster Setup with Automated Scaling and Pay-per-Use Pricing](https://jelastic.com/blog/kubernetes-cluster-scaling-pay-per-use-hosting/)