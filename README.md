# Automated build of HA k3s Cluster with `kube-vip` (Optional)


This playbook will build an HA Kubernetes cluster with `k3s` and an optional virtual loadbalancer for control plane`kube-vip` (for bare metal)

This is based on the work from [this fork](https://github.com/techno-tim/k3s-ansible) which is based on the work from [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible).

## 📖 k3s Ansible Playbook

Build a Kubernetes cluster using Ansible with k3s. This is tested on the following machines:

- [x] Ubuntu (tested on version 22.04)

on processor architecture:

- [X] x64

## ✅ System requirements

- Deployment environment must have Ansible 2.4.0+.


- [`netaddr` package](https://pypi.org/project/netaddr/) must be available to Ansible. If you have installed Ansible via apt, this is already taken care of. If you have installed Ansible via `pip`, make sure to install `netaddr` into the respective virtual environment.

- `server` and `agent` nodes should have passwordless SSH access.

## 🚀 Getting Started

### 🍴 Preparation

Edit `inventory/k3scluster/hosts.ini` to match the system information gathered above

For example:

```ini
[master]
192.168.30.38

[node]
192.168.30.41
192.168.30.42

[k3s_cluster:children]
master
node
```

If multiple hosts are in the master group, the playbook will automatically set up k3s in [HA mode with etcd](https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/). 

The loadbalancing part to the master nodes ips should be done manually, if kube-vip does not work.
Kube-vip requires a virtual vip that is dedicated for it and free in the subnet. The network infrastructure should allow traffic to this virtual ip and proper filewall rule should be defined to allow this. This happens in layer 2 and they communicate to each other using ethernet frames.

This requires at least k3s version `1.19.1` however the version is configurable by using the `k3s_version` variable.

If needed, you can also edit `inventory/k3scluster/group_vars/all.yml` to match your environment.

### ☸️ Create Cluster

Start provisioning of the cluster using the following command:

```bash
ansible-playbook site.yml -i inventory/k3scluster/hosts.ini
```

### 🔥 Remove k3s cluster

```bash
ansible-playbook reset.yml -i inventory/my-cluster/hosts.ini
```

>You should also reboot these nodes due to the VIP not being destroyed

## ⚙️ Kube Config

After deployment the kube config file for the created cluster will be available in the Ansible control machine directory.

## Thanks 🤝

Thanks to these repos for code and ideas:

- [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible)
- [geerlingguy/turing-pi-cluster](https://github.com/geerlingguy/turing-pi-cluster)
- [212850a/k3s-ansible](https://github.com/212850a/k3s-ansible)
- [techno-tim/k3s-ansible](https://github.com/techno-tim/k3s-ansible)
