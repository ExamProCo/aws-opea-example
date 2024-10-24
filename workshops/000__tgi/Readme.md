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


# Install LFS

Since we are going to
```sh
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt-get install git-lfs
git lfs install
```

## Download the model (llama-3.2-1b Completion)

> You should be able to download using the hugging cli but it didn't work for me so we'll ignore this

```sh (DONT DO)
sudo mkdir /models
sudo chown ubuntu:ubuntu /models
huggingface-cli login
huggingface-cli download meta-llama/llama-3.2-1b --repo-type model --cache-dir /models
```
The other way according to the docs is to use git

> https://huggingface.co/docs/hub/en/models-downloading

```sh
git clone https://huggingface.co/meta-llama/llama-3.2-1b /models/meta-llama/llama-3.2-1b
```

> We could possibly also use  the TGI CLI to donwload model weights as well...
> https://huggingface.co/docs/text-generation-inference/en/basic_tutorials/using_cli

## Download the model (Llama-3.2-1B-Instruct Chat Completion)

We should use this one but we will run into issues when starting TGI which will require us to modify the tokenizer_configur
```sh (Dont use)
git clone https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct /models/meta-llama/Llama-3.2-1B-Instruct
```

> The model you're using (meta-llama/Llama-3.2-1B-Instruct) has a tokenizer that does not have a pad_token defined. You can manually set the pad_token to the eos_token (End Of Sequence token). This is a known issue with some models and modifying the tokenizer_config.json is necessary

We need to add or update the pad_token to be the name value as the eos_token 

vim /models/meta-llama/Llama-3.2-1B-Instruct/tokenizer_config.json

however we can also use this python script which will be less error prone:

```sh
from transformers import AutoTokenizer

model_path = "/data/models/meta-llama/Llama-3.2-1B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_path)
tokenizer.pad_token = tokenizer.eos_token
tokenizer.save_pretrained(model_path)
```

##  Run TGI and Consume for Completion

```sh
sudo docker run -d \
-p 8080:80 \
-v /models:/data/models \
ghcr.io/huggingface/text-generation-inference:latest \
--model-id /data/models/meta-llama/llama-3.2-1b \
--port 80
```

Ensure the TGI service gets into a Connected state or check for errors to fix your model issues.

```sh
sudo docker ps # get container id
sudo docker logs <container id>
```

```sh
curl -X POST "http://localhost:8080/v1/completions" \
-H "Content-Type: application/json" \
--data '{
    "model": "/data/models/meta-llama/llama-3.2-1b",
    "prompt": "What is the capital of France?",
    "max_tokens": 50
}'
```
> In hugging face they show you similar commands to run

##  Run TGI and Consume for Chat Completion

```sh
sudo docker run -d \
-p 8080:80 \
-v /models:/data/models \
ghcr.io/huggingface/text-generation-inference:latest \
--model-id /data/models/meta-llama/Llama-3.2-1B-Instruct \
--port 80
```

``` sh
curl -X POST "http://localhost:8080/v1/chat/completions" \
-H "Content-Type: application/json" \
--data '{
    "messages": [
        {"role": "user", "content": "What is the capital of France?"},
        {"role":"assistant","content":"The capital of France is Paris."},
        {"role": "user", "content": "Are you sure?"}
    ]
}'
```

