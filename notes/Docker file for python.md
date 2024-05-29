```dockerfile
# maintainer info
FROM python:3.12-alpine
LABEL maintainer="carrergt@gmail.com"

WORKDIR /app
COPY ./requirements.txt /app
RUN pip3 install -r requirements.txt

# config project
COPY ./ /app

CMD ["sh","prod_start.sh"]
```
[[docker]]
[[python]]