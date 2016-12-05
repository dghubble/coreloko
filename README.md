
# coreloko

CoreOS clusters, locally with QEMU, KVM, and libvirt.

Several people use the [coreos-baremetal](https://github.com/coreos/coreos-baremetal) QEMU/KVM example clusters locally. That's great, but can be slow since the clusters PXE boots and provisions to mirror bare-metal in production.

`coreloko` let's you create a CoreOS cluster (etcd, Kubernetes, rktnetes, Torus) on your laptop in QEMU/KVM much faster, but using the same profiles, groups, and Ignition configs you'd use in production.

## Usage

Download and verify CoreOS images.

    sudo ./coreloko init

Generate user-data for nodes. The coreos-baremetal [example clusters](https://github.com/coreos/coreos-baremetal/tree/master/examples) will be the choices in future. Until then, please continue to use coreos-baremetal for these.

    ./coreloko userdata etcd
    ./coreloko userdata etcd3
    ./coreloko userdata k8s
    ./coreloko userdata torus

For now, a small cloud-config adds your SSH public keys to nodes.

    ./coreloko userdata

Create QEMU/KVM cluster nodes.

    sudo ./coreloko create

## Access

By default, cloud-config adds your SSH public key to nodes. SSH to the address or use the serial console.

## Ignition

Here is a mechanism allowing Ignition to be used instead of cloud-config (discouraged). I'll try to improve this pain point later.

    sudo ./coreloko shutdown
    sudo virsh edit node1

Insert a [pass-through](https://libvirt.org/drvqemu.html#qemucommand) QEMU argument `fw_cfg` to load an Ignition file as a QEMU [firmware](https://github.com/qemu/qemu/blob/master/docs/specs/fw_cfg.txt) config device.

    <qemu:commandline>
        <qemu:arg value='-newarg'/>
        <qemu:env name='QEMU_ENV' value='VAL'/>
    </qemu:commandline>

    <domain type='kvm' xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'>
      <name>some-name</name>
      ...
      </devices>
      <qemu:commandline>
        <qemu:arg value='-fw_cfg'/>
        <qemu:arg value='name=opt/com.coreos/config,file=/absolute/path/coreloko/hello.ign'/>
      </qemu:commandline>
    </domain>

Start the cluster nodes again with the pass-through.

    sudo ./coreloko start

Currently, there seems to be an [issue](https://github.com/coreos/bugs/issues/1693) getting Ignition to load this.

## Cleanup

Destroy all nodes in the cluster.

    sudo ./coreloko destroy

## Plain QEMU

You can use qemu commands directly via `coreos_production_qemu.sh` which has a `-i` Ignition flag. `libvirt` will probably be preferred in this repo.
