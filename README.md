# WORK IN PROGRESS

# Deploying OPNFV Fuel on Ravello

## Prepare Jumphost

OPNFV deployments are done on PODs that have 6 nodes. One of these nodes is used as the jumphost where the deployment is initiated.
Please follow steps below to create a VM from Ravello and install dependencies.

Please do not shutdown Ravello or Fuel VMs until the installation is fully completed. (verify this by opening the Fuel Web Interface where you will get the details about upon the completion of the installation.)

1. Create a VM with the specs below.
    - VM flavor: Ubuntu Server 14.04.1 with qemu-kvm pre-installed
    - Hostname: jumphost
    - No of CPUs: 8
    - RAM: 16GB
    - Allow nested virtualization: true
    - Disk: 300GB
    - Network: Leave it default
2. Publish the application on Google.
3. Once the node is up, SSH into it, update the system and install the needed packages
    <pre><code>sudo apt-get update
    sudo apt-get upgrade -y
    sudo apt-get install -y git openjdk-7-jre python-dev libffi-dev libssl-dev sshpass qemu-kvm libvirt-bin libxml2-dev libxslt1-dev python-dev fuseiso genisoimage bridge-utils lxde virt-manager htop
</code></pre>
4. Update /etc/network/interfaces as below.
    <pre><code>iface eth0 inet manual
    auto pxebr
    iface pxebr inet static
        bridge_ports eth0
        address  10.20.0.253
        broadcast 10.20.0.255
        netmask 255.255.255.0
        gateway 10.20.0.1
        dns-nameservers 8.8.8.8
    </code></pre>
5. Edit /etc/hosts file and add jumphost there.
    <pre><code>127.0.0.1 jumphost</code></pre>
6. Reflect updates to Ravello VM on your browser.
7. Shutdown the VM, publish updates and start the VM again.

Please note that openjdk-7-jre is needed for connecting jumphost to Jenkins as slave and has no relation to OPNFV or Fuel themselves.
lxde, virt-manager, and htop are not normally needed but used in scope of this exercise to see what is happening. The deployment and testing in OPNFV CI do not require graphical user interface and these packages are not needed.

## Install OPNFV Fuel Master

1. Fetch the Fuel ISO that was released for OPNFV Brahmaputra release.
<pre><code>wget http://artifacts.opnfv.org/fuel/brahmaputra/opnfv-brahmaputra.1.0.iso</code></pre>
2. Start virt-manager.
    <pre><code>sudo virt-manager</code></pre>
3. Create a VM with the specs below.
    - Name: fuel
    - Installation media: Local install media
    - User ISO image: select the opnfv-brahmaputra.1.0.iso
    - OS type: Linux
    - Version: RHEL7
    - RAM: 8192MB
    - CPUs: 4
    - Disk: 100GB
    - Choose pxebr
4. When you click Finish, the OS installation should start. No need to do anything until you see the Fuel menu.
5. Once the Fuel menu is displayed, SSH into fuel VM, find the process fuelmenu and kill it. (root password is r00tme)
    <pre><code>ssh root@10.20.0.2</code></pre>
6. The Fuel installation should now proceed and no further input is needed from the user.
