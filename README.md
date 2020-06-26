# packer-proxmox-template

Packer configuration for creating Debian 10 virtual machine templates for Proxmox VE.

Requirements:

- [Packer](https://www.packer.io/downloads)
- [Debian installation image](https://www.debian.org/distrib/) uploaded to Proxmox VE

VM installation is usually done using an ISO image file and depending on the OS, this can be a time consuming task one might want to avoid.

## Automating Debian installation using preseeding

Preseeding provides a way to answer questions asked during the installation process, without having to manually enter the answers while the installation is running.

This is the [preseed.cfg](preseed.cfg) that will be used to create the virtual machine template. It makes some assumptions such as what the root password should be (hint: it's `toor`) and a few other things, so be sure to check it out and modify to fit your needs.

## Creating a new VM Template

Templates provide an easy way to deploy many VMs of the same type.

Templates are created by converting an existing VM to a template. As soon as the VM is converted, it cannot be started anymore. If you want to modify an existing template, you need to create a new template.

Creating a VM template is easy:

```sh
$ packer build debian-10-buster.json
proxmox: output will be in this color.

==> proxmox: Creating VM
==> proxmox: No VM ID given, getting next free from Proxmox
==> proxmox: Starting VM

...

==> proxmox: Stopping VM
==> proxmox: Converting VM to template
Build 'proxmox' finished.

==> Builds finished. The artifacts of successful builds are:
--> proxmox: A template was created: 102
```

## Deploy a VM from a Template

Right-click the template in Proxmox VE, and select "Clone".

- **full clone** is a complete copy and is fully independent from the original VM or VM Template, but it requires the same disk space as the original. Useful while you don't have a stable base image
- **linked clone** requires less disk space but cannot run without access to the base VM Template. Not supported with LVM & ISCSI storage types

## License

MIT
