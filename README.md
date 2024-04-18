## Описание:
Social-network - форум для обмена информацией. Через него можно создавать посты, добавляя к ним картинки, вступать в тематические группы, просматривать и комментировать посты других людей.
## Технологии:
* Python 3.10
* Django3
* Nginx
* Gunicorn
* React
* djangorestframework
* Certbot
## Установка на локальный сервер
1. Клонировать проект из репозитория:
2. Создайте вертуальное окружение:
```python3 -m venv venv```
3. Установите зависимости:
```pip install -r requirements.txt```
4. Создайте файл ***.env*** в корневой папке (в папке с Manage.py), добавьте SECRET_KEY.
***
# Разверните проект на удаленном сервере
***
 ### Подключение к GitHub
Установите Git: 

```sudo apt install git```

Создайте пару ключей SSH:

```ssh-keygen```

Сохраните открытый ключ в учетной записи GitHub:

```cat .ssh/id_rsa.pub```

Клонируйте проект на удаленный сервер:

```git clone git@github.com:Your_account/Project_name.git```  
***
### Запуск  backend
Установите на сервер менеджер пакетов и утилиту виртуальной среды: 
```sudo apt install python3-pip python3-venv -y```  
Создайте виртуальную среду:  
```python3 -m venv venv ```  
Активируйте виртуальную окружения: 
```source venv/bin/activate```   
Установите зависимости:  
```pip install -r requirements.txt```    
Примените миграции:   
```python manage.py migrate```  
Создайте superuser:  
```python manage.py createsuperuser```
В файле settings.py в ALLOWED_HOSTS добавьте IP-адрес сервера, а также «127.0.0.1», «localhost» и имя домена.  
***
### Запуск frontend
Установите менеджер пакетов npm на сервер:
```curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\```  
```sudo apt-get install -y nodejs```
Установите зависимости внешнего интерфейса из каталога ***infra_sprint1/frontend***:  
``npm i``  
***
### Установите и запустите Gunicorn
```pip install gunicorn==20.1.0```   
Создать unit для gunicorn:  
```sudo nano /etc/systemd/system/gunicorn.service ```  
В файле Gunicorn.service опишите конфигурацию процесса
```html
[Unit]
Description=gunicorn daemon 
After=network.target 

[Service]
User=yc-user 
WorkingDirectory=/home/yc-user/social-network/backend/
ExecStart=/home/yc-user/social-network/backend/venv/bin/gunicorn --bind 0.0.0.0.0:8000 backend.wsgi
[Install]
WantedBy=multi-user.target  
```

Запустите процесс Gunicorn.service:
```sudo systemctl start gunicorn```  
Добавьте процесс в автозапуск: 
```sudo systemctl enable gunicorn```  
***
### Установите Nginx
```sudo apt install nginx -y```  
Запустите  Nginx:  
```sudo systemctl start nginx```  
Сообщите брандмауэру, какие порты должны оставаться открытыми:
```sudo ufw allow 'Nginx Full'```  
```sudo ufw allow OpenSSH```  
Включите брандмауэр: 
```sudo ufw enable```
***

### Создайте статику внешнего интерфейса приложения.
В каталоге ***social-network/frontend*** выполните:  
```npm run build```
```sudo cp -r /home/yc-user/social-network/frontend/build/. /var/www/social-network/```  
Настройка для обработки статического интерфейса внешнего приложения.:   
``` sudo nano /etc/nginx/sites-enabled/default```  
Удалите все настройки из файла и добавьте свои:  
```html
server {
    server_name YOUR_IP YOUR DOMAIN;
    client_max_body_size 32m;
    server_tokens off;

    location /api/ {
        proxy_pass http://127.0.0.1:8080;
    }
    location /admin/ {
        proxy_pass http://127.0.0.1:8080;
    }

    location /media/ {
        alias /var/www/social-network/media/;
    }
    location / {
        root /var/www/social-network;
        index index.html index.html index.htm;
        try_files $uri /index.html;
    }
```

### Настройка для работы с серверным приложением
В файле ***settings.py*** ,изменить(добавить):  
```STATIC_URL = 'static_backend'```
```STATIC_ROOT = BASE_DIR / 'static_backend'```  
Активировав виртуальную среду, запустите команду:  
```python manage.py collectstatic```
Перейдите в корень проекта и выполните команду:  
```sudo cp -r social-network/backend/static_backend/ /var/www/social-network/```  
***
### Настроить шифрование
Установите ***certbot***:  
```sudo apt install snapd```

```sudo snap install core; sudo snap refresh core```  

```sudo snap install --classic certbot```

```sudo ln -s /snap/bin/certbot /usr/bin/certbot```

Давайте запустим certbot и получим SSL-сертификат: 

```sudo certbot --nginx```

Перезагрузите конфигурацию Nginx:

```sudo systemctl reload nginx```  

Настройте автоматическое продление SSL-сертификата: 

```sudo certbot certificates```  

```sudo certbot renew --dry-run```

Автор | Почта
------------- | -------------
[Doomhunter190](https://github.com/DoomHunter190) | <small>[maximportnov9999@gmail.com](maximportnov9999@gmail.com)
