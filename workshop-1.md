# Google Cloud Build-with-AI Workshop-1

This guide provides instructions for setting up a custom VPC network, subnets, firewall rules, and a web server on Google Cloud Platform using `gcloud` commands.

---

# Prompts:

## 1. Create a VPC network named bwai-vpc. It should not have any subnets initially.
A Virtual Private Cloud (VPC) is your own private data center in the cloud.

* Go to the project homepage on Google Cloud and search for "VPC network".
* Click the shell icon on the top right to activate Cloud Shell.
* Create an isolated network that starts without automatic subnets.
```bash
gcloud compute networks create build-with-ai-vpc \
 --subnet-mode=custom
```

## 2. In this VPC, create a public subnet named bwai-vpc-pub-sub and a private subnet named bwai-vpc-priv-sub in the europe-west1 region. Choose appropriate /24 CIDRs for both subnets.
Subnets are subdivisions of your VPC that help you organize and secure your resources.
* Create a public subnet for resources like web servers that need to talk to the internet/resources that can be accessed from the internet.
```bash
gcloud compute networks subnets create build-with-ai-vpc-pub-sub \
  --network build-with-ai-vpc \
  --region europe-west1 \
  --range 10.0.1.0/24 \
  --enable-flow-logs
```

* Create a private subnet for internal resources like databases.
```bash
gcloud compute networks subnets create build-with-ai-vpc-priv-sub \
--network build-with-ai-vpc \
--region europe-west1 \
--range 10.0.2.0/24 \
--enable-private-ip-google-access
```

* Verify if both subnets are created.
```bash
gcloud compute networks subnets list --filter="network:build-with-ai-vpc"
```

## 3. Create a firewall rule named bwai-allow-http that allows incoming traffic on TCP port 80 from 0.0.0.0/0 to all instances in the bwai-vpc network.
* Google Cloud Platform blocks all incoming traffic on VMs by default.
* Create a firewall rule to allow traffic to your web server on port 80.
```bash
gcloud compute firewall-rules create build-with-ai-allow-http \
  --network build-with-ai-vpc \
  --allow=tcp:80 \
  --source-ranges 0.0.0.0/0 \
  --direction=INGRESS \
  --priority 1000 \
  --target-tags=http-server
```

## 4. Launch a VM named bwai-pub-web-server in the public subnet (region: europe-west1, type: e2-micro, OS: Ubuntu 22.04) that automatically installs nginx and creates /var/www/html/index.html with the text 'Hello $full_name from $event_name!' on startup.
* Create a VM and startup script so that it automatically updates the software list `apt-get update`, installs Nginx `apt-get install -y nginx`, creates a custom home page with your name `echo "Hello..."` and starts the web server so it's ready to handle visitors immediately.
```bash
gcloud compute instances create build-with-ai-pub-web-server \
    --zone=europe-west1-b \
    --machine-type=e2-micro \
    --subnet build-with-ai-vpc-pub-sub \
    --tags=http-server \
    --image-family ubuntu-2204-lts \
    --image-project ubuntu-os-cloud \
    --metadata startup-script='#!/bin/bash
    sudo apt-get update
    sudo apt-get install -y nginx
    echo "Hello YOUR_NAME from EVENT_NAME!" | sudo tee /var/www/html/index.html
    sudo systemctl enable nginx
    sudo systemctl start nginx'
```

## 5. Send a curl request to the virtual machine to check that the installation is completed successfully.
* Get your external ip with this:
```bash
gcloud compute instances list --filter="name=build-with-ai-pub-web-server"
```
* Run `curl http://<external_ip>` or type http://<external_ip> to get "Hello YOUR_NAME from EVENT_NAME!" on startup.
* You will see something like this:
