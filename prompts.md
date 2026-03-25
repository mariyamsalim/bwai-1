# Google Cloud Build-with-AI Workshop-1 ☁️
<img src="https://github.com/user-attachments/assets/90fc6635-27f3-49ef-aefd-7cc5a9130d9c" width="250" />

## Prompts:

1. Create a VPC network named bwai-vpc. It should not have any subnets initially.
2.  In this VPC, create a public subnet named bwai-vpc-pub-sub and a private subnet named bwai-vpc-priv-sub in the europe-west1 region. Choose appropriate /24 CIDRs for both subnets.
3.  Create a firewall rule named bwai-allow-http that allows incoming traffic on TCP port 80 from 0.0.0.0/0 to all instances in the bwai-vpc network.
4.  Launch a VM named bwai-pub-web-server in the public subnet (region: europe-west1, type: e2-micro, OS: Ubuntu 22.04) that automatically installs nginx and creates /var/www/html/index.html with the text 'Hello $full_name from $event_name!' on startup.
5.  Send a curl request to the virtual machine to check that the installation is completed successfully.


