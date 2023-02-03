# Requirements

To move along this tutorial and set up the required tools and applications, you will
need access to a cloud computing infrastructure including a set of Virtual Machines (VMs).


<!-- You should have already received an email containing above information. -->

So far, you should be able to get connected to the Windows machine by using RDP installed on your laptop. Also, you should have Hyper-V Manager installed on Windows. Make sure you have access to these resources and are comfortable using the `bash` to interact with the server before you continue this tutorial.

<!-- For this tutorial,
imagine the provided ssh private and public key is stored in `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`
and thus will automatically be used for ssh communications; in case you stored the keys in a
different location, you need to use the `-i` option for all ssh commands, like below:

`# ssh username@ip_address -i private_key` -->

To set up a Kubernetes cluster, we will need different VMs to take `master`, `worker`, and `cluster head` roles.
`master` nodes will be responsible for keeping the cluster running and scheduling resources on available
nodes while `worker` nodes will be responsible for running those workloads. Cluster head manages the nodes in the Kubernetes cluster and joins the worker node to the master.
Throughout this tutorial, we assume a cluster of following nodes: </br>
1. Master node: server1 with IP address 192.168.0.100
2. Worker node: server2 with IP address 192.168.0.101
3. Cluster head: serevr3 with IP address 192.168.0.102

<!-- ## OpenVPN Connection

