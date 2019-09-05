# Docker - Bitrix environment: 

Docker окружение для Bitrix сайтов

## Краткое описание на примере разворачивания окружения по умолчанию

- **Спуливаемся**
- **Вносим в host файл соответствия ip и локального домена**
    ```
    127.0.0.151 site1.loc
    127.0.0.151 site2.loc
    ```
- **Открываем консоль и переходим в директорию, куда спулили репу**
- **Вводим команду**
    ```
    docker-compose up -d --build
    ```
- **Проверяем в браузере, что сайты site1.loc и site2.loc корректно открываются**
- **Радуемся результату**

## Подробное описание на примере разворачивания окружения для одного сайта mysite.loc

- **Создаём директорию mysite и заходим в неё**
- **Спуливаемся**
- **Вносим в host файл соответствия ip и локального домена (ip адрес придуман от балды)**
    ```
    127.0.0.3 mysite.loc
    ```
- **Переименовываем директорию mysite/www/site1.loc в mysite/www/mysite.loc, а mysite/www/site2.loc - удаляем**
- **В файле .env устанавливаем переменным следующие значения:**
    ```
    APP_IPPORT_HOST=127.0.0.3:80
    APP_IPPORT_ADMINER_HOST=127.0.0.3:81
    ```
- **В файле sites.conf оставляем следующий код**
    ```
    <VirtualHost *:80>
        ServerAdmin admin@test.com
        ServerName mysite.loc
        ServerAlias www.mysite.loc
        DocumentRoot /var/www/html/mysite.loc/public_html
        ErrorLog /var/www/html/mysite.loc/apache_logs/error.log
        CustomLog /var/www/html/mysite.loc/apache_logs/access.log combined
    
        <Directory /var/www/html/mysite.loc/public_html>
            #Разрешение на перезапись всех директив при помощи .htaccess
            AllowOverride All
        </Directory>
        
        <Directory /var/www/html/mysite.loc/public_html>
            Options FollowSymLinks
            AllowOverride None
        </Directory>
    </VirtualHost>
    ```
- **Открываем консоль и переходим в директорию mysite**
- **Вводим команду**
    ```
    docker-compose up -d --build
    ```
- **Проверяем в браузере, что сайт mysite.loc корректно открываются**
- **Проверяем в браузере, что Adminer, соответствующий mysite корректно открывается по следующему адресу**
    ```
    http://mysite.loc:81
    ```
- **Для доступа к БД необходимы следующие логин и пасс (настраивается в .env)**
    ```
    login = root
    pass = pass123
    ```
- **Важно: при установке Битрикса в качестве адреса хоста БД нужно указать не localhost, а db, т.к. доступ к контейнеру БД из контейнера web осуществляется по имени контейнера (db)** 
- **Радуемся результату**

## Файлы и директории для настройки и разворачивания окружения
- **.env - файл основных настроек**
    ```
    Здесь зафиксированы такие настройки, как IP адрес, порт на котором работает окружение,
    adminer, и пр.
    ```
- **docker-compose.yml**
    ```
    Содержит описание контейнеров на основе переменных в .env
    ```
- **директория www**
    ```
    Общая директория хоста, примонтированная к файловой системе контейнера.
    Содержит файлы сайтов в рамках окружения и их логи
    Структура следующая:
    - www
        - site1.loc
            - apache_logs #логи апача site1
            - public_html #файлы site1
        - site2.loc
            - apache_logs #логи апача site2
            - public_html #файлы site2
        ...
    По умолчанию в окружении крутятся 2 сайта: 
    - site1.loc
    - site2.loc
    ``` 
- **директория web**
    ```
    Содержит следующие файлы:
    - bx.ini: в нём прописаны директивы для php.ini для корректной работы Bitrix
    - Dockerfile: инструкция для запуска контейнера на основе image-а PHP.
    По умолчанию используется php:7.1-apache
    - sites.conf: файл описания виртуальных хостов для Apache.
    ```
- **директория databases**
    ```
    Общая директория хоста, примонтированная к файловой системе контейнера.
    Содержит БД в рамках запущенного окружения.
    ```