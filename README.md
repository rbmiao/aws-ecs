


Deploy a Docker App to AWS using Elastic Container Service (ECS)
===

> Learn to build and deploy your distributed applications easily to the cloud with Docker



Create docker image and push to AWS:
===
* Make sure docker is running.

* Fetch source code:
```
mkdir aws-ecs && cd aws-ecs
git clone https://github.com/prakhar1989/docker-curriculum.git
cd docker-curriculum/flask-app
docker build -t rbmiao/catnip .
docker run -p 8889:5000 rbmiao/catnip
```
* Got docker image:
```
docker images
REPOSITORY                TAG       IMAGE ID       CREATED         SIZE
rbmiao/catnip             latest    2720f1425d62   3 minutes ago   895MB
```
* Run docker container in localhost:
```
docker run -p 8889:5000 rbmiao/catnip
```

* Login to app, localhost:8889. App comes. Refresh to get new pics.

* Login to aws ecr
Before running following command, need set up aws access permission already.
```
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 971879102683.dkr.ecr.us-east-1.amazonaws.com
```
```
Login Succeeded
```

* Create an ECR repo on aws: python-03132021

* On local desktop, tag docker image and push to ECR:
```
docker tag rbmiao/catnip:latest 971879102683.dkr.ecr.us-east-1.amazonaws.com/python-03133021:latest

docker push 971879102683.dkr.ecr.us-east-1.amazonaws.com/python-03133021:latest
```
It takes a couple of minutes to push to AWS. Output should look like this:
```
Using default tag: latest
The push refers to repository [971879102683.dkr.ecr.us-east-1.amazonaws.com/python-03133021]
a1415813388d: Pushed
8bfd42376c5d: Pushed
c7fff57dcdee: Pushed
78db0f6b59c6: Pushed
b7bcac1b5fe8: Pushed
e8846e341673: Pushed
04d1717d0e01: Pushed
dacb447ffe30: Pushed
bde301416dd2: Pushed
81496d8c72c2: Pushed
644448d6e877: Pushed
0e41e5bdb921: Pushed
latest: digest: sha256:6f6a08f30723085c6a83be65d3654a2dd039611901ac5f17945b4932350e79e2 size: 2843
```

* You can find the docker image in AWS ECR. And pull docker image from ECR

```
docker pull 971879102683.dkr.ecr.us-east-1.amazonaws.com/python-03133021:latest
```

* Here is link to the image: 971879102683.dkr.ecr.us-east-1.amazonaws.com/python-03133021:latest


Deploy docker app to ECS
===

* Create an ECS cluster on AWS using console.
AWS ECS -> Create cluster -> (EC2 Linux + Networking) -> Cluster name, CatAppCluster, t3.small (2vCPU, 2G mem), VPC: default, Enable auto public IP, a new SG, -> Create.

* (Testing purpose, skip this one)As a new EC2 instance has been created, it can be found in EC2 dashboard. You can login using key. But I skipped this step right now.
```
ssh -i "~/Downloads/ecs.pem" ec2-user@ec2-18-215-146-170.compute-1.amazonaws.com
```

* On AWS ECS console, create a new task, EC2 (not fargate), 1 GB, 1 vCPU, Add container: that's where our container comes into play.  Add. Create. This is task creation. Next to run.

* Back to AWS ECS, click your cluster, sub-menu, Run new Task -> EC2, Task: ##, (You can run multiple masks on EC2...)  -> Run Task. Your docker image has been created as docker container in this ECS cluster. Task will change to status RUNNING.

* You can find two docker containers available on the EC2 instance:
```
CONTAINER ID        IMAGE                                                                 COMMAND             CREATED             STATUS                    PORTS                              NAMES
b0430f9aeb1e        971879102683.dkr.ecr.us-east-1.amazonaws.com/python-03133021:latest   "python ./app.py"   3 minutes ago       Up 3 minutes              5000/tcp, 0.0.0.0:8888->4000/tcp   ecs-ECSCatAppDemoTask-1-CatAppContainer-ac89b5cf8edffa9ff901
d04ba1af2b41        amazon/amazon-ecs-agent:latest                                        "/agent"            23 minutes ago      Up 23 minutes (healthy)                                      ecs-agent
[ec2-user@ip-172-31-25-239 ~]$
```

* In order to make docker container accessible from internet. That port :8888 mapping to docker container needs to open in Security group. Click SG -> pick up the SG, edit rule, add tcp:8888 to it.

* Since this docker container is running on EC2 instance which has a public IP. To access the docker container web app: http://ec2-18-212-216-153.compute-1.amazonaws.com:8888/   You web comes.




*
Reference:
===

Follow the curriculum on [docker-curriculum.com](https://docker-curriculum.com/)




Deploy a Docker App to AWS:
https://www.youtube.com/watch?v=zs3tyVgiBQQ

