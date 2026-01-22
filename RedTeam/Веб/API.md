База знаний: [API testing](https://portswigger.net/web-security/learning-paths/api-testing/api-testing-api-recon/api-testing/api-recon) 
## API recon
- Последовательность:
	1. Изучение документации
	2. Изучение входных данных (обязательные и необязательные параметры)
	3. Изучение принимаемых методов HTTP и типов данных
	4. Изучение ограничений числа запросов и наличие аутентификации
- Machine-readable API docs: Burp Scanner, OpenAPI Parser BApp, JS Link Finder BApp
## Тестирование API
- ### Подходы
	- Менять HTTP-методы
	- Менять типы файлов (Content-Type)
	- Менять конечные точки API в URL: вместо `PUT /api/user/update` обратиться к `PUT /api/user/delete`, `PUT /api/user/add`
	- Mass assignment (добавление параметров в запрос, которые нельзя добавить через формы): https://learn.snyk.io/lesson/mass-assignment/, https://portswigger.net/web-security/learning-paths/api-testing/api-testing-mass-assignment-vulnerabilities/api-testing/identifying-hidden-parameters, https://portswigger.net/web-security/learning-paths/api-testing/api-testing-mass-assignment-vulnerabilities/api-testing/testing-mass-assignment-vulnerabilities
	- API server side parameter pollution (происходит, если параметры из внешнего API передаются во внутренний API без обработки): https://portswigger.net/web-security/learning-paths/api-testing/api-testing-testing-for-server-side-parameter-pollution-in-the-query-string/api-testing/server-side-parameter-pollution/truncating-query-strings, https://portswigger.net/web-security/learning-paths/api-testing/api-testing-testing-for-server-side-parameter-pollution-in-the-query-string/api-testing/server-side-parameter-pollution/injecting-invalid-parameters, https://portswigger.net/web-security/learning-paths/api-testing/api-testing-testing-for-server-side-parameter-pollution-in-the-query-string/api-testing/server-side-parameter-pollution/injecting-valid-parameters, https://portswigger.net/web-security/learning-paths/api-testing/api-testing-testing-for-server-side-parameter-pollution-in-the-query-string/api-testing/server-side-parameter-pollution/overriding-existing-parameters
	- API server side parameter pollution in path (если параметры указываются в пути): запрос `GET /edit_profile.php?name=peter%2f..%2fadmin` (на стороне сервера `GET /api/private/users/peter/../admin`) будет превращен на стороне сервера в `GET /api/private/users/peter/admin` ([Testing for server-side parameter pollution in REST](pathshttps://portswigger.net/web-security/learning-paths/api-testing/api-testing-testing-for-server-side-parameter-pollution-in-rest-paths/api-testing/server-side-parameter-pollution/testing-for-server-side-parameter-pollution-in-rest-paths))
	- API server side parameter pollution in structured data formats (JSON, XML): `POST /myaccount name=peter","access_level":"administrator`, `POST /myaccount {"name": "peter\",\"access_level\":\"administrator"}` ([Testing for server-side parameter pollution in structured data formats](https://portswigger.net/web-security/learning-paths/api-testing/api-testing-testing-for-server-side-parameter-pollution-in-structured-data-formats/api-testing/server-side-parameter-pollution/testing-for-server-side-parameter-pollution-in-structured-data-formats))
- ### Инструменты тестирования API
	- Postman
	- SoapUI
	- Burp Intruder со встроенным листом HTTP verbs (замена методов)
	- Content type converter BApp (смена типов данных)
	- Param miner BApp (перебор параметров)
	- Backslash Powered Scanner BApp (server-side injection vulnerabilities)