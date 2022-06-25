# Kubernetes Cluster with Kubeadm

To setup a K8s cluster we need at least two hosts, one for Master Node and another for Worker Node.

## Requirements

### Node Resources

|Node|CPU (cores)|Memory (GB)|
|:-:|:-:|:-:|
|Master|2|4|
|Worker|1|2|

### Sysctl Network

```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

### CRI

Use *containerd* as CRI:

1. Install containerd: `sudo apt install containerd runc`
2. Create config.toml:

    ```shell
    # mkdir -p /etc/containerd
    # containerd config default | sudo tee /etc/containerd/config.toml
    ```

3. Enable cgroup systemd

    ```toml
    [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
        ...
        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            SystemdCgroup = true
    ```

    or with one command: `sudo sed -i 's/            SystemdCgroup = false/            SystemdCgroup = true/' /etc/containerd/config.toml`

4. Restart service: `sudo systemctl restart containerd`

### Ports

See Kubernetes ports <https://kubernetes.io/docs/reference/ports-and-protocols/>.

---

## Kubernetes Setup

### Install Packages

1. Base packages:

    ```shell
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl
    ```

2. Google public sign key and K8s repo:

    ```shell
    sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

    echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    ```

3. Install K8s packages:

    ```shell
    sudo apt-get update
    sudo apt-get install -y kubelet kubeadm kubectl
    sudo apt-mark hold kubelet kubeadm kubectl
    ```

### Bring Up the Cluster

#### Master Node

1. `sudo kubeadm init  --pod-network-cidr=192.168.0.0/16`.

2. To run *kubectl* as non-root:

    ```shell
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```

3. Remove master Node taint: `kubectl taint nodes --all node-role.kubernetes.io/master-`
4. Install Calico:

    ```shell
    kubectl create -f https://projectcalico.docs.tigera.io/manifests/tigera-operator.yaml
    kubectl create -f https://projectcalico.docs.tigera.io/manifests/custom-resources.yaml
    ```

    > More info on <https://projectcalico.docs.tigera.io/getting-started/kubernetes/quickstart>.

5. Cluster join command: `kubeadm token create --print-join-command`

#### Worker Node

Use the output of *join command* in worker Nodes.
