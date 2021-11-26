# Conteneurs Docker, concepts et principes

## AWS Lab Instance

Create a Key Pair.

* AMI ID: ami-0dc53f9699f425293
* Instance Type: t3.medium
* Storage: 15 Go SSD (gp2)
* Tag: Name=CaaS-<YOUR_NAME>
* Security Group: Ingress all ports allow all traffic on 0.0.0.0/0
* Select your Key Pair

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
cat Dockerfile
docker build -t myapp_php .
docker images
docker run -d -p 8080:80 myapp_php
docker ps
docker exec -ti 1d921c1c08b1 /bin/bash
```

Visit: http://<PUBLIC_IP>:8080/

#### Modifying Dockerfile

Add:
```bash
RUN apt-get install -y htmldoc
vi public/index.php # add echo '<br>Update';
```

```bash
docker build -t myapp_php .
docker images
# docker push
docker ps
docker stop 8c4dadb01bd7
docker run -d -p 8080:80 myapp_php
```

Clean up:

```bash
docker ps
docker stop 8c4dadb01bd7
docker ps -a
docker rm 8c4dadb01bd7
```


#### Docker Registry & docker pull

visit: https://hub.docker.com/

```bash
docker pull redis
docker images
```

#### Install Docker compose

```bash
sudo apt install docker-compose
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

#### Example 2

https://github.com/oswaldderiemaecker/webinar-kubernetes-api-gateway/blob/master/docs/docker-compose.md

## CaaS

### Docker-Machine

#### Creating the Virtual Machines
```bash
docker-machine create --driver virtualbox myvm1
docker-machine ls
docker-machine env
eval $(docker-machine env)
docker ps
```

Get the IP of the docker-machine

```bash
docker-machine ip
```

Goto http://<DOCKER-MACHINE-IP>:8080/

### Swarm

#### Initializing the Swarm

```bash
docker swarm init --advertise-addr <PUBLIC_IP>:2377
Swarm initialized: current node (i2vzfx6k465cxzsivfbiohddt) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3rwnhvhfqu4pntyzs6la2h4pamm94a6gfuzu27j611tnw6cbw9-ay5df9c4emm8omrta0z2whm88 <PUBLIC_IP>:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

#### Adding an AWS worker

* AMI ID: ami-0dc53f9699f425293
* Instance Type t2.micro
* Tag: Name = CaaS - <YOUR_NAME> - Swarm Worker
* Security Group Ingress on all ports and allow all traffic on 0.0.0.0/0

#### Worker Joining the Manager
    
```bash
sudo usermod -aG docker $USER && newgrp docker
```

```bash
docker swarm join --token SWMTKN-1-3rwnhvhfqu4pntyzs6la2h4pamm94a6gfuzu27j611tnw6cbw9-ay5df9c4emm8omrta0z2whm88 192.168.99.101:2377
This node joined a swarm as a worker.
```
    
On the manager:

```bash
docker node ls
```

#### Deploying Application

```bash
cd docker-training
cat docker-service.yml
docker pull oswald/docker-training:latest
docker stack deploy -c docker-service.yml myapp
docker stack ps myapp
```
    
Visit: http://<PUBLIC_IP>/

#### Cleanup

```bash
docker stack rm myapp
```

NOTE: Terminate the Swarm worker instance.

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
sudo apt install conntrack
```

```bash
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
sudo apt-get install lynx
lynx http://<MINIKUBE_IP>:<MINIKUBE_PORT>
```

#### Cleanup

```bash
kubectl delete secret mysql-pass
kubectl delete deployment -l app=wordpress
kubectl delete service -l app=wordpress
kubectl delete pvc -l app=wordpress
```
    
#### Example 2
    
https://github.com/oswaldderiemaecker/webinar-kubernetes-api-gateway/blob/master/docs/minikube.md
    
```bash
minikube stop
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

### AWS ECS

#### Clone Sample

```bash
git clone https://github.com/Pierozi/aws-ecs-php-micro-services.git
```



