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