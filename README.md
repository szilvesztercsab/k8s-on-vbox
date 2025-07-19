# Kubernetes on VirtualBox

A classic, minimal, multi-node K8s setup on Debian 12 and VirtualBox via Vagrant and Ansible.

<!-- TODO: explain why not just use k3d -->
<!-- FIXME: explain hacks and non-defaults in detail (blog post maybe) -->
<!-- TODO: add thanks/sources/links to docs or posts that inspired this -->

## Contents

- **[`Vagrantfile`](./Vagrantfile)**: the Vagrant configuration file for setting up the virtual machines.
- **[`main.yml`](./main.yml)**: the main Ansible playbook for provisioning.
  - **[`requirements.yml`](./requirements.yml)**: the Ansible Galaxy requirements file listing the roles and collections to be installed.
- **[`kube-flannel.yml.patch`](./kube-flannel.yml.patch)**: _a nasty hack_, a patch file for the `kube-flannel.yml` manifest to make it work with VirtualBox networking.

  `flanneld` needs to bind to the `eth1` interface, which is the second network interface of the VMs and not the first (default).

  _See: `--iface` at <https://github.com/flannel-io/flannel/blob/master/Documentation/configuration.md>_).

## Usage

0. Requirements:

   - **Debian 12 (bookworm)**: on the host.
   - **Vagrant**: <https://www.vagrantup.com/downloads>
   - **VirtualBox**: <https://www.virtualbox.org/wiki/Downloads>
   - **Ansible**: <https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible-with-pipx>

   _Note: see [Tested with](#tested-with) below for the specific versions used._

   You should also back up your `~/.kube/config` file as it will be overwritten by the provisioning.

1. Clone this repository.
2. Start and provision the cluster with Vagrant:

   ```sh
   vagrant up
   ```

3. You should be able to access the Kubernetes on your host with `kubectl`:

   ```sh
   kubectl cluster-info
   kubectl get nodes
   ```

4. Destroy the cluster when you are done:

   ```sh
   vagrant destroy -f
   rm ~/.kube/config
   ```

## Tested with

- **Debian 12 (bookworm)**: The primary operating system of the host and the VMs.
- **Virtualbox (v7.1.12)**: The virtualization platform used for the VMs.
- **Ansible (v2.18.7)**: The configuration management tool used for provisioning.
- **Vagrant (v2.4.7)**: The tool used for managing the virtual machines.
- _so far_

**Note**: Your mileage may vary with other versions or distributions.

## MIT License

This project is licensed under the MIT License. See the [LICENSE.md](./LICENSE.md) file for details.
