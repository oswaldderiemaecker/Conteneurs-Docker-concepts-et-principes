# Conteneurs Docker, concepts et principes

## AWS Lab Instance

AMI ID: ami-0dc53f9699f425293
Instance Type t3.medium
Security Group Ingress on ports 22, 8080 allow all traffic on 0.0.0.0/0

## Docker 

### Clone Repository

```bash
git clone https://github.com/oswaldderiemaecker/docker-training.git
```

### Docker install

On AWS:

```bash
sudo usermod -aG docker $USER && newgrp docker
```

Quit session and relog

#### Build the application image

```bash
docker build -t myapp_php .
docker images
docker run -d -p 8080:80 myapp_php
docker ps
```

Visit: http://<PUBLIC_IP>:8080/

#### Modifying Dockerfile

Add:
```bash
RUN apt-get install -y vim
```

```bash
docker build -t myapp_php .
docker images
```

Clean up:

```bash
docker ps
docker stop 8c4dadb01bd7
docker rm 8c4dadb01bd7
```


#### Docker Registry & docker pull

visit: https://hub.docker.com/

```bash
docker pull redis
```

#### Install Docker compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
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

#### Install Minikube

##### Install kubectl

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

##### Install minikube

```bash
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
&& chmod +x minikube \
&& sudo mv minikube /usr/local/bin/
minikube version
yum install conntrack
```

```bash
sudo -i
export PATH=$PATH:/usr/local/bin
minikube start --driver=docker
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
kubectl create -f mysql-deployment.yaml
kubectl get pvc
kubectl get pods
```

#### The Application Metadata

```bash
wget https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/wordpress/wordpress-deployment.yaml
```

#### Deploying the WP

Replace Service Type to NodePort!

```bash
kubectl create -f wordpress-deployment.yaml
kubectl get pvc
kubectl get services wordpress
```

Open a new terminal and

```bash
minikube service wordpress
apt-get install lynx
lynx http://172.31.71.196:8080
```

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
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  --privileged \
  rancher/rancher:latest
```

Open https://<PUBLIC_IP>/

Follow instructions.

### AWS ECS

Follow the tutorial at: https://console.aws.amazon.com/ecs/home?region=us-east-1#/firstRun

https://aws.amazon.com/getting-started/tutorials/deploy-docker-containers/



