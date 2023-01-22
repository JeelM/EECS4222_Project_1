# Requirements

To move along this tutorial and set up the required tools and applications, you will
need access to a cloud computing infrastructure including a set of Virtual Machines (VMs).
You should have already received an email containing above information.
Make sure that you have ssh access to those resources and that you are comfortable
using the `bash` to interact with the server before you continue this tutorial.

For this tutorial,
imagine the provided ssh private and public key is stored in `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`
and thus will automatically be used for ssh communications; in case you stored the keys in a
different location, you need to use the `-i` option for all ssh commands, like below:

`# ssh username@ip_address -i private_key`

To set up a Kubernetes cluster, we will need different VMs to take `master` and `worker` roles.
`master` nodes will be responsible for keeping the cluster running and scheduling resources on available
nodes while `worker` nodes will be responsible for running those workloads.
You will need to specify one of the VMs assigned to you as the `master`, and the rest as `worker`s.
Throughout this tutorial, we assume a cluster of two nodes including a `master` with ip equal to `10.1.1.1` 
and a worker with ip of `10.1.1.2`. 
If you have more than one `worker` VM, repeat the worker VM commands for all other `workers`.


## OpenVPN Connection

To gain access to your VMs on our cloud, you will need to connect to our internal
network. To do so, you can use OpenVPN Connect to connect using the configuration file provided
to you. To use it, first download `OpenVPN Connect` from [their website](https://openvpn.net/download-open-vpn/). 
After the installation, you will be asked to create or import
a connection configuration. To do so, select the `File` tab and drag the provided connection
configuration onto OpenVPN Connect. Then, you will need to give the connection a name
and will be able to connect to the VPN server.

After connecting to the OpenVPN connection, you can test your connection by pinging the internal IP addresses
of the instances provided to you.

## SSH Access

To ssh onto the master or worker, use the default `ubuntu` user:

```sh
# ssh to the master
$ ssh ubuntu@10.1.1.1
# ssh to the worker
$ ssh ubuntu@10.1.1.2
# ssh to master if the private ssh key is not stored in the default place
$ ssh ubuntu@10.1.1.1 -i /PATH/TO/SSHKEY
```

To learn more about SSH and how you can use it, watch [this tutorial](https://youtu.be/YS5Zh7KExvE).

## Visual Studio Code

The easiest way to interact with your virtual machine is using Visual Studio Code 
Remote Development via SSH. To use this feature, you need to install the
[Visual Studio Code](https://code.visualstudio.com/) and the [Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh)
extension. You can follow the VSCode tutorials to connect to the `master VM` to follow with the
tutorials. The VMs used in this tutorial need to be a debian-based distribution and would
preferably be `Ubuntu 18.04` or `Ubuntu 20.04` with the standard `bash`.
We also recommend the installation of the following extensions:

- `Python` by `Microsoft`
- `Jupyter` by `Microsoft`
- `Kubernetes` by `Microsoft`
- `Pylance` by `Microsoft`

## Initial Setup

First, we need to update all packages installed on all VMs:

```sh
# update the package list and upgrade installed packages on all machines
(master and worker) $ sudo apt-get update && sudo apt-get upgrade -qy
```

In case you will be using more than one VM for your cluster, make sure that there
is network connectivity between your VMs by checking their `ping` status.

```sh
# ssh to the master and check connectivity with the workers
(master) $ ping 10.1.1.2
```

Make sure to replace `10.1.1.2` with your worker VM IPs.
You should see an output like this:

```console
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=1.00 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=0.416 ms
64 bytes from 10.1.1.2: icmp_seq=3 ttl=64 time=0.480 ms
64 bytes from 10.1.1.2: icmp_seq=4 ttl=64 time=0.471 ms
64 bytes from 10.1.1.2: icmp_seq=5 ttl=64 time=0.349 ms
64 bytes from 10.1.1.2: icmp_seq=6 ttl=64 time=0.348 ms
64 bytes from 10.1.1.2: icmp_seq=7 ttl=64 time=0.346 ms
64 bytes from 10.1.1.2: icmp_seq=8 ttl=64 time=0.362 ms
```

## Firewall Configurations

The firewall configuration has been already done on your VMs, but generally we need the following
ports to be open for this tutorial:

- `TCP` port `6443` for Kubernetes API
- `UDP` port `8472` for Flannel VXLAN (Kubernetes CNI)
- `TCP` port `10250` for kubelet
- `TCP` port `80` for the web application
- `TCP` port `9090` for prometheus
- `TCP` port `8091` for locust
- `TCP` port `3000` for grafana

## Anaconda Installation

In this project, we will be using Python for interacting with our cluster. Using other
programming languages is also possible, but might need additional setup from you. You
can install [Anaconda](https://docs.conda.io/en/latest/) to act as the environment manager on your cluster.
Run the following on your `master` node:

```sh
# download miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh
# install miniconda using non-interactive mode
bash ~/miniconda.sh -b -p $HOME/miniconda
# add bash hook for conda
echo 'eval "$(~/miniconda/bin/conda shell.bash hook)"' >> ~/.bashrc && source ~/.bashrc
```

After the installation is complete, you can test your installation using the following:

```console
$ conda --version
conda 4.9.2
```

Next, connect to the master node using visual studio code.
Then, open an empty file with `.py` extension to activate the python
extension on VS Code and click on the
`Select Python Interpreter` button on the bottom left corner of the window and
select `Python 3.8.x 64-bit ('base': conda)` to use as the default python
environment.

Now that we have done our initialization step, you are ready to install
Kubernetes and other required tools on your cluster. 

[Next Step](02-kubernetes.md) -->
