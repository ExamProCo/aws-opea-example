# Goal

In this workshop we are going to:
- Launch an EC2 instance and install docker
- Download a model from hugging face into a local file
- Start up TGI and invoke the model.

# Launch EC2 Instance

- c5.4xlarge (16 vCPU 32 GB) - $0.78 USD
- Ubuntu 24
- 60 GB


# Switch Users
```sh
sudo su - ubuntu
```

# Install Docker

```sh
./install_docker.sh
```

# Install Miniconda

```sh
./install_miniconda.sh
```

# Setup Conda Env

```sh
conda create -n myenv python=3.12
conda activate myenv
```

# Install Conda

```sh
conda install git
git config --global credential.helper store
```

# Install HuggingFace CLI

```sh
conda install -c conda-forge pip
pip install huggingface_hub
```


## Download the model

> You should be able to download using the hugging cli but it didn't work for me
```sh
sudo mkdir /models
sudo chown ubuntu:ubuntu /models
huggingface-cli login
huggingface-cli download meta-llama/llama-3.2-1b --repo-type model --cache-dir /models
```
The other way according to the docs is to use git

> https://huggingface.co/docs/hub/en/models-downloading

```sh
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt-get install git-lfs
git lfs install
git clone https://huggingface.co/meta-llama/llama-3.2-1b /models/meta-llama/llama-3.2-1b
```

## Install and run TGI

```sh
sudo docker run -d -p 8080:80 \
    -v /models:/data/models \
    ghcr.io/huggingface/text-generation-inference:latest \
    --model-id /data/models/meta-llama/llama-3.2-1b \
    --port 80
```

> In hugging face they show you how to run with TGI.

```sh
curl -X POST "http://localhost:8080/v1/completions" \
-H "Content-Type: application/json" \
--data '{
    "model": "/data/models/meta-llama/llama-3.2-1b",
    "prompt": "What is the capital of France?",
    "max_tokens": 50
}'
```

> This model we are using will not work with chat/completions because its not fine tune for conversations.

``` sh
curl -X POST "http://localhost:8080/v1/chat/completions" \
-H "Content-Type: application/json" \ 
--data '{"model": "meta-llama/Llama-3.2-1B","messages": [{"role": "user", "content": "What is the capital of France?"}]}'
```