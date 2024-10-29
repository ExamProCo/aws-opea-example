https://github.com/opea-project/GenAIExamples/tree/main/ChatQnA/docker_compose/intel/cpu/xeon

- m7i.4xlarge
- 80 GB

# Install Docker

https://docs.docker.com/engine/install/linux-postinstall/#:~:text=If%20you%20don't%20want,members%20of%20the%20docker%20group.

```
./install_docker.sh
```
## Setup Docker to work with non root user

```sh
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

 Verify
 
 ```sh
docker run hello-world
 ```

# Clone
git clone https://github.com/opea-project/GenAIExamples.git
export HUGGINGFACEHUB_API_TOKEN="YOUR_KEY"
export host_ip=$(hostname -I | awk '{print $1}')
export no_proxy="localhost"
cd GenAIExamples/ChatQnA/docker_compose/intel/cpu/xeon/
docker compose -f compose.yaml up -d

url localhost:8888/v1/chatqna \
-H "Content-Type: application/json" \
-d '{
    "messages": "What is the revenue of Nike in 2023?"
}'

-------
-------
-------

# AWS OPEA Example

This is an example repo to deploy ChatQnA to an EC2 instance.

## Scope

The goal of this example it show you with the least moving parts
how to deploy and monitor OPEA ChatQnA megaservice.

We'll be deploying two EC2 instances:

1. Montoring Service
- We'll install Grafana server to serve a dashboard
- We'll install Prometheus to accept metrics from other nodes
2. Chatbot Service
- We'll install docker
- We'll install Prometheus Node Explorer to export metrics
- We'll deploy ChatQnA megaservice to docker container

## Monitoring with Grafana and Prometheus

### Accessing the dashboard

- The Grafana server will start on port `3000`
- In our EC2 security group we we'll ensure `HTTP` is open to `0.0.0.0/0` for Port `3000`
- The default username and password for Grafana is `admin` / `admin`
  - You will be asked to change the default `admin` password and we will change this to `Testing123456!`