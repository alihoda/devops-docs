# Docker Network

## Network Types

### 1. Bridge

- *docker0*
- Default network interface

> To connect to the containers from outside, the port has to be exposed by `-p HostPort:ContainerPort` in `docker` command.

### 2. User-Defined Bridge

- Just like the *Bridge*
- Has internal DNS
- Recommended to use over than the *Bridge*
- Create on via `docker network create test-bridge`

### 3. Host

- Run container like an application on the Host
- Use host IP and MAC address
- No need to expose any ports
- And there is no isolation!!

### 4. MacVlan

- Connect containers directly to physical network (like a virtual machine)
- Get their own IP and MAC address
- No virtual interfaces
- Create on via `docker network create -d macvlan --subnet 10.10.10.0/24 --gateway 10.10.10.1 -o parent=enp3s0 newnet`

> To connect a container to a *macvlan* network the container's IP has to be specified like: `docker run -itd --rm --network newnet --ip 10.10.10.10 --name test busybox`.
>
> If a container needs to ping (or talk) to others, the *promiscuous* mode needs to be enabled. Because actually all containers that connect to a *macvlan* network, they use same switch port which the host uses. Enable *promiscuous* mode via `sudo ip link set enp3s0 promisc on`.

#### Prob

- All the benefits of *bridge*;
- Use DNS to resolve names;
- No need to expose ports.

#### Cons

- There is no DHCP, so IPs should be defined manually (IP conflict);
- The MAC address is on *promiscuous* mode which there is no control over it. 

### 5. IPVlan (L2)

- Allow host to share its network interface with containers
- There all have same one MAC address but they have their own IP (one MAC, multiple IPs)
- No need the *promiscuous* mode
- Create one via `docker network create -d ipvlan --subnet 10.10.10.0/24 --gateway 10.10.10.1 -o parent=enp3s0 newnet`

### 6. IPVlan (L3)

- Containers connect to host's interface and it acts like a router
- There is no more broadcast traffic
- Create one via `docker network create -d ipvlan --subnet 10.10.10.0/24 -o parent=enp3s0 -o ipvlan_mode=l3 --subnet 10.10.20.0/24 newnet`

> Containers inside a subnet can not talk to outside because there is no route in *route table*!! The static route should be added manually.

### 7. None

- It just add a *loopback* interface and does not create any virtual network interface (perfectly secure)!!
- `docker network create -d None test-none`
