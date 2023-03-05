# Ref awaresome-compose
  - https://github.com/docker/awesome-compose/tree/master/django
# Wakatime project share url
  - https://wakatime.com/@spcn08/projects/ellickubel
## ขั้นตอนการพัฒนา
### push images เข้า docker hub
- $ docker compose up -d --build เพื่อสร้าง images
- $ docker ps เพื่อเช็คimages
- $ docker login เพื่อเข้า docker hub
- $ docker tag swarm02-web kanchanarangcharoen/swarm01-web:050365
- $ docker push kanchanarangcharoen/swarm01-web:050365 เพื่อ push images เข้า docker hub
### stack deploy ที่ https://portainer.ipv9.me