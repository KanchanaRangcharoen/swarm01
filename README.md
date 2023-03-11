# swarm01 django
## Ref awaresome-compose

- <https://github.com/docker/awesome-compose/tree/master/django>

## Wakatime project share url

- <https://wakatime.com/@spcn08/projects/ellickubel>

## URL-Deploy

- https://kan-django0703.xops.ipv9.me/

## Development Stage
**Ref Prepare Machine** => https://github.com/pitimon/dockerswarm-inhoure#readme

### Revert Proxy on Manager
- Set IP for Client

    - Edit file hosts
        - windows C:\Windows\System32\drivers\etc\hosts
        - Linux /etc/hosts
    - Add domain IP of manager for every application
        ```
        [ip manage] traefik.demo.local
        ```
    - [Step Revert Proxy by pitimon](https://github.com/pitimon/dockerswarm-inhoure/tree/main/ep03-traefik)

### Create Image
Bring selected apps push images 
- Compose up file compose.yaml to create images 
    ```
    docker compose up -d --build
    ```
- Login docker hub
    ```
    docker login  # user and password docker hub
    ```
- Create a tag that refers to images 
    ```
    docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
    ```

    **Ex.** docker tag django-web :latest kanchanarangcharoen/django-web:0703
- Upload an image to a repositories on docker hub
    ```
    docker push [OPTIONS] NAME[:TAG]
    ```
    **Ex.** docker push kanchanarangcharoen/django-web:0703

### Create docker-compose.yml
<details><summary><ins>SHOW CODE docker-compose.yml</ins></summary>
<p>

```
version: '3.7'

services:
  db:
    image: postgres
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    networks:
      - default
    volumes:
      - db_data:/var/lib/postgresql/data

  web:
    image: kanchanarangcharoen/django-web:0703
    networks:
      - webproxy
      - default
    volumes:
      - static_data:/usr/src/app/static
    depends_on:
      - db
    deploy:
      replicas: 1
      labels:
        - traefik.docker.network=webproxy
        - traefik.enable=true
        - traefik.http.routers.${APPNAME}-https.entrypoints=websecure
        - traefik.http.routers.${APPNAME}-https.rule=Host("${APPNAME}.xops.ipv9.me")
        - traefik.http.routers.${APPNAME}-https.tls.certresolver=default
        - traefik.http.services.${APPNAME}.loadbalancer.server.port=8000

      restart_policy:
        condition: any
      update_config:
        delay: 5s
        parallelism: 1
        order: start-first

volumes:
  db_data:
  static_data:

networks:
  default:
    driver: overlay
    attachable: true
  webproxy:
    external: true
```
</p>
</details>

### Bring docker-compose.yml Stack Deploy on the machine

![django](https://user-images.githubusercontent.com/119097660/224472963-6fe2c144-7066-4c2e-a9db-6dddf6e91c8b.png)
