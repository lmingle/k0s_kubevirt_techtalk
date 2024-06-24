# k0s-kubevirt Tech talk

## Installation steps
## k0s
### Install k0sctl 
https://github.com/k0sproject/k0sctl

### Setup simple 2 node cluster
sample config file can be found at ./installation/kubevirt-cluster.yaml

### Install k0s cluster
    k0sctl apply --config ./installation/kubevirt-cluster.yaml 

### Retrieve kubeconfig
    k0sctl kubeconfig --config ./installation/kubevirt-cluster.yaml

### Update ip address from private ip to public if EC2

## kubevirt
### Validate Hardware Virtualization support on the worker nodes
    
    sudo apt update
    
    sudo apt install libvirt-clients

    virt-host-validate qemu


### *Fix FAIL (Check /dev/kvm is world writable or you are in a group that is allowed to access it)
    # sudo adduser <username> kvm
    sudo adduser ubuntu kvm

    #sudo chown <username> /dev/kvm
    sudo chown ubuntu /dev/kvm

### install kubevirt
    # Point to latest release
    export RELEASE=$(curl https://storage.googleapis.com/kubevirt-prow/release/kubevirt/kubevirt/stable.txt)

    # Deploy the KubeVirt operator
    kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${RELEASE}/kubevirt-operator.yaml

    # Create the KubeVirt CR (instance deployment request) which triggers the actual installation
    kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${RELEASE}/kubevirt-cr.yaml

### install virtctl
    export VERSION=$(curl https://storage.googleapis.com/kubevirt-prow/release/kubevirt/kubevirt/stable.txt)

    wget https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/virtctl-${VERSION}-linux-amd64

### on worker node,  specify k0s’ specific kubelet path for kubevirt
    echo "/var/lib/k0s/kubelet /var/lib/kubelet none defaults,bind 0 0" >> /etc/fstab
    mount -a



## Usage
Sample vm file in ./vm-definitions/ubuntu.yaml

    kubectl apply -f ./vm-definitions/ubuntu.yaml
    virtctl start ubuntu
    virtctl console ubuntu
    virtctl stop ubuntu