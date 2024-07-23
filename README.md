# Foobar

Foobar is a Python library for dealing with word pluralization.

## Installation

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install foobar.

```bash
pip install foobar
```

## Prereq

A pc :)

## Installing

First you want to prepare a image for proxmox, you can use balena etcher for that.
- Proxmox iso: https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso
- balenaEtcher: https://etcher.balena.io/

For proxmox setup there's nothing in particular you should for the most part is basicly a OS installation like any other.

Once you have installed Proxmox you can start provisioning your VMs. Since we're most intrested here in installing portainer for a streaming server I'd recommend choosing to install your new VM into a HD you don't want to change very soon, with proxmox changing HDs can be very easy but with this particular setup my portainer started to misbehave so I wouldn't recommend you to do that.

Time for our VM, I'd suggest you install ubuntu server: https://ubuntu.com/download/server. Basicly you have to upload this ISO to your proxmox then you can use it to create the VM.

The only thing while installing the VM:
- Dont select LVM
- Remember to install ssh when asked

```python
import foobar

# returns 'words'
foobar.pluralize('word')

# returns 'geese'
foobar.pluralize('goose')

# returns 'phenomenon'
foobar.singularize('phenomena')
```

## Contributing

Pull requests are welcome. For major changes, please open an issue first
to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

[MIT](https://choosealicense.com/licenses/mit/)
