# About Project
1. softbook is the project root directory name

# FastAPI deployment with supervisor and nginx

1. Install the `gunicorn`
```
(env) atul@atul-Lenovo-G570:~/softbook$ pip install gunicorn

```
2. Check the version of `gunicorn`
```
(env) atul@atul-Lenovo-G570:~/softbook$ gunicorn --version

```

3. Run fastapi application using gunicorn
```
(env) atul@atul-Lenovo-G570:~/softbook$ gunicorn -w 4 -k uvicorn.workers.UvicornH11Worker main:app
```

4. Install supervisor 

```
(env) atul@atul-Lenovo-G570:~/softbook$ pip install supervisor

```

5. create the configuration file for fastapi in supervisor
```
atul@atul-Lenovo-G570:~$ sudo nano /etc/supervisor/conf.d/fastapi.conf

```

6. Right click to paste below code in nano editor
```
[program:fastapi]
command=/home/atul/softbook/env/bin/gunicorn -w 4 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8000 main:app
directory=/home/atul/softbook
user=atul
autostart=true
autorestart=true
stdout_logfile=/var/log/fastapi/fastapi.stdout.log
stderr_logfile=/var/log/fastapi/fastapi.stderr.log
environment=PYTHONUNBUFFERED=1
```
7. Pressh Ctrl+O to save 
8. if ask `File Name to Write` then simply press the Enter button
9. Press Ctrl+X for exit
10. Run the below command to check the supervisor version
```
atul@atul-Lenovo-G570:~$ supervisord --version
```

11. Run bolow commands start the supervisor for fastapi.conf
```
atul@atul-Lenovo-G570:~$ sudo supervisorctl reread

```

```
atul@atul-Lenovo-G570:~$ sudo supervisorctl update

```

```
atul@atul-Lenovo-G570:~$ sudo supervisorctl restart fastapi

```

12. command to check the status of fastapi
```
atul@atul-Lenovo-G570:~$ sudo supervisorctl status

```

13. Now you can use the `http://localhost:8000/softbook-docs` in browser

14. install the nginx server
```
atul@atul-Lenovo-G570:~$ sudo apt install nginx

```
15. Create a new Nginx configuration file
```
sudo nano /etc/nginx/sites-available/fastapi
```

16. Right click in nano editor to paste the below code
```
server {
    listen 80;
    server_name localhost;

    # Static files
    location /static/ {
        alias /home/atul/softbook/static/;
    }

    # Media files
    location /uploads/ {
        alias /home/atul/softbook/uploads/;
    }
  

    # FastAPI application
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```
17. press Ctrl+O  for save 
18. press enter if ask "File Name to Write"
19. press Ctrl+x for eixt from nano editor
20. Enable the Nginx Configuration file 

```
sudo ln -s /etc/nginx/sites-available/fastapi /etc/nginx/sites-enabled

```

21. Create Nginx Configuration File

```
atul@atul-Lenovo-G570:~$ sudo nano /etc/nginx/nginx.conf

```
22. paste the below code in '/etc/nginx/nginx.conf' file in nano editor
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    gzip on;
    gzip_disable "msie6";

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```
23. Download the mime.type file
```
atul@atul-Lenovo-G570:~$ sudo wget https://raw.githubusercontent.com/nginx/nginx/master/conf/mime.types -O /etc/nginx/mime.types

```

24. Verify and Test Nginx Configuration
```
atul@atul-Lenovo-G570:~$ sudo nginx -t
```


25. Set appropriate ownership for directories
```
atul@atul-Lenovo-G570:~$ sudo chown -R www-data:www-data /etc/nginx
atul@atul-Lenovo-G570:~$ sudo chown -R www-data:www-data /var/log/nginx
atul@atul-Lenovo-G570:~$ sudo chown -R www-data:www-data /var/www/html

```

26. Ensure directories have proper permissions

```
atul@atul-Lenovo-G570:~$ sudo chmod -R 755 /etc/nginx
atul@atul-Lenovo-G570:~$ sudo chmod -R 755 /var/log/nginx
atul@atul-Lenovo-G570:~$ sudo chmod -R 755 /var/www/html
```

27. Fix Permissions for Log and PID Files

```
atul@atul-Lenovo-G570:~$ sudo chown -R www-data:www-data /var/log/nginx
atul@atul-Lenovo-G570:~$ sudo chmod -R 755 /var/log/nginx

```

28. Create and set ownership for PID directory
```
atul@atul-Lenovo-G570:~$ sudo mkdir -p /run/nginx
atul@atul-Lenovo-G570:~$ sudo chown -R www-data:www-data /run/nginx
atul@atul-Lenovo-G570:~$ sudo chmod -R 755 /run/nginx

```

29. Set appropriate ownership for log directory
```
atul@atul-Lenovo-G570:~$ sudo chown -R www-data:www-data /var/log/nginx

```
30. Ensure log directory has correct permissions
```
atul@atul-Lenovo-G570:~$ sudo chmod -R 755 /var/log/nginx

```
31. Set appropriate ownership for run directory
```
atul@atul-Lenovo-G570:~$ sudo chown -R www-data:www-data /run/nginx
```
32. Ensure run directory has correct permissions
```
atul@atul-Lenovo-G570:~$ sudo chmod -R 755 /run/nginx

```

33. Some time apache create problem for 80 port because ubuntu bydefault install apache2
- Stop the Apache service
```
atul@atul-Lenovo-G570:~$ sudo systemctl stop apache2

```
- Disable Apache service so that it doesn't start automatically on boot
```
atul@atul-Lenovo-G570:~$ sudo systemctl disable apache2

```
- Verify that Apache service is stopped:

```
atul@atul-Lenovo-G570:~$ sudo systemctl status apache2

```


34. Run the below command to start and enable nginx
```
atul@atul-Lenovo-G570:~$ sudo systemctl start nginx
atul@atul-Lenovo-G570:~$ sudo systemctl enable nginx

```
35. check the status of nginx server
```
atul@atul-Lenovo-G570:~$ sudo systemctl status nginx

```

36. Reload Nginx to apply the configuration changes
```
atul@atul-Lenovo-G570:~$ sudo systemctl reload nginx

```
37. Now you can use http://localhost/softbook-docs in your browser

38. Some notes
 - If you want to reload changes then restart the supervisor
   ```
   atul@atul-Lenovo-G570:~$ sudo supervisorctl restart fastapi

   ```
- If you want to stop supervisor then run below command
  ```
  atul@atul-Lenovo-G570:~$ sudo supervisorctl stop fastapi
  ```
- If you want to start supervisor then run below command
  ```
  atul@atul-Lenovo-G570:~$ sudo supervisorctl start fastapi

  ```
- IF you want to run `uvicorn` for debug or development then first stop supervisor after that run uvicorn
  ```
  (env) atul@atul-Lenovo-G570:~/softbook$ uvicorn main:app --reload

  ``` 
