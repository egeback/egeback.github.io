# Proxmox

## Proxmox create cloud-init
```
qm create 9101 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9101 jammy-server-cloudimg-amd64.img rpool_ted
qm set 9101 --scsihw virtio-scsi-pci --scsi0 rpool_ted:vm-9101-disk-0
qm set 9101 --ide2 rpool_ted:cloudinit
qm set 9101 --boot c --bootdisk scsi0
qm template 9101

qm create 9201 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9201 jammy-server-cloudimg-amd64.img rpool_james
qm set 9201 --scsihw virtio-scsi-pci --scsi0 rpool_james:vm-9201-disk-0
qm set 9201 --ide2 rpool_james:cloudinit
qm set 9201 --boot c --bootdisk scsi0
qm template 9201

qm create 9301 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9301 jammy-server-cloudimg-amd64.img rpool_james
qm set 9301 --scsihw virtio-scsi-pci --scsi0 rpool_james:vm-9201-disk-0
qm set 9301 --ide2 rpool_barney:cloudinit
qm set 9301 --boot c --bootdisk scsi0
qm template 9201
```
