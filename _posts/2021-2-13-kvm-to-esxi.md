* Migrating a virtual machine from KVM to ESXi

Migrating a virtual machine from one host to another is usually no big deal if both hosts run the same VMM. But what if one wants to move a VM from a host running a different hypervisor than the target host? In the case of moving a VM from KVM to ESXi that’s just not (easily) possible. However, one can convert the virtual hard drive and recreate the virtual machine on the target host, which should be good enough for most use cases

* Exporting the virtual hard drive

To export the virtual hard drive on the KVM host, there’s a handy little command line tool called qemu-img, which is usually part of the distribution running KVM. On Fedora, RHEL and CentOS it can be installed with:

yum install qemu-img

Let’s convert a image file from raw to vmdk:

qemu-img convert -f raw /var/lib/libvirt/images/VM.img -O vmdk VM.tmp.vmdk -o compat6

For a list of supported formats have a look at the qemu-img manpage. We can now copy the resulting vmdk file VM.tmp.vmdk to the target ESXi host, e.g. using scp.

* Importing the vmdk file

Even though we’ve just created a vmdk image file, it cannot be used as a virtual hard drive for a VM on ESXi. If you try to create a virtual machine with this particular vmdk file as hard drive, you’ll most likely get a error message in /var/log/hostd.log similar to this:

/var/log/hostd.log

[...]
Power On message: Failed to open disk scsi0:0: Unsupported or invalid disk type 7.  Ensure that the disk has been imported.
[...]

Fortunately, on ESXi there’s another handy command line tool for handling vmdk image files, called vmkfstools. However before we import the vmdk image file it is advisable to create a directory for the new virtual machine on the datastore first. This allows you to put the imported image into the correct directory (i.e. the directory where ESXi keeps all the files concerting one particular VM) straight away.

~ # mkdir /vmfs/volumes/datastore0/New-VM
~ # vmkfstools -i /vmfs/volumes/datastore0/VM.tmp.vmdk  -d thin /vmfs/volumes/datastore0/New-VM/New-VM.vmdk
Destination disk format: VMFS thin-provisioned
Cloning disk '/vmfs/volumes/datastore0/VM.tmp.vmdk'...
Clone: 100% done.

Of course, there are other disk formats available, too. You can throw away the original VM.tmp.vmdk image file after the import was completed successfully

rm /vmfs/volumes/datastore0/VM.tmp.vmdk

You can now go ahead an create a new virtual machine on the target host using the newly imported vmdk file /vmfs/volumes/datastore0/New-VM/New-VM.vmdk as a virtual hard drive. But depending on your VM, you might want to change the type of the hard drive from ide to scsi first:

~ # sed -i 's/ddb.adapterType = "ide"/ddb.adapterType = "lsilogic"/' /vmfs/volumes/datastore0/New-VM/New-VM.vmdk 

* Making adjustments to the virtual machine

Booting the newly created virtual machine might not work straight away, e.g. on Fedora, RHEL and CentOS the UUID of the hard drive is included in the initial RAM file system. The easiest way to fix this is to boot into rescue mode (there’s usually a rescue mode entry in the grub boot menu) and run

dracut --regenerate-all --force

to recreate the initramfs. You might also have to edit your network configuration since your network device usually get’s a different name.

[credit](https://possiblelossofprecision.net/?p=2293)
