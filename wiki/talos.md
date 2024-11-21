# Talos
Talos on proxmox guide: [Talos Guide](https://www.talos.dev/v1.8/talos-guides/install/virtualized-platforms/proxmox/)<br>
Generate image: [Image factory](https://factory.talos.dev/)

Create controlplane.patch:

    machine:
        # Provides machine specific network configuration options.
        network: 
          interfaces:
            # - interface: enxbc2411d46b11 # The interface name.
            - deviceSelector:
                physical: true # should select any hardware network device, if you have just one, it will be selected
        # Used to provide additional options to the kubelet.
        kubelet:        
        # Provides machine specific network configuration options.
            extraArgs:
              rotate-server-certificates: true
        
        files:
          - content: |
                [metrics]
                address = "0.0.0.0:11234"        
            path: /var/cri/conf.d/metrics.toml
            op: create
    
        # Used to provide instructions for installations.
        install:
            image: factory.talos.dev/installer/<IMAGE_ID>:<TALOS_VERSION> # Allows for supplying the image used to perform the installation.
    
        # Example configuration for cloudflare ntp server.
        time:
            disabled: false # Indicates if the time service is disabled for the machine.
            servers:
              - time.cloudflare.com
    
        # # Used to configure the machine's sysctls.
    
        # MachineSysctls usage example.
        sysctls:
            kernel.domainname: <DOMAIN_NAME>
        
        certSANs:
          - <VIP_ADRESS>
          - <NODE1 IP>
          - <NODE2 IP>
          - <NODE3 IP>
    
    # Provides cluster specific configuration options.
    cluster:
      proxy:
        disabled: true
      network:
        cni:
          name: none # On installera Cilium manuellement
      # A list of urls that point to additional manifests.
      extraManifests:
        - https://raw.githubusercontent.com/alex1989hu/kubelet-serving-cert-approver/main/deploy/standalone-install.yaml
        - https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
    
      # # Allows running workload on control-plane nodes.
      allowSchedulingOnControlPlanes: true

Create one patch file per node node<NR>.patch:

    # kube-server-1.patch
    machine:
    network:
      hostname: <HOST_NAME>
      interfaces:
        - deviceSelector:
            physical: true # should select any hardware network device, if you have just one, it will be selected
          addresses:
           - <NODE_IP>/24
          dhcp: false
          vip:
            ip: <VIP_IP_ADRESS> # Specifies the IP address to be used.
          routes:
           - network: 0.0.0.0/0
             gateway: <GATEWAY_IP>
      
      nameservers:
        - <NAME_SERVER_IP>
      
      extraHostEntries:
        - ip: <NODE1_IP>
          aliases:
            - <NODE1_HOST_NAME>
        - ip:<NODE1_IP>
          aliases:
            - <NODE2_HOST_NAME>
        - ip: <NODE1_IP>
          aliases:
            - <NODE3_HOST_NAME>
    
    cluster:
    apiServer:
      certSANs:
        - <VIP_IP_ADRESS>
        - <NODE1_HOST_NAME>
        - <NODE2_HOST_NAME>
        - <NODE3_HOST_NAME>
        - <NODE1_FULL_HOST_NAME_WITH_DOMAIN>
        - <NODE2_FULL_HOST_NAME_WITH_DOMAIN>
        - <NODE3_FULL_HOST_NAME_WITH_DOMAIN>

Generate secrets file:<br>

`talosctl gen secrets`

Generate config:<br>

`talosctl gen config <$CLUSTER_NAME> https://$CONTROL_PLANE_IP:6443 --with-secrets secrets.yaml --config-patch=@controlplane.patch --force`

Generate machine configs:<br>
`
talosctl machineconfig patch controlplane.yaml --patch @kube-server-1.patch --output kube-server-1.yaml
talosctl machineconfig patch controlplane.yaml --patch @kube-server-2.patch --output kube-server-2.yaml
talosctl machineconfig patch controlplane.yaml --patch @kube-server-3.patch --output kube-server-3.yaml
`

Apply config:<br>
`
talosctl apply-config --nodes <NODE1_IP> --file kube-server-1.yaml --insecure
talosctl apply-config --nodes <NODE2_IP> --file kube-server-2.yaml --insecure
talosctl apply-config --nodes <NODE2_IP> --file kube-server-3.yaml --insecure
`

Bootstrap cluster:<br>
`talosctl bootstrap --talosconfig talosconfig --nodes <NODE1_IP> --endpoints <NODE1_IP>`

Install Cilium (https://www.talos.dev/v1.8/kubernetes-guides/network/deploying-cilium/#machine-config-preparation)<br>

    helm install \
    cilium \
    cilium/cilium \
    --version 1.15.6 \
    --namespace kube-system \
    --set ipam.mode=kubernetes \
    --set kubeProxyReplacement=true \
    --set securityContext.capabilities.ciliumAgent="{CHOWN,KILL,NET_ADMIN,NET_RAW,IPC_LOCK,SYS_ADMIN,SYS_RESOURCE,DAC_OVERRIDE,FOWNER,SETGID,SETUID}" \
    --set securityContext.capabilities.cleanCiliumState="{NET_ADMIN,SYS_ADMIN,SYS_RESOURCE}" \
    --set cgroup.autoMount.enabled=false \
    --set cgroup.hostRoot=/sys/fs/cgroup \
    --set k8sServiceHost=localhost \
    --set k8sServicePort=7445

Check status in the dashboard:<br>
`talosctl --talosconfig talosconfig --nodes <NODE_IP> --endpoints <NODE_IP> dashboard`

Download kubectl config:<br>
`talosctl --talosconfig talosconfig --nodes <NODE_IP> --endpoints <NODE_IP> kubeconfig`



