# Анатомия пуш нотификаций

Push-уведомление — это короткое сообщение, состоящее из токена девайса, полезной нагрузки (payload) и ещё некоторой информации. 
Полезная нагрузка — это актуальные данные, которые будут отправляться на девайс.

![image](https://user-images.githubusercontent.com/47610132/182320430-59439cd2-50a9-4192-89db-7e008ca0727c.png)

- iOS запрашивает у сервера `Apple Push Notification Service (APNS)` токен девайса. Приложение получает токен девайса. 
Можно считать, что токен – это адрес для отправки push-уведомлений. Приложение отправляет токен девайса на ваш сервер. 
Когда произойдёт какое-либо событие для вашего приложения, сервер отправит push-уведомление в APNS. APNS отправит push-уведомление на девайс пользователя.

- Когда пользователь получит push-уведомление, появится сообщение, и/или будет воспроизведён звуковой сигнал, и/или обновится бейдж на иконке приложения. 
Пользователь может открыть приложение из уведомления. Приложение получит контент push-уведомления и сможет обработать его.

Ваш сервер должен преобразовать полезную нагрузку в JSON-словарь. Полезная нагрузка для простого push-сообщения выглядит следующим образом:

```{ "aps": { "alert": "Hello, world!", "sound": "default" } }```

> Полезная нагрузка — это словарь, который состоит из, по крайней мере, одной пары «ключ-значение» «aps», значение которой само по себе является словарём. 
> В примере выше «aps» содержит два поля: «alert» и «sound». Когда на девайс придёт push-уведомление, отобразится всплывающее сообщение 
> с текстом «Hello, world!» и будет воспроизведён стандартный звуковой сигнал.

![image](https://user-images.githubusercontent.com/47610132/182321644-1d6ed4b9-c1ee-40a0-af50-d1d646607573.png)

# Полезное:
- [Когда почта доставляет: боремся с потерями push-уведомлений в iOS / Ася Свириденко](https://www.youtube.com/watch?v=SVCMbPIuy8w&t=1471s)
- [Тестирование push-уведомлений в мобильных приложениях](https://habr.com/ru/company/youla/blog/553762/)
- [The Push Notifications primer](https://www.wwdcnotes.com/notes/wwdc20/10095/)
