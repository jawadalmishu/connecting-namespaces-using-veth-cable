# Creating Two Network Namespaces and Connect them with Virtual Ethernet (Veth) Devices




## Introduction:
Network namespaces provide a powerful way to isolate network resources within a Linux system. By creating and connecting namespaces, you can simulate separate network environments and control network connectivity between them. In this blog post, we will explore how to create and connect namespaces using Vagrant and Ubuntu 18.04 or Centos 7.


## What are Network Namespaces?

Network namespaces provide isolated instances of the network stack, interfaces, and routing tables within a Linux system. With network namespaces, processes can operate within their own independent network environment, ensuring isolation from processes in other namespaces.

Each network namespace has its own set of network interfaces, IP addresses, routing tables, and firewall rules. This allows processes within a namespace to have their own unique network configuration, separate from processes in other namespaces.

By utilizing network namespaces, administrators can create isolated network environments for different applications or users, preventing interference or conflicts between them. It also enables the implementation of network virtualization and containerization technologies, where each container or virtual machine can operate in its own network namespace, ensuring network isolation and security.
Overall, network namespaces provide a powerful tool for managing network resources and achieving network isolation within a Linux system. They contribute to enhancing security, scalability, and flexibility in various network-related applications and environments.
## What are Veth Devices?

Veth devices, or virtual Ethernet devices, are a pair of virtual network interfaces that serve as a connection between network namespaces. Each veth pair consists of two ends: one end is located within a specific namespace, while the other end resides in a different namespace. These virtual interfaces function similarly to physical Ethernet cables, enabling communication between the connected namespaces.

With veth devices, traffic can be transmitted bidirectionally through the virtual interface pair. Data can flow from one namespace to another through this virtual link, allowing processes within the namespaces to communicate as if they were connected by a physical Ethernet cable.

By utilizing veth devices, network namespaces can be interconnected, facilitating the creation of complex network topologies and enabling isolated network environments to communicate with one another. Veth pairs provide a flexible and efficient means of connecting namespaces within a Linux system.
## Prerequisites:

- Basic familiarity with Linux and networking concepts.
- Vagrant, Git and VirtualBox installed on your machine.

## Step 1: Setting Up the Environment:
First you need to install oracle virtual box, git and vagrant. Now create a new folder name vagrant-vms(you can give any name as your wish). Now go to vagrant-vms folder and create another folder centos7(for rpm based) or ubuntu18(for debian based). Now go to centos7 folder and open git bash terminal here.Now run this command in the git bash.

```ruby
vagrant init geerlingguy/centos7
```

It will create the centos 7 virtual os, but it will take some time.
If you want to install ubuntu 18, run this command.

```ruby
vagrant init ubuntu/bionic64
```

Now open the oracle virtual box, you will see this two os.


## Step 2: Launching the Virtual Machine:
1. Now we have created our virtual os. So, run the following command to start the virtual machine:

```ruby
vagrant up
```

2. Once the VM is up and running, access the command prompt within the VM by running:

```ruby
vagrant ssh
```
## Step 3: Creating and Connecting Namespaces:

Now that we have our Centos VM up and running, let's proceed with creating and connecting namespaces.

1. In the VM's command prompt, run the following commands to create the namespaces:

```ruby
sudo ip netns add ns1
sudo ip netns add ns2
```

2. Next, create a virtual Ethernet cable (Veth) that connects the namespaces:

```ruby
sudo ip link set veth1 netns ns1
sudo ip link set veth2 netns ns2
```

3. Configure IP addresses for the Veth interfaces:

```ruby
sudo ip netns exec ns1 ip addr add 10.0.0.1/24 dev veth1
sudo ip netns exec ns2 ip addr add 10.0.0.2/24 dev veth2
```

4. Enable the Veth interfaces:

```ruby
sudo ip netns exec ns1 ip link set dev veth1 up
sudo ip netns exec ns2 ip link set dev veth2 up
```
## Step 4: Testing the Connectivity:
To test the connectivity, let's open two terminals, one for each namespace:


Terminal 1 (for ns1):
```ruby
sudo ip netns exec ns1 bash
```

Terminal 2 (for ns2):
```ruby
sudo ip netns exec ns2 bash
```

In Terminal 1 (ns1), you can now ping the IP address of veth2 in ns2:
```ruby
ping 10.0.0.2
```

If everything is set up correctly, you should see successful ICMP echo replies.
