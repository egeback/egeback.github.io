# MicroOS
## Config files for MicroOS:
butane script:
```
variant: fcos
version: 1.4.0
kernel_arguments:
  should_exist:
    - ip={IP}::{GATEWAY}:255.255.255.0:{HOSTNAME}:enp6s18:on:10.0.1.1
storage:
  filesystems:
    - path: /home
      device: "/dev/sda3"
      format: btrfs
      wipe_filesystem: false
      mount_options:
       - "subvol=/@/home"
  files:
    - path: /etc/hostname
      mode: 0644
      overwrite: true
      contents:
        inline: "{FULL_HOST_NAME}"
    - path: /etc/NetworkManager/system-connections/enp6s18.nmconnection
      mode: 0600
      contents:
        inline: |
          [connection]
          id=enp6s18
          type=ethernet
          interface-name=enp6s18
          [ipv4]
          address1={IP}/24,{GATEWAY}
          dhcp-hostname={HOSTNAME}
          dns={DNS_SERVER};
          dns-search={DOMAIN}
          may-fail=false
          method=manual
passwd:
  users:
    - name: root
      ssh_authorized_keys:
        - ssh-rsa {KEY1}
        - ssh-rsa {KEY2}
      password_hash: "{HASH}"
    - name: marky
      ssh_authorized_keys:
        - ssh-rsa {KEY1}
        - ssh-rsa {KEY2}
      password_hash: "{HASH}"

```
# Generate from butane
```
  butane -o config.ign -p -s config.bu
```
# Script file:
```

#!/bin/sh
## Enable network
# combustion: network


## Post output on stdout
#exec > >(exec tee -a /dev/tty0) 2>&1
exec > >(exec tee -a /root/combustion.log) 2>&1

zypper ref
zypper up -y --no-confirm
zypper --non-interactive install vim-small jq helm nfs-client git patterns-microos-cockpit cockpit bash-completion python3 python3-pip htop k3s-selinux

## helm config
helm completion bash > /etc/bash_completion.d/helm
helm plugin install https://github.com/databus23/helm-diff

## Enable services
systemctl enable --now cockpit.socket

# Leave a marker
echo "Configured with combustion" > /etc/issue.d/combustion
```
