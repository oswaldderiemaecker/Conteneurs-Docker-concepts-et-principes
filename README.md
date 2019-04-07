# Conteneurs Docker, concepts et principes

## Docker 

### Clone Repository

```bash
git clone git@github.com:oswaldderiemaecker/docker-training.git
```

#### Build the application image

```bash
docker build -t myapp_php .
docker images
docker run -p 8080:80 myapp_php
docker ps
```

Visit: http://192.168.99.100:8080/

#### Modifying Dockerfile

Add:
```bash
RUN apt-get install -y vim
```

```bash
docker build -t myapp_php .
docker images
```

#### Docker Registry & docker pull

visit: https://hub.docker.com/

```bash
docker pull redis
```

#### Docker Compose

```bash
cat docker-compose.yml

docker-compose up -d
docker-compose ps
docker-compose logs --tail=10 -f
docker-compose stop
docker-compose down
```

