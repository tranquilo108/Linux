Создать два произвольных файла.
```bash
touch {1,2}
```
Первому присвоить права на чтение и запись для владельца и группы, только на чтение — для всех.
```bash
sudo chmod 664 1 
sudo chmod u=rw,g=rw,o=r
 ```
Второму присвоить права на чтение и запись только для владельца.
```bash
sudo chmod 600 2 
sudo chmod u=rw,g=-,o=- 2
```
 Сделать это в численном и символьном виде.
Назначить новых владельца и группу для директории целиком.
```bash
sudo chown -R username:groupname /home/tranquilo/
```

Управление пользователями:
* создать пользователя, используя утилиту useradd и adduser;
``` bash
sudo useradd -s /bin/bash -m -d /home/username username
sudo adduser username - вводим всю информацию которая требуется
```
* удалить пользователя, используя утилиту userdel.
```bash
sudo userdel username
```
Управление группами:
* создать группу с использованием утилит groupadd и addgroup;
```bash
sudo groupadd groupname
sudo addgroup groupname
```
* попрактиковаться в смене групп у пользователей;
```bash
sudo usermod -g groupname username
```
* добавить пользователя в группу, не меняя основной;
```bash
sudo usermod -aG groupname username
```
Создать пользователя с правами суперпользователя.
```bash
sudo usermod -aG sudo username
```
Сделать так, чтобы sudo не требовал пароль для выполнения команд.
```bash
sudo visudo
%sudo ALL=(ALL:ALL) NOPASSWD:ALL
Ctrl+O, Ctrl+X
```
Дополнительные (необязательные) задания:
* Используя дополнительные материалы, выдать одному из созданных пользователей право на выполнение ряда команд, требующих прав суперпользователя (команды выбираем на своё усмотрение).
```bash
sudo visudo
#User privilege specification
username ALL=(ALL) NOPASSWD: /sbin/reboot, /sbin/shutdown
Ctrl+O, Ctrl+X
```
* Создать группу developer и нескольких пользователей, входящих в неё.
```bash
sudo groupadd developer
sudo adduser user1
sudo adduser user2
sudo usermod -g developer user1
sudo usermod -g developer user2
```
Создать директорию для совместной работы.
```bash
sudo mkdir shared_directory
sudo chown :developer shared_directory
```
Сделать так, чтобы созданные одними пользователями файлы могли изменять другие пользователи этой группы.
```bash
sudo chmod 770 shared_directory
sudo chmod g+s shared_directory
```
* Создать в директории для совместной работы поддиректорию для обмена файлами, но чтобы удалять файлы могли только их создатели.
```bash
sudo mkdir file_exchange
sudo chmod +t file_exchange
```
* Создать директорию, в которой есть несколько файлов. Сделать так, чтобы открыть файлы можно было, только зная имя файла, а через ls список файлов посмотреть было нельзя.
```bash
sudo mkdir files
touch {files/1,files/2,files/3}
sudo chmod ugo-rw+x files
```
Результат:
Текст команд, которые применялись при выполнении задания. Присылаем в формате текстового документа: задание и команды для решения (без вывода). Формат - PDF (один файл на все задания).
