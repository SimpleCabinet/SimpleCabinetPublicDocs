# SimpleCabinetPublicDocs
Документация к SimpleCabinet. Все компоненты SImpleCabinet после релиза будут доступны под лицензией MIT с открытым исходным кодом  
# Структура проекта
- [SimpleCabinetBackend](https://github.com/SimpleCabinet/SimpleCabinetBackend) - модуль для лаунчсервера, отвечающий за соеденение с базой данных и обработку всех запросов личного кабинета
- [SimpleCabinetFrontend](https://github.com/SimpleCabinet/SimpleCabinetFrontend) - веб интерфейс на Vue.js и bootstrap-vue, отвечающий за взаимодействие с пользователем.  
После сборки является html/js/css SPA страницей. PHP не требуется, веб интерфейс подключается напрямую к лаунчсерверу по websocket
- [SpongeIntegrationPlugin](https://github.com/SimpleCabinet/SpongeIntegrationPlugin) - плагин для Sponge, отвечающий за выдачу предметов в магазине и команду `/cart`
- [BukkitIntegrationPlugin](https://github.com/SimpleCabinet/BukkitIntegrationPlugin) - плагин для Bukkit, отвечающий за выдачу предметов в магазине и команду `/cart`
# Требования
- Nginx для раздачи файлов
- GravitLauncher 5.1.9 и выше
- Рекомендуемые базы данных: PostgreSQL 11 и выше, MariaDB(форк MySQL) 10.3 и выше, при невозможности установки MariaDB - MySQL
- Прочие поддерживаемые базы данных: MS SQL, Oracle и другие
- Миграция: любая CMS с базой данных MySQL/MariaDB и одним из поддержиываемых алгоритмов хеширования пароля: BCRYPT, DOUBLEMD5, MD5, SHA256, AUTHMESHA256, PHPASS. Миграция возможна на любую из поддерживаемых БД
- Выдача привилегий: LuckPerms
- Выдача предметов: сервер на Sponge или Bukkit
# Установка
- Скачайте модуль SimpleCabinetBackend вместе с библиотеками  
Распакуйте библиотеки в libraries лаунчсервера, файл модуля в папку modules  
Перезапустите лаунчсервер для приминения изменений
- Выполните команду `cabinet helper install` для запуска процедуры подключения к базе данных. Вам предложат ввести данные для подключения к вашей базе данных и выбора параметров  
**Если вы используете MariaDB 10.3 и выше - то на вопрос `Are you using MariaDB 10.3 or higher?(Y/N)` после выбора базы данных ответьте Y, в противном случае - N. Вам предложат ввести "диалект", соответствующей версии вашей базы данных(например MySQL8Dialect для MySQL 8). Список диалектов находится [тут](https://github.com/SimpleCabinet/SimpleCabinetBackend#%D1%81%D0%BF%D0%B8%D1%81%D0%BE%D0%BA-%D0%B4%D0%B8%D0%B0%D0%BB%D0%B5%D0%BA%D1%82%D0%BE%D0%B2-%D0%B4%D0%BB%D1%8F-mysql-%D0%B8-mariadb)**. Не пугайтесь если вы увидите ошибки "таблица не найдена" при первом создании таблиц - это нормально. Недостающие таблицы будут созданы автоматически    
Если данные указаны верно вы получите сообщение об успешной установке и auth provider/auth handler/hwid provider будут автоматически заменены на предоставленные SimpleCabinet.  
Выполните `cabinet check` для проверки правильности установки (необязательно)
- Настройте конфигурацию модуля SimpleCabinet в `config/SimpleCabinet/Config.json`, указав группы привилегий, данные платежных систем, почты, разрешенные размеры скинов и прочие настройки
- Скачайте репозиторий SimpleCabinetFrontend и выполните команду `npm install` для установки всех зависимостей
- Измените в файле `src/config.js` данные для подключения к лаунчсерверу, название проекта и прочие параметры, после чего выполните `npm run build` для запуска процесса оптимизированной production сборки.  
По окончанию готовые html/css/js файлы будут лежать в папке `build`  
Для изменения дизайна под себя вы можете запустить отладочный неоптимизированный сервер webpack командой `npm run serve`. Изменения в файлах будут автоматически применены в браузере
- Настройте поддомен вида `cabinet.yourproject.ru` в nginx и вашем dns, указывающий на готовые html/css/js файлы, полученные на предыдущем этапе. Зайдите на этот домен в браузере и проверьте работу SimpleCabinet.
- Настройте основной поддомен лаунчера `launcher.yourproject.ru`, добавив `add_header 'Access-Control-Allow-Origin' 'cabinet.yourproject.ru'` для того, что бы разрешить браузеру забирать скины с этого домена
# Документация компонентов
- Конфигурация бекенда, ручная установка, особенности конкретных баз данных, миграция с других CMS в README модуля [SimpleCabinetBackend](https://github.com/SimpleCabinet/SimpleCabinetBackend)
- Конфигурация фронтенда, гайд для разработчиков дизайна в README фронтенда [SimpleCabinetFrontend](https://github.com/SimpleCabinet/SimpleCabinetFrontend)
- Выполнение запросов к лаунчсерверу на JavaScript в README библиотеки [JavaScriptLauncherAPI](https://github.com/GravitLauncher/JavaScriptLauncherAPI)
# Привязка сервера
- Создайте аккаунт сервера командой `cabinet helper addserver {ServerName}` и скопируйте логин/пароль в конфигурацию ServerWrapper. Права и флаги будут выставлены автоматически
- Установите плагин интеграции на ваш сервер и при необходимости настройте конфигурацию плагина
- Создайте вручную или выполните команду для создания deliveryProvider для доставки предметов игрокам на сервере, указав UUID только что созданного пользователя
# Платежные системы
- **Для всех платежных систем**: Убедитесь что nginx проксирует путь /webapi к лаунчсерверу. Это необходимо для обработки обратных запросов, которые посылаются платежной системой на сайт, сообщая о статусе платежа  
В дальнейшем предпологается что /webapi лаунчсервера находится в `launcher.yourproject.ru/webapi`
- **Убедитесь что вы соответствуете требованиям платежных систем и агрегаторов. Все необходимые картинки вставлены, политики конфиденциальности доступны, вся информация корректна**
## UnitPay
Укажите все данные в разделе конфигурации `payments.unitpay`.  
В личном кабинете UnitPay в параметре `resultUrl` укажите `https://launcher.yourproject.ru/webapi/lk/unitpay`  
Протестируйте работу
## Robokassa
Укажите все данные в разделе конфигурации `payments.robokassa`.  
В личном кабинете Robokassa в параметре `resultUrl` укажите `https://launcher.yourproject.ru/webapi/lk/robokassa`  
Протестируйте работу
