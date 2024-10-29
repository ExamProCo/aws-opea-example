## Setup

Set the compute to 1 T4

## Clone

```sh
git clone https://github.com/opea-project/GenAIExamples.git
```

## Add Hugging Face API Key

```sh
export HUGGINGFACEHUB_API_TOKEN="YOUR_KEY"
```

# Configuare

host_ip is the public IP address you will access the chat UI

We are setting no proxy since we are not going to have to proxy to gain access.

```sh
export host_ip=$(hostname -I | awk '{print $1}')
export no_proxy="localhost"
```

## Set Env Vars for GPU Nvidia

```sh
cd GenAIExamples/ChatQnA/docker_compose/nvidia/gpu/
source ./set_env.sh
```

```sh
docker compose up -d
```


```sh
curl http://$host_ip:8008/v1/chatqna \
    -H "Content-Type: application/json" \
    -d '{
        "messages": "What is the revenue of Nike in 2023?"
    }'
```