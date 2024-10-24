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

```sh
sudo mkdir /models
sudo chown ubuntu:ubuntu /models
huggingface-cli login
huggingface-cli download meta-llama/llama-3.2-1b --repo-type model --cache-dir /models
```

## TGI

```sh
sudo docker run -d -p 8080:80 \
    -v /models:/data/models \
    ghcr.io/huggingface/text-generation-inference:latest \
    --model-id /data/models \
    --port 80 \
    --device cpu
```

docker run -it -p 8080:80 \
    -v /models:/data/models \
    ghcr.io/huggingface/text-generation-inference:latest \
    --model-id /data/models/models--meta-llama--llama-3.2-1b/ \
    --port 80 \
    --device cpu