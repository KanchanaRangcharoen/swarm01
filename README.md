# swarm01 django
## Ref awaresome-compose

- <https://github.com/docker/awesome-compose/tree/master/django>

## Wakatime project share url

- <https://wakatime.com/@spcn08/projects/ellickubel>

## URL-Deploy

- https://kan-django0703.xops.ipv9.me/

## Development Stage
**Ref Prepare Machine** => https://github.com/pitimon/dockerswarm-inhoure#readme
1. Create VM on proxmox
2. Set Template
    - Set time
        ```
        timedatectl set-timezone Asia/Bangkok
        ```
    - [Install docker](https://docs.docker.com/engine/install/ubuntu/)
3. Create node from Template
    - manage
    - work1
    - work2
4. Change hostname
    ```
    hostnamectl set-hostname [set name] 
    ```
5. Edit machine-id in clone
    ```
    cp /dev/null /etc/machine-id
    rm /var/lib/dbus/machine-id
    ln -s /etc/machine-id /var/lib/dbus/machine-id
    init 0
    ```

6. Create Docker Swarm and Portainer on Manager
    - Swarm init

        ```
        docker swarm init 
        ```    
    - Copy token url and run it on 2 worker machines
    - manager node

        ```
        docker node ls
        ```    
    - Install portainer for swarm

        ```
        curl -L https://downloads.portainer.io/ce2-17/portainer-agent-stack.yml -o portainer-agent-stack.yml docker stack deploy -c portainer-agent-stack.yml portainer
        ```

7. [Revert Proxy on Manager](#revert-proxy-on-manager)
8. [Create Image](#create-image)
9. [Create docker-compose.yml](#create-docker-composeyml)
10. Bring docker-compose.yml Stack Deploy on the machine

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