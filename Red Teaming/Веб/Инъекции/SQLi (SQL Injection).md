SQL-инъекция - это уязвимость веб-безопасности, позволяющая атакующему изменять запросы, которые приложение отправляет к своей базе данных
## Источники информации
- [SQL injection Topic PortSwigger Academy](https://portswigger.net/web-security/authentication)
- База знаний: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection
## Инструменты
- [sqlmap](https://github.com/sqlmapproject/sqlmap): `sqlmap -u <url> --data <login=1&password=1>`
## Общая информация
- Внедрение выражения `' or 1=1--` делает следующий SQL-запрос `SELECT <columns> FROM <table_name> WHERE <some_column> = '' or 1=1--`, что приводит к получению значений всех строк из таблицы, так как `1=1` всегда `true`
- Атакующий может обойти аутентификацию, если SQL-запрос в таблицу с пользователями уязвим. Внедрение `--` после значения логина позволит обойти проверку пароля (`SELECT * FROM users WHERE username = 'administrator'--' AND password = ''`)
## Виды
### Union-based
1. Проверка возможности SQL инъекций путем вставки специальных символов в значение параметров: `'`, `"`, `\`
2. Если SQL инъекция возможна, необходимо понять, сколько столбцов в результате выполнения запроса: `1 ORDER BY 1/2/3/... <comment symbol: -- ->`
3. Далее используется объединение таблиц: `(-)1 UNION SELECT 1,2,3,4,5,... <comment symbol: -- ->