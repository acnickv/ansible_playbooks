# Overview

These instructions will provide some details for running the enclosed playbooks against local infrastructure.

# bootstrap.yml

## Goal
Create a playbook to bootstrap a newly installed raspbian installation from using the default `pi` user account, with very widely known credentials to a new account, accessible by a known SSH key.

## Preparation
To make life easier, the ansible portion of this work will be performed within a Docker container. This repo can be cloned locally and then run against the container.

As there are no longer officially support Ansible containers provided by Ansible or Red Hat, and I have not created an Ansible container myself, here are the quick instructions for for getting started:

1. Download the latest CentOS 7 container 
   
   `sudo docker pull docker.io/centos:centos7`
2. Start the container from the current working directory where the repo has been pulled: 
   
    `sudo docker -it -v ${PWD}:/mnt:ro centos:centos7 /bin/bash`
3. Once started, install both ansible as well as the ssh-agent needed for authentication (I ran this from my Mac, and was not in the mood at the time of doing this to share my SSH agent default location with my local Docker Engine): 
   
   `yum install -y openssh-clients ansible`
4. Navigate to the `/mnt` directory: `cd /mnt`
5. Start the SSH Agent and export the provided variables (I will leave this activity to the user to complete): 
   
   `ssh-agent`
6. Load the key: 
   
   `ssh-add ./<private_key_file>`. 
   
   Enter the password for the key when prompted

## Bootstrapping the Raspbian instance
Once the Raspbian instance has been started, you should be able to find the IP address for the device. Perhaps it is displayed on the screen, or you have run a network ARP scan or pulled the new IP from the DHCP server... regardless, without that IP address this activity is grounded.

With the new IP address, run the playbook against it. The entire activity should take no more than 30 seconds to complete:

`ansible-playbook -i <ipaddress>, bootstrap.yml -k`

When prompted for the SSH password, provide the string `raspberry`. This is the password for the `pi` account everyone knows and loves.

# SUCCESS!!
That was it! Now your raspbian instance has a new `ansible_svc` account installed with a known key, and the `pi` account has been purged from the system.