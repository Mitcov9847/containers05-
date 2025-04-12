# Отчет о выполнении работы

## Цель работы
Цель работы заключается в создании и настройке Docker контейнера, который будет включать Apache, PHP, MariaDB и WordPress. Работа также требует настройки конфигурационных файлов, создания необходимых скриптов и проверки работоспособности сайта WordPress в контейнере.

---

## 1. Создание репозитория `containers05`

Вначале был создан репозиторий с именем `containers05`, куда были добавлены все необходимые файлы и конфигурации для выполнения данной работы.

---

## 2. Создание папки `files` с файлами конфигурации `apache2`, `php`, `mariadb`

В рамках работы были созданы следующие папки внутри репозитория:
- `files/apache2/` для файлов конфигурации Apache2.
- `files/php/` для файлов конфигурации PHP.
- `files/mariadb/` для файлов конфигурации MariaDB.

Затем из контейнера были скопированы следующие файлы конфигурации:
- `/etc/apache2/sites-available/000-default.conf` в `files/apache2/`.
- `/etc/apache2/apache2.conf` в `files/apache2/`.
- `/etc/php/8.2/apache2/php.ini` в `files/php/`.
- `/etc/mysql/mariadb.conf.d/50-server.cnf` в `files/mariadb/`.

![Снимок экрана 2025-04-12 183508](https://github.com/user-attachments/assets/af523b6e-455b-4586-b316-88eb6f9c124a)

---

## 3. Создание файла `supervisord.conf`

Был создан файл конфигурации для Supervisor — `supervisord.conf`. Этот файл необходим для управления процессами Apache2 и MariaDB внутри контейнера. В нем указаны параметры для автозапуска и перезапуска обоих сервисов.

![image](https://github.com/user-attachments/assets/1449f182-7a14-40ab-837f-6f5aa0916ba7)

---

## 4. Создание файла `wp-config.php`

В процессе выполнения работы был создан файл конфигурации WordPress — `wp-config.php`. Этот файл был настроен для подключения к базе данных WordPress, используя параметры:
- Имя базы данных: `wordpress`
- Имя пользователя: `wordpress`
- Пароль: `wordpress`
- Адрес сервера базы данных: `localhost`

Файл был добавлен в папку `files/` и впоследствии скопирован в контейнер.

![image](https://github.com/user-attachments/assets/7b531789-5d06-43b2-bb83-05ed04f6da1b)

---

## 5. Создание файла `Dockerfile`

Был создан Dockerfile, который описывает процесс сборки Docker образа с необходимыми сервисами:
- Установка Apache2, PHP, MariaDB и Supervisor.
- Монтирование томов для данных MySQL и логов.
- Добавление файлов WordPress и конфигураций из папки `files/`.
- Запуск Supervisor для управления процессами.

![image](https://github.com/user-attachments/assets/127a0994-f9d9-43c1-b25c-22f4c32f316c)

---

## 6. Настройка конфигурационных файлов

### Конфигурационный файл Apache2

1. Открыл файл `files/apache2/000-default.conf`.
2. Заменил строку `#ServerName www.example.com` на `ServerName localhost`.
3. Изменил строку `ServerAdmin webmaster@localhost` на свой адрес электронной почты.
4. Добавил строку `DirectoryIndex index.php index.html` после строки `DocumentRoot /var/www/html/`.
5. В конце файла `files/apache2/apache2.conf` добавил строку `ServerName localhost`.

### Конфигурационный файл PHP

1. Открыл файл `files/php/php.ini`.
2. Заменил строку `;error_log = php_errors.log` на `error_log = /var/log/php_errors.log`.
3. Настроил параметры:
    - `memory_limit = 128M`
    - `upload_max_filesize = 128M`
    - `post_max_size = 128M`
    - `max_execution_time = 120`

### Конфигурационный файл MariaDB

1. Открыл файл `files/mariadb/50-server.cnf`.
2. Раскомментировал строку `#log_error = /var/log/mysql/error.log`.

---

## 7. Создание базы данных и пользователя для WordPress

1. Создал базу данных `wordpress` и пользователя `wordpress` с паролем `wordpress` в контейнере MariaDB.
2. Выполнил следующие команды:
    ```sql
    CREATE DATABASE wordpress;
    CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'wordpress';
    GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

---

## 8. Добавление файла конфигурации WordPress

1. Открыл сайт WordPress по адресу `http://localhost/`.
2. Указал параметры подключения к базе данных:
    - Имя базы данных: `wordpress`
    - Имя пользователя: `wordpress`
    - Пароль: `wordpress`
    - Адрес сервера базы данных: `localhost`
3. Скопировал содержимое файла конфигурации в файл `files/wp-config.php` на компьютере.

---

## 9. Итоговый результат, вкладка localhost.

## 10. Ответы на вопросы

1. **Какие файлы конфигурации были изменены?**
   - Файлы конфигурации Apache2 (`000-default.conf`, `apache2.conf`), PHP (`php.ini`), MariaDB (`50-server.cnf`), а также файл конфигурации WordPress (`wp-config.php`).

2. **За что отвечает инструкция `DirectoryIndex` в файле конфигурации Apache2?**
   - Инструкция `DirectoryIndex` указывает, какие файлы должны быть загружены по умолчанию при обращении к каталогу на сервере. В данном случае, это `index.php` и `index.html`.

3. **Зачем нужен файл `wp-config.php`?**
   - Файл `wp-config.php` необходим для подключения WordPress к базе данных и содержит важные настройки, такие как имя базы данных, имя пользователя и пароль.

4. **За что отвечает параметр `post_max_size` в файле конфигурации PHP?**
   - Параметр `post_max_size` определяет максимальный размер данных, которые могут быть отправлены через HTTP POST-запрос. Это важно при загрузке файлов или отправке больших форм.

5. **Какие недостатки могут быть в созданном образе контейнера?**
   - Возможно, контейнер нуждается в улучшении безопасности, например, ограничении прав доступа для базы данных и других сервисов.
   - Контейнер может быть более оптимизирован с точки зрения использования ресурсов.

---

## Выводы

Работа была успешно выполнена. Контейнер с Apache2, PHP, MariaDB и WordPress был создан и настроен. Конфигурационные файлы были изменены в соответствии с требованиями задания. Также был создан файл конфигурации для Supervisor для управления процессами. Работа контейнера была проверена, сайт WordPress был успешно развернут.

---
