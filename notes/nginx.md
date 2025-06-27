## Config examples
```nginx
server {
    listen 80;
    
    listen 443 ssl;
    ssl_certificate /ssl/fullchain.pem;
    ssl_certificate_key /ssl/privkey.pem;
    
    server_name     site;
    index           index.html;
    client_max_body_size 1G;

    location /static/ {
        alias /static/;
        expires 30d;
        access_log off;
        try_files $uri $uri/ /; #redirect all requests to index.html
    }

    location /media/ {
        alias /media/;
        expires 30d;
        access_log off;
    }

    location ^~ /api {
        rewrite ^/api/(.*) /$1 break;
        proxy_pass       http://192.168.0.112:8000/;
        proxy_redirect   off;
    }
}

```

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name sub.domail.com;
    server_tokens off;
    charset utf-8;
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://ip:port;
    }
}
```

```nginx
server {
    listen 80;
    client_max_body_size 1G;

    location / {
        proxy_pass       http://frontend:3000/;
        proxy_redirect   off;
    }

    location ^~ /api {
        proxy_pass       http://backend:3000/;
        proxy_redirect   off;
    }
}
```

### Redirect to port 443
```nginx
server {
    listen 80;
    server_name site;

    location / {
        return 301 https://$server_name$request_uri;
    }
}
```
### Fix redirect error
When accessing https://gestureai.chat/api/admin, the backend returns a 302 redirect pointing to http://backend:3000/api/admin/ instead of the correct domain URL. This happens because the backend is unaware that it is running behind a reverse proxy and generates redirects based on its internal hostname (backend:3000) instead of the public domain.

```nginx
server {
    listen 80;
    client_max_body_size 1G;

    location ^~ /api {
        proxy_pass       http://backend:3000;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Host $host;

        proxy_redirect   off;
    }
}
```

`proxy_pass http://backend:3000;`
Forwards requests to the backend service without stripping /api from the path.

`proxy_set_header Host $host;`
Passes the original domain (gestureai.chat) to the backend.

`proxy_set_header X-Real-IP $remote_addr;`
Forwards the client's real IP address.

`proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;`
Keeps the chain of client and proxy IPs.

`proxy_set_header X-Forwarded-Proto $scheme;`
Indicates whether the original request was HTTP or HTTPS.

`proxy_set_header X-Forwarded-Host $host;`
Passes the original host for proper URL generation in backend redirects.

### File name
```bash
./config/default.conf.template
```

## Docker compose file examples
```yaml
services:
  nginx:
    image: nginx
    ports: 
      - "80:80"
    volumes:
      - ./config:/etc/nginx/templates
      - ./static:/static
      - ./media:/media
```

```yaml
services:
  nginx:
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
```


[[proxy]]
[[docker]]
