# HA Cluster on Azure

## How to Create Shared Disk Cluster
### Evaluation Environment
- Region
  - West US
- VM Size
  - Standard D2s v3 (2 vcpus, 8 GiB memory)
- Shared Disk
  - Ultra Disk
```
+--------------------+      +--------------------+
| - Windows Server   |      | - Windows Server   |
| - EXPRESSCLUSTER X |      | - EXPRESSCLUSTER X |
|        +-----+     |      |     +-----+        |
|        | HBA |     |      |     | HBA |        |
|        +--+--+     |      |     +--+--+        |
+-----------|--------+      +--------|-----------+
            |  +------------------+  |
            |  | Shared Disk      |  |
            +--+ +----+---------+ +--+
               | | R: | S:      | |
               | +----+---------+ |
               +------------------+
R: Volume for network partiotion resolution resource of disk method (called Diks NP Resource)
S: Volume for Disk Resource
```

### Prerequisite
- Enable Cloud Shell in advance.
- Create a Resourge Group in West US region.
  - I have used **westus** for the Resource Group.

### How to Create VMs and Cluster
1. Launch Cloud Shell.
1. Create shared disk.
   ```ps
   PS> $datadiskconfig = New-AzDiskConfig -Location 'westus' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 2
   PS> New-AzDisk -ResourceGroupName 'westus' -DiskName 'mySharedDisk' -Disk $datadiskconfig
   ```
1. Create VMs on the same Resource Group.
   - VM Size: Standard D2s v3
   - Check **Yes** for **Enable Ultra Disk compatibility**.
   - Add the disk that you have created to **Data disks**.
1. Create a cluster following **Installation and Configuration Guide**.
   - https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/504/W42_IG_EN/index.html

### See Also
- Enable shared disk
  - https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-shared-enable#disk-sizes
- Using Azure ultra disks
  - https://docs.microsoft.com/ja-jp/azure/virtual-machines/windows/disks-enable-ultra-ssd