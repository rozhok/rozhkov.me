---
title: "Дьявол в деталях"
date: 2020-04-28T08:40:56+03:00
lastmod: 2020-04-28T08:40:56+03:00
tags: ["work"]
---

Чтобы отдохнуть от работы и не забыть удовольствие от разработки, я пилю веб-читалку телеграм каналов (прототип показывал на одном из стримов). Зачем оно надо, если можно читать в телеграме? Ну, во-первых, изначальная задача—прочитать канал от корки до корки в хронологическом порядке без необходимости скроллить полчаса вверх, а во-вторых—не напрягать глаза мелкими буквами. На стоящие каналы я попадаю редко, но если попадаю, то сразу вспоминаю о своем прототипе и читаю через него. Вот вчера опять нашел интересный канал и решил чуть подпилить продукт.

Так вот, задумал я отображать дату публикации. Казалось бы, делов на одну минуту—просто добавить див со спаном и дело в шляпе. Однако не всё так просто! Ведь дата хранится у нас в UTC, соответственно, если просто так показать её пользователю, то будет разница во времени, что не есть хорошо. Окей, давайте посмотрим, что для этого придумало сообщество, так, "rails display time in user timezone", так, падажи, ага, нам предлагают добавить клиентский js который будет после отрисовки страницы брать таймзону у браузера и форматировать соответствующим образом наше поле. Есть готовая библиотека от бейзкемпа, иду смотреть что там, так, не, не хочу. Тащить кучу JS чтобы потом у меня мигали поля после рендера—отказать. Наверное, библиотека используется в каких-то инпутах, короче которые не обломно перерисовать. Но мне такой способ не понравился. Смотрим дальше, оказывается нормального способа понять на сервере что за таймзона у человека нет! Или вычисляйте по IP, или добавляйте настроечку, сохраняйте её в печеньку и потом читайте. Но я-то хотел чтобы человек просто зашел и увидел время в своей таймзоне… Можно делать, например, асинхронный запрос на страницу, где передавать таймзону из браузера, сохранять её в пользовательские настройки и при последующих отрисовках использовать её. Но это и все другие решения—какие-то лютые костыли или их комбинации, суммарный код которых будет больше того, что я уже написал для проекта.

Спустя час изысканий я забил, и решил оставить время в UTC.

Казалось бы микроскопическая и простая задача потянула за собой целый ворох проблем и вопросов, раздумья над которыми отвлекают от разработки основной функциональности.

А вы потом спрашиваете, почему так долго кнопочку добавить. Да тут на одном поле с датой можно легко день потратить!

Конечно, наверное я пойду по пути асинхронного запроса и сохранения настроек, и в следующий раз, когда буду делать похожую штуку, то уже вспомню про опробованный подход, и уменьшу время на разработку. 

Но из таких мелочей и состоит наша повседневная жизнь. Кнопка там, поле тут, вёрстка вылазит сям, вот и день подошёл к концу, вроде и работал, а ничего не сделал.
