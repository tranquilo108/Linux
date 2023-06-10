# Задание:
## 1. Подключить дополнительный репозиторий на выбор: Docker, Nginx, Oracle MySQL. Установить любой пакет из этого репозитория.
```bash
sudo su
cat > /etc/apt/sources.list.d/docker.list
deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable
Ctrl+D
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
apt install docker-ce
```
## 2. Установить и удалить deb-пакет с помощью dpkg.
```bash
sudo wget http://archive.ubuntu.com/ubuntu/pool/universe/x/xzgv/xzgv_0.9.2-2build1_amd64.deb
sudo dpkg -i xzgv_0.9.2-2build1_amd64.deb
sudo dpkg -r xzgv
```
## 3. Установить и удалить snap-пакет.
```bash
snap install gimp 
snap remove gimp
```
## 4. Добавить задачу для выполнения каждые 3 минуты (создание директории, запись в файл). 
```bash
crontab -e
```
Выбираем редактор. Я выбрал nano

*/3 * * * * mkdir -p /home/tranquilo/newdir && echo "Cron молодец " >> /home/tranquilo/newdir/data.txt

Ctrl+O

Ctrl+X

## 5. * Подключить PPA-репозиторий на выбор. Установить из него пакет. Удалить PPA из системы.
```bash
sudo add-apt-repository ppa:numix/ppa
sudo apt update
sudo apt install numix-gtk-theme
sudo apt remove numix-gtk-theme
sudo add-apt-repository --remove ppa:numix/ppa
```
## 6. * Создать задачу резервного копирования (tar) домашнего каталога пользователя. Реализовать с использованием пользовательских crontab-файлов.
```bash
crontab -e
```
0 1 * * * tar -czf /home/tranquilo/backup.tar.gz /home/tranquilo/

Ctrl+O

Ctrl+X

Результат:
Текст команд, которые применялись при выполнении задания. При наличи: часть конфигурационных файлов, которые решают задачу. Присылаем в формате текстового документа: задание и команды для решения (без вывода). Формат - PDF (один файл на все задания).
