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

## PaaS

```bash
git clone https://github.com/oswaldderiemaecker/paas
```

### Cloud Foundry

```bash
cf dev start -m 3072 -c 4
```

#### Clone Sample 

```bash
git clone https://github.com/cloudfoundry-samples/spring-music
cd ./spring-music
cf login -a api.local.pcfdev.io --skip-ssl-validation
```

#### Build the app

```bash
./gradlew assemble
```

#### Push the app

```bash
cf push --hostname spring-music
```

Open the sample app in your browser:
requested state: started
instances: 1/1
usage: 512M x 1 instances
routes: spring-music.local.pcfdev.io

## CaaS

### AWS ECS

#### Clone Sample

```bash
git clone https://github.com/Pierozi/aws-ecs-php-micro-services.git
```

### Swarm

#### Creating the Virtual Machines
```bash
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2

docker-machine ls
```

#### Initializing the Swarm

```bash
docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.100:2377"
Swarm initialized: current node (i2vzfx6k465cxzsivfbiohddt) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3rwnhvhfqu4pntyzs6la2h4pamm94a6gfuzu27j611tnw6cbw9-ay5df9c4emm8omrta0z2whm88 192.168.99.101:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

#### Worker Joining the Manager

```bash
docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-3rwnhvhfqu4pntyzs6la2h4pamm94a6gfuzu27j611tnw6cbw9-ay5df9c4emm8omrta0z2whm88 192.168.99.101:2377"
This node joined a swarm as a worker.
```
```bash
docker-machine ssh myvm1 "docker node ls"
```

#### Deploying Application

```bash
cd docker-training
docker-machine scp docker-service.yml myvm1:~
docker-machine ssh myvm1 "docker pull oswald/docker-training:latest"
docker-machine ssh myvm1 "docker stack deploy -c docker-service.yml myapp"
docker-machine ssh myvm1 "docker stack ps myapp"
```

#### Cleanup

```bash
docker-machine ssh myvm1 "docker stack rm myapp"
```

### Minikuke

```bash
minikube start
```

#### Application Configuration

```bash
kubectl create secret generic mysql-pass --from-literal=password=YOUR_PASSWORD
```

#### The Database Metadata

```bash
wget https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/wordpress/mysql-deployment.yaml
```

#### Deploying the Database

```bash
kubectl create -f https://k8s.io/examples/application/wordpress/mysql-deployment.yaml
kubectl get pvc
kubectl get pods
```

#### The Application Metadata

```bash
wget https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/wordpress/wordpress-deployment.yaml
```

#### Deploying the WP

```bash
kubectl create -f https://k8s.io/examples/application/wordpress/wordpress-deployment.yaml
kubectl get pvc
kubectl get services wordpress
```

Visit: http://192.168.99.103:32334/wp-admin/install.php

#### Cleanup

```bash
kubectl delete secret mysql-pass
kubectl delete deployment -l app=wordpress
kubectl delete service -l app=wordpress
kubectl delete pvc -l app=wordpress
```

### Rancher

#### Install and run Rancher

```bash
docker-machine start rancheros
eval $(docker-machine env rancheros)
docker-machine ip rancheros
```

#### Configure the Ethereum Helm Package

```
geth.account.address = 0xab70383d9207c6cc43ab85eeef9db4d33a8ad4e8
geth.account.privateKey = 38000e15ca07309cc2d0b30faaaadb293c45ea222a117e9e9c6a2a9872bb3bcf
geth.account.secret = any passphrase that Geth will use to encrypt your private key
```

### AWS ECS

Follow the tutorial at: https://aws.amazon.com/getting-started/tutorials/deploy-docker-containers/



