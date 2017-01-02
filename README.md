
# coreloko

Create CoreOS clusters locally, with QEMU, KVM, and libvirt.

Clusters match the CoreOS [matchbox](https://github.com/coreos/coreos-baremetal/tree/master/examples) examples, but boot and provision faster since they don't need to mirror production bare-metal network booting.

## Usage

Download and verify CoreOS images.

    sudo ./coreloko init

Generate Ignition user-data for nodes. By default, a minimal Ignition file with your SSH public key will be generated.

    ./coreloko userdata

Create QEMU/KVM cluster nodes.

    sudo ./coreloko create

## Manage

Use the coreloko wrapper to act on all nodes in a cluster.

    sudo ./coreloko [start|reboot|shutdown|poweroff|destroy]

SSH to the IP address or use the serial console.

    sudo virsh console node1
    ssh core@address

## Dependencies

Install the QEMU/KVM and libvirt package dependencies.

    # Fedora
    sudo dnf install virt-install virt-manager

    # Debian/Ubuntu
    sudo apt-get install virt-manager virtinst qemu-kvm

