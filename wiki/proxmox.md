# Proxmox

## Proxmox create cloud-init
```
qm create 9103 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9103 jammy-server-cloudimg-amd64.img rpool_ted
qm set 9103 --scsihw virtio-scsi-pci --scsi0 rpool_ted:vm-9103-disk-0
qm set 9103 --ide2 rpool_ted:cloudinit
qm set 9103 --boot c --bootdisk scsi0
qm template 9103

qm create 9202 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9202 jammy-server-cloudimg-amd64.img rpool_james
qm set 9202 --scsihw virtio-scsi-pci --scsi0 rpool_james:vm-9202-disk-0
qm set 9202 --ide2 rpool_james:cloudinit
qm set 9202 --boot c --bootdisk scsi0
qm template 9202

qm create 9203 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9203 jammy-server-cloudimg-amd64.img rpool_james
qm set 9203 --scsihw virtio-scsi-pci --scsi0 rpool_james:vm-9203-disk-0
qm set 9203 --ide2 rpool_james:cloudinit
qm set 9203 --boot c --bootdisk scsi0
qm template 9203

qm create 9302 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9302 jammy-server-cloudimg-amd64.img local-lvm
qm set 9302 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9302-disk-0
qm set 9302 --ide2 local-lvm:cloudinit
qm set 9302 --boot c --bootdisk scsi0
qm template 9302

qm create 9303 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9303 jammy-server-cloudimg-amd64.img local-lvm
qm set 9303 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9303-disk-0
qm set 9303 --ide2 local-lvm:cloudinit
qm set 9303 --boot c --bootdisk scsi0
qm template 9303

qm create 9304 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9304 jammy-server-cloudimg-amd64.img local-lvm
qm set 9304 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9304-disk-0
qm set 9304 --ide2 local-lvm:cloudinit
qm set 9304 --boot c --bootdisk scsi0
qm template 9304


wget https://download.opensuse.org/tumbleweed/appliances/openSUSE-MicroOS.x86_64-kvm-and-xen.qcow2
qm create 9105 --name microos --cores 2 --memory 4096 --net0 virtio,bridge=vmbr0
qm importdisk 9105 openSUSE-MicroOS.x86_64-kvm-and-xen.qcow2 rpool_ted
qm set 9105 --scsihw virtio-scsi-pci --scsi0 rpool_ted:vm-9105-disk-0
qm set 9105 --boot c --bootdisk virtio0
qm set 9105 --agent 1
qm set 9105 --vga qxl
qm set 9105 --machine q35
qm resize 9105 scsi0 +10000M
qm template 9105
```
