# Ref awaresome-compose

- <https://github.com/docker/awesome-compose/tree/master/django>

## Wakatime project share url

- <https://wakatime.com/@spcn08/projects/ellickubel>

## URL-DEPLOY

- https://kan-django0703.xops.ipv9.me/

## ขั้นตอนการพัฒนา

### push images เข้า docker hub

```ruby
- $ docker compose up -d --build เพื่อสร้าง images
- $ docker ps เพื่อเช็คimages
- $ docker login เพื่อเข้า docker hub
- $ docker tag django-web:latest kanchanarangcharoen/django-web:0703
- $ docker push kanchanarangcharoen/django-web:0703 เพื่อ push images เข้า docker hub
```

### stack deploy ที่ <https://portainer.ipv9.me>


