```nginx
server {
    listen          80;
    server_name     site;
    index           index.html;
    client_max_body_size 1G;
    location / {
        proxy_pass       http://backend:5174/;
        proxy_redirect   off;
    }
    location ^~ /storage {
        rewrite ^/storage/(.*) /$1 break;
        proxy_pass       http://minio:9000/;
        proxy_redirect   off;
    }
}
```

[[proxy]]
