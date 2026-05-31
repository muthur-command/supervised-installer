> [!IMPORTANT]
> This installation method is for advanced users only!
>
> The Supervised Debian installation is an opinionated appliance. That means it has
> many dependencies and bundles default configuration files for system services!
>
> For typical users, please use the official Muthur Command OS image (OVA / Raspberry Pi).

# Install Muthur Command Supervised

This installation method provides the full Muthur Command experience on a regular operating system. This means, all components from the Muthur Command method are used, except for the Muthur Command Operating System. This system will run the Muthur Command Supervisor. The Supervisor is not just an application, it is a full appliance that manages the whole system. It will clean up, repair or reset settings to default if they no longer match expected values.

By not using the Muthur Command Operating System, the user is responsible for making sure that all required components are installed and maintained. Required components and their versions will change over time. Muthur Command Supervised is provided as-is as a foundation for community supported do-it-yourself solutions. We only accept bug reports for issues that have been reproduced on a freshly installed, fully updated Debian with no additional packages.

This method is considered advanced and should only be used if one is an expert in managing a Linux operating system, Docker and networking.



## Installation

Run the following commands as root (`su -` or `sudo su -` on machines with sudo installed):

Step 1: Transition from the default Debian networking service `ifupdown` to NetworkManager and systemd-resolved, run the following commands:

```bash
apt install \
network-manager \
systemd-resolved
```

At this point you won't have Internet access because systemd-resolved doesn't know about your DNS setup. To restore Internet, we need to fully transition the network setup to NetworkManager. There was a hint in the output of the previous command:
```
The following network interfaces were found in /etc/network/interfaces
which means they are currently configured by ifupdown:
- enp1s0
If you want to manage those interfaces with NetworkManager instead
remove their configuration from /etc/network/interfaces.
```

The network interface name might be different depending on your setup. For a default network setup using DHCP the following commands work:

```bash
systemctl restart systemd-resolved.service && \
systemctl disable --now networking.service && \
mv /etc/network/interfaces /etc/network/interfaces.disabled && \
systemctl restart NetworkManager
```

> [!NOTE]
> Your system might have a new IP address at this point, probably because the DHCP server id used by NetworkManager appears to be different.

Step 2: Install Docker-CE, OS Agent and Supervised dependencies which aren't part of the package dependencies with this command:

```bash
apt install \
curl \
udisks2
```

Step 3: Install Docker-CE with the following command:

```bash
curl -fsSL get.docker.com | sh
```

Step 4: Install the OS-Agent:

Instructions for installing the OS-Agent can be found [here](https://github.com/muthur-command/os-agent/tree/mc#install--upgrade)

Step 5: Install the Muthur Command Supervised Debian Package:

```bash
curl -L -o muthur-command-supervised.deb https://github.com/muthur-command/supervised-installer/releases/latest/download/muthur-command-supervised.deb
apt install ./muthur-command-supervised.deb
```

## Supported Machine types

- generic-x86-64
- odroid-c2
- odroid-c4
- odroid-n2
- qemuarm-64 (use this for any aarch64 machine)
- qemux86-64
- raspberrypi3-64
- raspberrypi4-64
- raspberrypi5-64
- khadas-vim3

## Configuration

The default path for our `$DATA_SHARE` is `/var/lib/muthurcommand`.
This path is used to store all Muthur Command related things.

You can reconfigure this path during installation with

```bash
DATA_SHARE=/my/own/muthurcommand dpkg --force-confdef --force-confold -i muthur-command-supervised.deb
```

## Troubleshooting

If something's going wrong, use `journalctl -f` to get your system logs. If you are not familiar with Linux and how you can fix issues, we recommend to use Muthur Command OS.

## Source

- **Upstream:** [home-assistant/supervised-installer](https://github.com/home-assistant/supervised-installer) — upstream source repository (ported to Muthur Command OS).
- **This repository:** **Muthur Command** maintains this fork for **Muthur Command OS**; behavior may diverge from upstream over time.
- **License:** Apache-2.0; see [`LICENSE`](./LICENSE).