To gain access to your VMs on our cloud, you will need to connect to our internal
network. To do so, you can use OpenVPN Connect to connect using the configuration file provided
to you. To use it, first download `OpenVPN Connect` from [their website](https://openvpn.net/download-open-vpn/). 
After the installation, you will be asked to create or import
a connection configuration. To do so, select the `File` tab and drag the provided connection
configuration onto OpenVPN Connect. Then, you will need to give the connection a name
and will be able to connect to the VPN server.

After connecting to the OpenVPN connection, you can test your connection by pinging the internal IP addresses
of the instances provided to you. -->

## SSH Access

To ssh onto the master or worker, use the default `eecs` user. The password for 'eecs' user is 'eecs'.

```sh
# ssh to the master
$ ssh eecs@192.168.0.100
# ssh to the worker
$ ssh eecs@192.168.0.101
# ssh to the cluster head
$ ssh eecs@192.168.0.102
```

<!-- 
To ssh onto the master or worker, use the default `ubuntu` user:

```sh
# ssh to the master
$ ssh ubuntu@10.1.1.1
# ssh to the worker
$ ssh ubuntu@10.1.1.2
# ssh to master if the private ssh key is not stored in the default place
$ ssh ubuntu@10.1.1.1 -i /PATH/TO/SSHKEY
``` -->

To learn more about SSH and how you can use it, watch [this tutorial](https://youtu.be/YS5Zh7KExvE).

<!-- ## Visual Studio Code

The easiest way to interact with your virtual machine is using Visual Studio Code 
Remote Development via SSH. To use this feature, you need to install the
[Visual Studio Code](https://code.visualstudio.com/) and the [Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh#_connect-to-a-remote-host)
extension. You can follow the VSCode tutorials to connect to the `master VM` to follow with the
tutorials.<br />
VS code -> View tab -> Command Palette <br />
Option: **Remote SSH: Connect to Host**<br />
Add new SSH Host:<br />
ssh eecs@192.168.0.100 <br/>
select a SSH configuration file to update -> /Users/username/.ssh/config<br />
from the bottom of the window select connect button<br />
(if asked enter the pass phrase for private key and press enter)<br />

We also recommend the installation of the following extensions (From the right panel select Extensions):

- `Python` by `Microsoft`
- `Jupyter` by `Microsoft`
- `Kubernetes` by `Microsoft`
- `Pylance` by `Microsoft` -->

## Initial Setup

First, we need to update all packages installed on all VMs:

```sh
# update the package list and upgrade installed packages on all machines
(master, worker and cluster head) $ sudo apt-get update && sudo apt-get upgrade -qy
```

In case you will be using more than one VM for your cluster, make sure that there
is network connectivity between your VMs by checking their `ping` status.

```sh
# ssh to the master and check connectivity with the workers
(master) $ ping 192.168.0.100
```

Make sure to replace `192.168.0.101` with your worker VM IP.
You should see an output like this:

```console
Pinging 192.168.0.101 with 32 bytes of data:
Reply from 192.168.0.101: bytes=32 time<1ms TTL=64
Reply from 192.168.0.101: bytes=32 time<1ms TTL=64
Reply from 192.168.0.101: bytes=32 time<1ms TTL=64
Reply from 192.168.0.101: bytes=32 time<1ms TTL=64

Ping statistics for 192.168.0.101:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

## SSH-key

SSH keys are used to establish secure connections between two devices, allowing for secure and authenticated access to remote systems. SSH keys provide a more secure way to log in to a remote server than using a password alone. An SSH key pair typically consists of a private key, which should be kept confidential and stored on the client machine, and a public key, which can be shared and stored on remote servers. When connecting to a remote server using SSH, the client first authenticates using its private key, and the server then verifies the authenticity of the client's public key. If the public key matches the private key, the server grants access to the client. 
In this project, we generate ssh key on server3 and then copy the ssh public key to server1 and server2.



```console
$ ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/home/eecs/.ssh/id_rsa): [enter]
Enter passphrase (empty for no passphrase): [enter]
Enter same passphrase again: [enter]
Your identification has been saved in /home/eecs/.ssh/id_rsa
Your public key has been saved in /home/eecs/.ssh/id_rsa.pub
...
```

```sh
# This copies the server3's public key to the server1 and server2 authorized keys file
$ ssh-copy-id eecs@192.168.0.100
$ ssh-copy-id eecs@192.168.0.101
```

## Give 'eecs' user some priviledges
For some of the commands, you need to write 'sudo' to run the command with the root access. 'sudo' asks password from the user. By adding `eecs ALL=(ALL) NOPASSWD:ALL` at the end of the sudoers file, eecs user won't have to enter password when writing sudo.

```sh
# open sudoers file
$ sudo visudo

# add following command at the end of the file
eecs ALL=(ALL) NOPASSWD:ALL

```



<!-- 
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
 -->
 
<!-- ## Firewall Configurations

The firewall configuration has been already done on your VMs, but generally we need the following
ports to be open for this tutorial:

- `TCP` port `6443` for Kubernetes API
- `UDP` port `8472` for Flannel VXLAN (Kubernetes CNI)
- `TCP` port `10250` for kubelet
- `TCP` port `80` for the web application
- `TCP` port `9090` for prometheus
- `TCP` port `8091` for locust
- `TCP` port `3000` for grafana -->

## Anaconda Installation

In this project, we will be using Python for interacting with our cluster. Using other
programming languages is also possible, but might need additional setup from you. You
can install [Anaconda](https://docs.conda.io/en/latest/) to act as the environment manager on your cluster.
**Run the following on your `master` node:**

```sh
# download miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh
# install miniconda using non-interactive mode
bash ~/miniconda.sh -b -p $HOME/miniconda
# add bash hook for conda
echo 'eval "$(~/miniconda/bin/conda shell.bash hook)"' >> ~/.bashrc && source ~/.bashrc
```

After the installation is complete, you can test your installation using the following (It's not important to have the same version):

```console
$ conda --version
conda 22.11.1
```

<!-- Next, connect to the master node using visual studio code.
Then, open an empty file with `.py` extension to activate the python
extension on VS Code and click on the
`Select Python Interpreter` button on the bottom left corner of the window and
select `Python ... ('base': conda)` to use as the default python
environment. -->

Now that we have done our initialization step, you are ready to install
Kubernetes and other required tools on your cluster. 

[Next Step](02-kubernetes.md) -->
