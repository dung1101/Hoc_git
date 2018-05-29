# Deploy trên ubuntu 16.0.4

Cấu trúc thư mục : osticket 
 - /project : `/home/osticket`
 - /static : `/home/osticket/user/static`
 - /media : `/home/media`

## install requirement (chạy trên quyền root)
```
apt-get update
apt-get install python3-pip python3-dev nginx python3.5-dev libmysqlclient-dev redis-server
pip3 install --upgrade pip
pip install virtualenv
```
*python3.5-dev dành cho python 3.5 nếu version khác thì thay đổi cho phù hợp*
## Di chuyển vào thư mục chứa project và tạo môi trường 
```
cd /home/osticket
virtualenv env
```
## Kích hoạt môi trường và cài đặt những tool cần thiết
```
source env/bin/activate

pip install django gunicorn channels channels_redis simplejson mysqlclient

deactive
```
## Tạo file `asgi.py` 
`nano osticket/asgi.py`
nội dung 
```
"""
ASGI entrypoint. Configures Django and then runs the application
defined in the ASGI_APPLICATION setting.
"""

import os
import django
from channels.routing import get_default_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "osticket.settings")
django.setup()
application = get_default_application()
```
## Tạo Gunicorn systemd Service File
`nano /etc/systemd/system/gunicorn.service`
nội dung
```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=root
Group=www-data
WorkingDirectory=/home/osticket
ExecStart=/home/osticket/env/bin/gunicorn --access-logfile - --workers 3 --bind unix:/home/osticket/osticket.sock osticket.wsgi:application

[Install]
WantedBy=multi-user.target
```
## Bật gunicorn
```
systemctl start gunicorn
systemctl enable gunicorn
```
## Check gunicorn 
```
sudo systemctl status gunicorn
```
## nếu chưa `active` chỉnh lại file config và reload 
```
systemctl daemon-reload
systemctl restart gunicorn
```
## Tạo daphne systemd Service File

`nano /etc/systemd/system/daphne.service`
nội dung
```
[Unit]
Description=My Daphne Service
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/home/osticket
ExecStart=/home/osticket/env/bin/daphne -b 0.0.0.0 -p 8001 osticket.asgi:application
Restart=on-failure

[Install]
WantedBy=multi-user.target

```
## chạy daphne
```
systemctl start daphne.service
systemctl enable daphne.service
```
## Check daphne
```
sudo systemctl status daphne
```
## nếu chưa `active` chỉnh lại file config và reload 
```
systemctl daemon-reload
systemctl restart daphne
```
## Cấu hình redis-server
`sudo nano /etc/redis/redis.conf`
tìm đến dòng bind 127.0.0.1 thêm vào ip của máy ví dụ
`bind 127.0.0.1 192.168.40.150`

## Cấu hình nginx
`nano /etc/nginx/sites-available/default`<br>
ở bên dưới dòng `listen [::]:80 default_server;` thêm vào nội dung bên dưới và comment tất cả các dòng khác lại
```
	location = /favicon.ico { access_log off; log_not_found off; }
        location /static/ {
                root /home/osticket/user;
        }
        location /media/ {
                root /home/osticket;
        }

        location / {
                include proxy_params;
                proxy_pass http://unix:/home/osticket/osticket.sock;
        }
		location /ws/ {
                proxy_pass http://0.0.0.0:8001;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
        }
```
## Test your Nginx configuration for syntax errors:
```
nginx -t
```
## Ok thì khởi động lại nginx 
```
systemctl restart nginx
```
