# packer-proxmox-template

Packer configuration for creating Debian 10 virtual machine templates for Proxmox VE.

Requirements:

- [Packer](https://www.packer.io/downloads)
- [Debian installation image](https://www.debian.org/distrib/) uploaded to Proxmox VE

Launching a virtual machine requires an operating system to be installed. VM installation is usually done using an ISO image file and depending on the OS, this can be a time consuming task one might want to avoid.

Templates provide an easy way to deploy many VMs of the same type, but naturally we don't want them to be _completely_ identical. They may need a different hostname, an IP address, etc.

## Automating Debian installation using preseeding

Preseeding provides a way to answer questions asked during the installation process, without having to manually enter the answers while the installation is running. You can check out the configuration for standard Debian 10 installation in [preseed.cfg](preseed.cfg).

## Post-installation configuration

Cloud-init is useful for initial machine configuration like creating users or preseeding `authorized_keys` file for SSH authentication. You can check out the configuration in [cloud.cfg](cloud.cfg).

> The `cloud_init_public_ssh_key` variable must contain the public key you will use to authenticate with all machines built from this template. It will be used to preseed the `~/.ssh/authorized_keys` file. It's a bit of a hack, but I want to avoid commiting public keys into a git repository

## Creating a new VM Template

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

Values from the `variables` section in `debian-10-buster.json` can be overidden like so:

```
$ packer build -var "cores=4" -var "memory=4096" -var "disk=20G" debian-10-buster.json
```

or you can just as easily specify a file that contains the variables:

```sh
$ packer build -var-file example-variables.json debian-10-buster.json
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
