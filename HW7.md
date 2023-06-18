## 1. Установить Nginx и настроить его на работу с PHP-FPM.
```sh
sudo apt install nginx 
sudo apt install php8.1-fpm
sudo nano /etc/php/8.1/fpm/pool.d/www.conf
```
Раскомментируем или добавляем эти строки.
```
listen = /run/php/php8.1-fpm.sock
listen.owner = www-data
listen.group = www-data
listen.mode = 0660
```
```sh
sudo nano /etc/nginx/sites-available/default
```
Внутри блока server добавляем следующее:
```
location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/run/php/php8.1-fpm.sock;
}
```
```sh
sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
sudo systemctl reload php8.1-fpm
```
## 2. Установить Apache. Настроить обработку PHP. Добиться одновременной работы с Nginx.
```sh
sudo apt install apache2
apt install php8.1 libapache2-mod-php8.1 
sudo nano /etc/apache2/mods-enabled/dir.conf
```
Меняем последовательность на такую
```
<IfModule mod_dir.c>
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
```sh
sudo nano /etc/apache2/ports.conf
```
Заменяем Listen 80 на Listen 8080, порт 80 занят nginx
```sh
sudo nano etc/apache2/sites-enabled/000-default.conf
```
Заменяем
```
 <VirtualHost *:80> на <VirtualHost *:8080>
 ```
```sh
cd /var/www/html
sudo su
cat > info.php
```
<?php
phpinfo();
?>
Ctrl+D
```sh
systemctl reload apache2
```
## 3. Настроить схему обратного прокси для Nginx (динамика - на Apache).
```sh
nano etc/nginx/sites-enabled/default
```
Добавляем 
```
location / {
    proxy_pass http://localhost:8080;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
}
location ~* ^.+.(jpg|jpeg|gif|png|ico|css|zip|pdf|txt|tar|js)$ {
    root /var/www/html;
}
```
Удаляем 
```
location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    root /var/www/html;
    fastcgi_pass unix:/run/php/php8.1-fpm.sock;
}
```
## 4. Установить MySQL. Создать новую базу данных и таблицу в ней.
```sh
sudo apt install mysql-server-8.0
sudo mysql
```
```mysql
CREATE DATABASE coffee;
USE coffee;
CREATE TABLE coffees (
id INT PRIMARY KEY AUTO_INCREMENT,
name VARCHAR(255) NOT NULL,
description TEXT,
price DECIMAL(10, 2) NOT NULL,
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
INSERT INTO coffees (name, description, price) VALUES ('Espresso', 'Strong and concentrated coffee', 2.99);
SELECT * FROM coffees;
```
## 5. Установить пакет phpmyadmin и запустить его веб-интерфейс для управления MySQL.
```sh
sudo apt install phpmyadmin
```
Выбираем при установке apache2 и вводим пароль
```sh
sudo nano /etc/apache2/apache2.conf
Добавляем строку в конец файла
# phpMyAdmin Configuration
Include /etc/apache2/conf-available/phpmyadmin.conf
```
```sh
nano etc/nginx/sites-enabled/default
```
Удаляем
```
location ~* ^.+.(jpg|jpeg|gif|png|ico|css|zip|pdf|txt|tar|js)$ {
    root /var/www/html;
}
```
```sh
nano /etc/phpmyadmin/apache.conf 
```
Меняем 
```
<IfModule mod_php7.c> на <IfModule mod_php8.c>
```
```sh
cat > /etc/apache2/conf-available/phpmyadmin.conf
Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
    Options +FollowSymlinks
    DirectoryIndex index.php
    AllowOverride All
</Directory>
Ctrl+D
a2enconf phpmyadmin
systemctl restart apache2
```
Переходим по адресу http://localhost/phpmyadmin/index.php вводим логин пароль и пользуемся

## 6. Настроить схему балансировки трафика между несколькими серверами Apache на стороне Nginx с помощью модуля ngx_http_upstream_module.
```sh
nano /etc/nginx/nginx.conf
```
Вписываем в блок hhtp
```
upstream apache_servers {
    least_conn;
    server myserver1.com;
    server myserver2.com;
    server myserver3.com;
}
```
Внутри блока сервер
```  
server {
    location / {
        proxy_pass http://apache_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Fo$proxy_add_x_forwarded_for;
    }    
}

```
```sh
sudo systemctl restart nginx
```