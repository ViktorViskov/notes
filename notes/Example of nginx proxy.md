```nginx
server {
    listen          80;
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

#### File name
```bash
./config/default.conf.template
```

#### Docker compose file
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
[[proxy]]
[[nginx]]
[[docker]]
