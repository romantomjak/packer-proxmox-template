# packer-proxmox-template

Packer configuration for creating Debian 11 virtual machine templates for Proxmox VE.

## Requirements

- [Packer](https://www.packer.io/downloads) 1.7.1+
- [Proxmox VE](https://www.proxmox.com/en/proxmox-ve) 6.2+

## Minimum background information

Launching a virtual machine requires an operating system to be installed. VM installation is usually done using an ISO image file and depending on the OS, this can be a time consuming task one might want to avoid. Luckily, this can be automated using a process known as _preseeding_.

> Preseeding provides a way to answer questions asked during the installation process, without having to manually enter the answers while the installation is running. You can check out the configuration for a standard Debian 10 installation in [preseed.cfg](preseed.cfg) and read more about this method in the [preseed documentation](https://wiki.debian.org/DebianInstaller/Preseed).

Proxmox Templates provide an easy way to deploy many VMs of the same type, but naturally we don't want them to be _completely_ identical. They may need a different hostname, an IP address, etc. This is what _cloud-init_ takes care of.

> Cloud-init is used for initial machine configuration like creating users or preseeding `authorized_keys` file for SSH authentication. You can check out the configuration in [cloud.cfg](cloud.cfg) and read more about this in [cloud-init documentation](https://cloudinit.readthedocs.io/en/latest/).

## Creating a new VM Template

Templates are created by converting an existing VM to a template. As soon as the VM is converted, it cannot be started anymore. If you want to modify an existing template, you need to create a new template.

Here's how to do all that in one step:

```sh
$ packer build debian-11-bullseye.pkr.hcl
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

Variables from the `debian-11-bullseye.pkr.hcl` can be overidden like so:

```
$ packer build -var "proxmox_host=10.10.0.10:8006" debian-11-bullseye.pkr.hcl
```

or you can just as easily specify a file that contains the variables:

```sh
$ packer build -var-file example-variables.pkrvars.hcl debian-11-bullseye.pkr.hcl
```

## Deploy a VM from a Template

Right-click the template in Proxmox VE, and select "Clone".

- **full clone** is a complete copy and is fully independent from the original VM or VM Template, but it requires the same disk space as the original
- **linked clone** requires less disk space but cannot run without access to the base VM Template. Not supported with LVM & ISCSI storage types

## Contributing

You can contribute in many ways and not just by changing the code! If you have
any ideas, just open an issue and tell me what you think.

Contributing code-wise - please fork the repository and submit a pull request.

## License

MIT
