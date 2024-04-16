ssh -i "EhsanMarketingDj-v3.0.0-KP.pem" ubuntu@ec2-65-0-61-13.ap-south-1.compute.amazonaws.com


su djauser

djauser1234

# ========================================================
sudo nano /etc/systemd/system/gunicorn.socket

sudo nano /etc/systemd/system/gunicorn.service

sudo nano /etc/nginx/sites-available/djproject


sudo systemctl daemon-reload
sudo systemctl restart gunicorn
sudo systemctl restart nginx

# 1 ==========================================
`sudo nano /etc/systemd/system/gunicorn.socket`

```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```


# 2 ==========================================
`sudo nano /etc/systemd/system/gunicorn.service`

```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=djuser
Group=www-data
WorkingDirectory=/home/djuser/djprojectdir/djproject
ExecStart=/home/djuser/djprojectdir/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/run/gunicorn.sock \
          core.wsgi:application

[Install]
WantedBy=multi-user.target
```

# 3 ==========================================
`sudo nano /etc/nginx/sites-available/djproject`

```
server {
    listen 80;
    server_name 65.0.61.13;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/djuser/djprojectdir/djproject;
    }

    location /media/ {
        root /home/djuser/djprojectdir/djproject;
    }

    location / {
        proxy_pass http://localhost:8000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
}

```


# 4 ==========================================
`sudo nano /etc/systemd/system/daphne.service`

```                           
[Unit]
Description=Daphne ASGI Server
After=network.target

[Service]
User=djuser
Group=www-data
WorkingDirectory=/home/djuser/djprojectdir/djproject
ExecStart=/home/djuser/djprojectdir/venv/bin/daphne -b 0.0.0.0 -p 8000 core.router:application

[Install]
WantedBy=multi-user.target

```

##############################################

sudo systemctl daemon-reload
sudo systemctl start daphne


sudo systemctl enable daphne


sudo systemctl status daphne

##############################################
################## New  made by Me ###########
##############################################
##############################################


# 111111111111111111111111111111111111111111111

`sudo nano /etc/systemd/system/daphne.service`

```                           
[Unit]
Description=Daphne ASGI Server
After=network.target

[Service]
User=djuser
Group=www-data
WorkingDirectory=/home/djuser/djprojectdir/djproject
ExecStart=/home/djuser/djprojectdir/venv/bin/daphne -b 0.0.0.0 -p 8001 core.router:application

[Install]
WantedBy=multi-user.target

```

# 22222222222222222222222222222222222222222222222222222222222222222222222222
`sudo nano /etc/nginx/sites-available/djproject`

```
server {
    listen 80;
    server_name 65.0.61.13;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/djuser/djprojectdir/djproject;
    }

    location /media/ {
        root /home/djuser/djprojectdir/djproject;
    }

    location /ws/ {
        proxy_pass http://localhost:8001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
    
    location / {
        include proxy_params;
        proxy_pass http://unix:/run/gunicorn.sock;
    }
}
```


# 33333333333333333333333333333333333333333333333333

sudo systemctl daemon-reload
sudo systemctl start daphne


sudo systemctl enable daphne


sudo systemctl status daphne

# 44444444444444444444444444444444444444444444444444444

sudo systemctl daemon-reload
#sudo systemctl restart gunicorn
sudo systemctl restart nginx



