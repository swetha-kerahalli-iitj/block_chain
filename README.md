Reference from (https://medium.com).
For Q1,Q5, I followed google class recorded session, where I have created 10 nodes and connected with each other by giving connect_chain query present in usernodes folder in git repo https://github.com/swetha-kerahalli-iitj/block_chain
For Q1, connecting atleast 2 users,Q2,Q3,Q4,Q6,Q7, I have followed reference from (https://medium.com) named folder as src/node and src/wallet

For Q1,Q5, followed steps for execution mentioned in the gc recorded session.
Follow the below steps to execute for Q1 connecting users,Q2,Q3,Q4,Q6,Q7.

## Requirements

### Python version
Python 3.9

### Libraries
Install libraries with pip:
`pip3 install -r requirements.txt`

### Hardware

- Processor: 2 vCPU
- Memory: 4GB
- Storage: 50GB

## Installation Guide

You should have access to a virtual machine running Ubuntu 20.04. 
You should install Kubernetes on this VM and deploy this application on it.

### Setting up Microk8s
Follow [Microk8s](https://microk8s.io/docs) to install microk8s using snap in virtual machine

```bash
ubuntu@ip-10-0-0-54:~$ sudo apt-get update && sudo apt-get upgrade
ubuntu@ip-10-0-0-54:~$ sudo snap install microk8s --classic
```

Update your user's permission to be added to the microk8s group:
```bash
ubuntu@ip-10-0-0-54:~$ sudo usermod -a -G microk8s ubuntu
ubuntu@ip-10-0-0-54:~$ sudo chown -f -R ubuntu ~/.kube
```

After changing those permissions, you'll have to create a new shell for them to take effect, so you 
can exit and re-ssh to the machine. Once you're in again, enable some add-ons to your microk8s cluster:
```bash
ubuntu@ip-10-0-0-54:~$ microk8s enable dns ingress storage
```

Use MetalLB as our load balancer for Kubernetes. It can be enabled the same way as the other add-ons:
```bash
ubuntu@ip-10-0-0-54:~$ microk8s enable metallb
```

You will be asked for a range of IP's to provide, answer with the range of private addresses you want, here I'll use : `10.0.1.1–10.0.1.254`.


### Deploying the node using kubectl
MicroK8s uses a namespaced kubectl command to prevent conflicts with any existing installs of kubectl.
```bash
ubuntu@ip-10-0-0-54:~$ alias kubectl='microk8s kubectl'
```

Re-run the command every time you log back in. Now clone the code from the github and head to the deploy directory:
```bash
ubuntu@ip-10-0-0-54:~$ git clone https://github.com/swetha-kerahalli-iitj/block_chain
ubuntu@ip-10-0-0-54:~$ cd block_chain/deploy/
```

Deploy the blockchain using kubectl apply:
```bash
ubuntu@ip-10-0-0-54:~/block_chain/deploy$ kubectl apply -f kubernetes/
```

Voilà, you now have a node running.

### Validate the Kubernetes deployment
Validate that the service is correctly created:
```bash
ubuntu@ip-10-0-0-54:~$ kubectl get svc
NAME            TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP      10.152.183.1     <none>        443/TCP        22m
block_chain   LoadBalancer   10.152.183.158   10.0.1.1      80:32301/TCP   20m
```

Deployment is up:

```bash
ubuntu@ip-10-0-0-54:~$ kubectl get pods
NAME                            READY   STATUS    RESTARTS   AGE
block_chain-b9d844446-9hnzg   1/1     Running   0          21m
```

### Validate Networking
In virtual machine validate the service. 
Here the IP is the EXTERNAL-IP associated to the block_chain service. Make sure you use the correct one:

```bash
ubuntu@ip-10-0-0-54:~$ curl 10.0.1.1/block
```


## New blockchain user 
We have 4 users of the blockchain in the current implementation: swetha, suma, vipura and the miner. To create a 
new user, you will have to generate a new public/private key pair. To do so, you can simply run the 
"new_user_creation.py" script inside of the `common` directory:
```bash
export PYTHONPATH=src
python src/common/new_user_creation.py 
```
The output will print you new public/private keys that you will be able to use.
