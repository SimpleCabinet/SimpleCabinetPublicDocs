# SimpleCabinetPublicDocs
Документация к SimpleCabinet 2. Все компоненты SimpleCabinet после релиза будут доступны под лицензией MIT с открытым исходным кодом  
**Первая стадия релиза**: Релиз SimpleCabinetWebAPI, SimpleCabinetModule и плагина Bukkit  
**Вторая стадия релиза**: Релиз плагина для Sponge  
**Третья стадия релиза**: Релиз mini версии frontend части, реализующий минимальный функционал  
**Четвертая стадия релиза**: Релиз полной версии frontend части  
# Структура проекта
- [SimpleCabinetWebAPI](https://github.com/SimpleCabinet/SimpleCabinetWebAPI) - Основной бекенд SimpleCabinet. Занимается подключением к базе данных и обработкой всех запросов по HTTP API
- [SimpleCabinetModule](https://github.com/SimpleCabinet/SimpleCabinetModule) - Модуль для лаунчсервера, предоставляющий SimpleCabinetAuthCoreProvider
- [SimpleCabinetBukkitPlugin](https://github.com/SimpleCabinet/SimpleCabinetBukkitPlugin) - Плагин Bukkit 1.7 - 1.17+, добавляющий команды `/shop` и `/economy` для получения предметов и торговли
- [SimpleCabinetFrontend2](https://github.com/SimpleCabinet/SimpleCabinetFrontend2) - фронтенд часть SimpleCabinet 2
- **Все остальные части SimpleCabinet 2 находятся на ранних стадиях разработки**
# Требования
- Nginx для раздачи файлов
- GravitLauncher 5.2.11 и выше
- Java 17 и выше
- PostgreSQL в качестве основной базы данных
- Миграция: любая CMS с базой данных MySQL/MariaDB и одним из поддержиываемых алгоритмов хеширования пароля: BCRYPT, DOUBLEMD5, MD5, SHA256, AUTHMESHA256, PHPASS
- Выдача привилегий: LuckPerms
- Выдача предметов: сервер на Sponge или Bukkit
# FAQ
- **Почему SimpleCabinet 2 не поддерживает MySQL?**  
  - Простота установки: выбор PostgreSQL делает установку проще, понятнее и прямолинейнее
  - Оптимизация: PostgreSQL имеет множество функций, которых нет в большистве популярных версий MySQL. Некоторые функции отсутствуют в MySQL вовсе
  - Обновление до новых версий: обновление БД теперь более предсказуемо и не требует ручных действий
- **У меня есть плагины, которые работают только с MySQL. Что делать?**
  - Вы по прежнему можете пользоватся плагинами, работающими только с MySQL. Функционал экономики и магазина блоков и привилегий предоставляет SimpleCabinet, а для прав используется LuckPerms который поддерживает PostgreSQL
- **Как обновится с SimpleCabinet 1**
  - Вам понадобится установить лаунчсервер версии 5.1.10 и **последний** модуль SimpleCabinet 1. После чего вы должны ввести команду `cabinet upgrade2` **после** установки web части SimpleCabinet 2. Пользователи будут перенесены в новую БД
# Установка PostgreSQL
- Установите пакет `postgresql-server`(Debian) и запустите сервер `systemctl start postgresql`
- Выполните `su - postgres bash -c "psql"`
- Создайте пользователя и базу данных:
```sql
create user cabinet2 with password 'MYPASSWORD';
create database cabinet2;
grant all privileges on database cabinet2 to cabinet2;
```
- Выйдите из консоли с помощью `\q`
- Откройте файл `/etc/postgresql/11/main/pg_hba.conf`, найдите строчки:
```
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
# IPv6 local connections:
host    all             all             ::1/128                 trust
```
и измените `trust` на `md5`. Перезапустите сервер `systemctl restart postgresql`
# Установка - Часть 1: Web
- Подготовьте окружение: создайте отдельного пользователя `cabinet` от имени которого будет работать SimpleCabinet 2
- Скачайте файл `web-XXXX.jar` и запустите его для появления файла `application.properties`
- Настройте подключение к базе данных и прочие параметры
- Запустите `web-XXXX.jar` повторно с помощью screen и убедитесь, что данные от БД указаны верно.
- Выполните команду начальной настройки: `curl http://localhost:PORT/setup > setup.json`. Эта команда создаст пользователя admin с случайным паролем и вечный токен(adminJwtToken) который понадобится для привязки лаунчсервера и серверов к SimpleCabinet
# Установка - Часть 2: Module
- Скачайте модуль для лаунчсервера `SimpleCabinetModule.jar` и положите его в папку modules лаунчсервера
- Выполните `cabinet install URL TOKEN`. Где URL - адрес кабинета, а TOKEN - токен доступа, полученный на предыдущем этапе. Команда установки проверит правильность указанных данных и **заменит** ваш старый AuthCoreProvider новым
# Установка - Часть 3: Frontend
- Клонируйте репозиторий фронтенд-части Simple Cabinet 2в любую удобную для вас папку
- Отредактируйте файл `.env` указав в качестве URL внешний адрес WebAPI кабинета
- Установите `yarn`
- Установите quasar-cli командой `yarn global add @quasar/cli`
- Соберите проект командой `quasar build`
# Установка - Часть 4: Nginx
**Этот раздел еще не готов**
# Функции SimpleCabinet 2
В данном разделе перечислены функции, которые **будут доступны при релизе web части**
### Авторизация
В SimpleCabinet 2 используется авторизация с помощью AuthCoreProvider и OAuth, что позволяет не хранить пароль на стороне лаунчера и браузера
### Функции администратора
- Модерирование(бан/разбан), просмотр банлиста доступен всем желающим
- Управление экономикой(курсы валют, начисление монет игрокам)
- Управление HWID(поиск мультиаккаунтов/бан)
- Другие функции
### Личный кабинет
- Восстановление забытого пароля по email
- Смена пароля, статуса и  личных данных
- Включение и выключение 2FA
- Выход с всех устройств, просмотр активных сессий
### Система экономики
- У каждого пользователя есть один или несколько счетов в разных валютах
- Пользователь может переводить монеты с своих счетов на счета других игроков если курс между валютой игрока и целевой валютой определен администратором
- Администратор может вручную или с помощью скриптов и плагинов добавлять, удалять и менять курсы валют
- Плагин на стороне сервера может инициировать любую операцию с валютами(пополнение/снятие/перевод)
- В системе может быть сколько угодно валют и курсов между ними
- Курсы может "Валюта 1 -> Валюта 2" и "Валюта 2 -> Валюта 1" могут отличатся. Могут существовать курсы только в одном направлении
- Курс "Валюта 1 -> Валюта 1" имеет особое значение - разрешает переводы в этой валюте между игроками
- Алгоритм проверит можно ли "дюпнуть" монеты путем переводов между валютами, и если такое возможно заблокирует изменение курса
- Все транзакции(пополнение, снятие, переводы) логгируются в таблице транзакций.
### Магазин привилегий
- Выдача привилегий с помощью LuckPerms
- Поддержка скидок, продлений, ограничений и т.д.
- Поддержка покупки на разные сервера
### Магазин блоков
- Выдача блоков с помощью плагинов
- Поддержка скидок, ограничений и т.д.
- Поддержка покупки на разные сервера
- Поддерживается указание: названия или ID предмета, extra предмета, зачарований, nbt.
- Экспериментальная(необязательная для реализации в frontend) возможность кастомизации покупаемых предметов
### Магазины других услуг
SimpleCabinet 2 предоставляет возможность легкого добавления новых видов товаров
