---
title: Skillnader och överväganden för hanterade diskar och hanteras avbildningar i Azure Stack | Microsoft Docs
description: Läs mer om skillnader och överväganden när du arbetar med hanterade diskar och hanterade avbildningar i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 87c3be01b5a77aa43a23fa64e5359e8ac4a20a36
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271245"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack hanterade diskar: skillnader och överväganden

Den här artikeln sammanfattas de kända skillnaderna mellan [Azure Stack hanterade diskar](azure-stack-manage-vm-disks.md) och [managed disks för Azure](../../virtual-machines/windows/managed-disks-overview.md). Läs mer om övergripande skillnader mellan Azure Stack och Azure, i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

Hanterade diskar förenklar Diskhantering för virtuella IaaS-datorer genom att hantera den [lagringskonton](../azure-stack-manage-storage-accounts.md) som är associerade med de Virtuella diskarna.

> [!Note]  
> Hanterade diskar på Azure Stack är tillgänglig från 1808 uppdateringen. Det är aktiverat som standard när du skapar virtuella datorer med Azure Stack-portalen, från och med uppdateringen 1811.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Lathund: managed disk skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|Kryptering för vilande Data |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |BitLocker 128-bitars AES-kryptering      |
|Bild          | Stöd för anpassade avbildningar |Stöds|
|Alternativ för säkerhetskopiering |Stöd för Azure Backup-tjänsten |Stöds inte än |
|Alternativen för katastrofåterställning |Stöd för Azure Site Recovery |Stöds inte än|
|Disktyper     |Premium SSD, Standard SSD (förhandsversion) och Standard-Hårddisk |Premium SSD, Standard HDD |
|Premium-diskar  |Stöds fullt ut |Kan tillhandahållas, men ingen prestandagräns eller garanterar  |
|Premium disks IOPs  |Beror på diskstorleken  |2300 IOPs per disk |
|Dataflöde för Premium-diskar |Beror på diskstorleken |145 MB/sekund per disk |
|Diskstorlek  |Azure Premium Disk: P4 (32 GiB) till P80 (32 TiB)<br>Azure Standard SSD-Disk: E10 (128 GiB) till E80 (32 TiB)<br>Azure Standard HDD Disk: S4 (32 GiB) till S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GB<br>M20: 512 GiB<br>M30: 1024 giB |
|Diskar kopiering av ögonblicksbild|Ta en ögonblicksbild Azure hanterade diskar är anslutna till en aktiv virtuell dator som stöds|Stöds inte än |
|Diskar prestanda analytiska |Aggregera mått och per disk-mått som stöds |Stöds inte än |
|Migrering      |Innehåller verktyg för att migrera från befintliga ohanterade virtuella Azure Resource Manager-datorer utan att behöva återskapa den virtuella datorn  |Stöds inte än |

> [!NOTE]  
> Hanterade diskar IOPs och dataflöde i Azure Stack är ett tak antal i stället för ett etablerade tal, som kan påverkas av maskin- och arbetsbelastningar som körs i Azure Stack.

## <a name="metrics"></a>Mått

Det finns också skillnader med mätvärden i storage:

- Med Azure Stack, görs transaktionsdata i lagringsmått ingen åtskillnad interna eller externa nätverksbandbredd.
- Azure Stack-transaktionsdata i mätvärden i storage inkluderar inte VM-åtkomst till de monterade diskarna.

## <a name="api-versions"></a>API-versioner

Azure Stack hanterade diskar stöder följande API-versioner:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Konvertera till managed disks

Du kan använda följande skript för att konvertera en för etablerade virtuell dator från ohanterade till hanterade diskar. Ersätt platshållarna med dina egna värden:

```powershell
$subscriptionId = 'subid'

# The name of your resource group where your VM to be converted exists
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM's configuration (network config, vm name, location).
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Delete old VM, but keep the OS disk
Remove-AzureRmVm -Name $virtualMachineName -ResourceGroupName $resourceGroupName

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Hanterade avbildningar

Azure Stack-stöder *hanteras avbildningar*, vilka aktivera du att skapa en hanterad avbildning-objekt på en generaliserad virtuell dator (både ohanterade och hanterade) som kan bara skapa hanterade disk virtuella datorer framöver. Hanterade avbildningar kan följande två scenarier:

- Du har generaliserats ohanterade virtuella datorer och vill använda hanterade diskar framöver.
- Du har en generaliserad hanterad virtuell dator och vill skapa flera, liknande hanterade virtuella datorer.

### <a name="step-1-generalize-the-vm"></a>Steg 1: Generalisera den virtuella datorn

För Windows, följer du de [Generalisera Windows-VM med hjälp av Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) avsnittet. Följ steg 1 för Linux, [här](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Se till att generalisera den virtuella datorn. Skapa en virtuell dator från en avbildning som inte har generaliserats korrekt leder till en **VMProvisioningTimeout** fel.

### <a name="step-2-create-the-managed-image"></a>Steg 2: Skapa hanterad avbildning

Du kan använda portal, PowerShell eller CLI för att skapa hanterade avbildningen. Följ stegen i Azure artikeln [här](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Steg 3: Välj användningsfallet

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Fall 1: Migrera ohanterade virtuella datorer till managed disks

Se till att generalisera den virtuella datorn korrekt innan du utför det här steget. Efter generalisering, kan du inte längre använda den här virtuella datorn. Skapa en virtuell dator från en avbildning som inte har generaliserats korrekt leder till en **VMProvisioningTimeout** fel.

Följ instruktionerna [här](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) att skapa en hanterad avbildning från en generaliserad virtuell Hårddisk i ett lagringskonto. Du kan använda den här avbildningen framöver för att skapa hanterade virtuella datorer.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Fall 2: Skapa den hanterade virtuella datorn från hanterad avbildning med hjälp av Powershell

När du har skapat en avbildning från en befintlig hanterad disk i virtuell dator med hjälp av skriptet [här](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell), följande exempelskript skapar en liknande Linux VM från ett befintligt bildobjekt:

Azure Stack PowerShell-modulen 1.7.0 eller senare: Följ anvisningarna [här](../../virtual-machines/windows/create-vm-generalized-managed.md).

Azure Stack PowerShell-modulen 1.6.0 eller senare eller tidigare:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Du kan också använda portalen för att skapa en virtuell dator från en hanterad avbildning. Mer information finns i Azure hanteras bild artiklar [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../../virtual-machines/windows/capture-image-resource.md) och [skapa en virtuell dator från en hanterad avbildning](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Konfiguration

Efter att ha tillämpat 1808 uppdatera eller senare, måste du utföra följande konfigurationssteg innan du använder hanterade diskar:

- Om en prenumeration skapades före uppdateringen 1808, följer du stegen nedan för att uppdatera prenumerationen. Annars kan misslyckas distribuera virtuella datorer i den här prenumerationen med felmeddelandet ”internt fel i Diskhanteraren”.
   1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Klicka på **Resursprovidrar**, klicka sedan på **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
   2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
- Om du använder en miljö med flera organisationer, fråga din molnoperator (som kan vara i din organisation eller från service provider) att konfigurera om var och en av dina gäst-kataloger följa stegen i [i den här artikeln](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Annars kan misslyckas distribuera virtuella datorer i en prenumeration som är associerade med den gästkatalogen med felmeddelandet ”internt fel i Diskhanteraren”.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om Azure Stack-datorer](azure-stack-compute-overview.md)
