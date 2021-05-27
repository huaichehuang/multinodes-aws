# Introduction
This repository provides the reference multi-nodes environment to install PrimeHub.

It will create these resources
1. Create 3 VMs for k8s nodes (10.40.0.11, 10.40.0.11, 10.40.0.13)
1. Create 1 VM for NFS and Harbor (10.40.0.14)
1. All 4 VMs are in the same VPC
1. All 4 VMs have public IPs
1. The OS image is AWS official Ubuntu 18.04 LTS

# Prerequisites

* AWS account
  * Prepare a AWS account. You can use a [free AWS account](https://aws.amazon.com/tw/free/)
  * Configure `$HOME/.aws`. Please see [AWS document](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)
  * Prepare [key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in AWS console
* Docker

## Deploy

 Variables | Description
-----------|------------------
 NAME      | Cluster name. We use it as the prefix of resources.
 USERNAME  | The owner of the cluster. We will tag resources with the tag `owner` with the USERNAME.
 KEY\_NAME | the name of your key pair

```bash
docker run --rm -it \
  -v $HOME/.aws:/root/.aws:ro \
  -e NAME=demo \
  -e USERNAME=demo \
  -e KEY_NAME=demo_key \
  infuseai/cdk:v0.1 cdk deploy
```

Once the creating completed, the output shows public IPs of created VMs. You can login these VMs by SSH command

```bash
ssh -i /path/to/saved_private_key -A ubuntu@<public_ip>
```

After logging in the first node, you can log in to other nodes via private IP

```bash
ssh -A 10.40.0.11
ssh -A 10.40.0.12
ssh -A 10.40.0.13
ssh -A 10.40.0.14
```

Optionally, you can connect to server with name. Edit the `~/.ssh/config`, add these settings

```bash
Host my_cluster_name
    User ubuntu
    IdentityFile /path/to/saved_private_key
    Hostname 1.2.3.4
    ForwardAgent yes
```

and connect again

```bash
ssh my_cluster_name
```

## Destroy

```bash
docker run --rm -it \
  -v $HOME/.aws:/root/.aws:ro \
  -e NAME=demo \
  -e USERNAME=demo \
  -e KEY_NAME=demo_key \
  infuseai/cdk:v0.1 cdk destroy
```
