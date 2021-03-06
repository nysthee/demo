# Azure disk new features and restrictions
## Azure disk new features
 - Azure disk size grow

available from `v1.11.0`

details: [Add azuredisk PV size grow feature](https://github.com/kubernetes/kubernetes/pull/64386)

example: [azuredisk storage class with size grow configuration](https://github.com/andyzhangx/demo/blob/master/pv/storageclass-azuredisk-sizegrow.yaml)

 - Enable dynamic azure disk volume limits

available from `v1.12.0`

details: [Enable dynamic azure disk volume limits](https://github.com/kubernetes/kubernetes/pull/67772)

Before v1.12.0, the maximum disk number of node is always set as 16 by default, so if it’s a little VM size that only supports 8 disk attachment, there would be error after attaching the 9th disk, while from v1.12.0, the 9th disk would not be scheduled to that node if it only accepts attaching 8 disks totally.

 - More disk type support

new managed disk types [`StandardSSD_LRS`](https://aka.ms/StandardSSDBlog), `UltraSSD_LRS` are available from `v1.13.0`

all possible `skuname` values are `Standard_LRS`, `StandardSSD_LRS`, `Premium_LRS`, `UltraSSD_LRS`
> Note: `UltraSSD_LRS` is still in Preview, it's only avaiable in certain region and supports Availablity Zone only, while AKS does not support Availablity Zone yet, details about UltraSSD_LRS: [Ultra SSD (preview) Managed Disks for Azure Virtual Machine workloads](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-ultra-ssd)

details: [add azure UltraSSD, StandardSSD disk type support](https://github.com/kubernetes/kubernetes/pull/70477)

## Azure disk restrictions
### 1. cannot attach an azure disk from another subscription
Error would be like following:
```
Events:
  Type     Reason              Age                  From                               Message
  ----     ------              ----                 ----                               -------
  Warning  FailedMount         3m (x1912 over 3d)   kubelet, aks-nodepool1-20449952-1  Unable to mount volumes for pod "nginx-azuredisk3_default(116d527e-c84c-11e8-af0a-d2df8f315e11)": timeout expired waiting for volumes to attach or mount for pod "default"/"nginx-azuredisk3". list of unmounted volumes=[azure]. list of unattached volumes=[azure default-token-dw48n]
  Warning  FailedAttachVolume  28s (x2129 over 3d)  attachdetach-controller            AttachVolume.Attach failed for volume "azure" : Attach volume "andytest" to instance "/subscriptions/b9d2281e-dcd5-4dfd-9a97-xxx/resourceGroups/MC_andy-aks1112_andy-aks1112_westus2/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-20449952-1" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=400 -- Original Error: Code="InvalidParameter" Message="Subscription b9d2281e-dcd5-4dfd-9a97-xxx of the request must match the subscription 4be8920b-2978-43d7-ab14-xxx contained in the managed disk id." Target="dataDisk.managedDisk.id"
```
