---
title: Skillnader och överväganden för Managed Disks i Azure Stack | Microsoft Docs
description: Läs mer om skillnader och överväganden när du arbetar med Managed Disks i Azure Stack.
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
ms.date: 01/31/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 60f633049d6fdbb59744c8003e742ff649d48ea7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243401"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: skillnader och överväganden

Den här artikeln sammanfattas de kända skillnaderna mellan [Azure Stack Managed Disks](azure-stack-manage-vm-disks.md) och [Managed Disks för Azure](../../virtual-machines/windows/managed-disks-overview.md). Läs mer om övergripande skillnader mellan Azure Stack och Azure, i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

Hanterade diskar förenklar Diskhantering för virtuella IaaS-datorer genom att hantera den [lagringskonton](../azure-stack-manage-storage-accounts.md) som är associerade med de Virtuella diskarna.

> [!Note]  
> Hanterade diskar på Azure Stack är tillgänglig från 1808 update. Det är aktiverat som standard när du skapar virtuella datorer med Azure Stack-portalen från 1811 update.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Lathund: Managed disk skillnader

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
> Hanterade diskar IOPs och dataflöde i Azure Stack är en cap-nummer i stället för ett etablerade tal, som kan påverkas av maskin- och arbetsbelastningar som körs i Azure Stack.

## <a name="metrics"></a>Mått

Det finns också skillnader med mätvärden i storage:

- Med Azure Stack, görs transaktionsdata i lagringsmått ingen åtskillnad interna eller externa nätverksbandbredd.
- Azure Stack-transaktionsdata i mätvärden i storage inkluderar inte VM-åtkomst till de monterade diskarna.

## <a name="api-versions"></a>API-versioner

Managed Disks för Azure Stack stöd för följande API-versioner:

- 2017-03-30
- 2017-12-01

## <a name="managed-images"></a>Hanterade avbildningar

Azure Stack-stöder *hanteras avbildningar*, vilka aktivera du att skapa en hanterad avbildning-objekt på en generaliserad virtuell dator (både ohanterade och hanterade) som kan bara skapa hanterade disk virtuella datorer framöver. Hanterade avbildningar kan följande två scenarier:

- Du har generaliserats ohanterade virtuella datorer och vill använda hanterade diskar framöver.
- Du har en generaliserad hanterad virtuell dator och vill skapa flera, liknande hanterade virtuella datorer.

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>Migrera ohanterade virtuella datorer till managed disks

Följ instruktionerna [här](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) att skapa en hanterad avbildning från en generaliserad virtuell Hårddisk i ett lagringskonto. Den här avbildningen kan användas för att skapa hanterade virtuella datorer framöver.

### <a name="create-managed-image-from-vm"></a>Skapa en hanterad avbildning från virtuell dator

När du har skapat en avbildning från en befintlig hanterad disk i virtuell dator med hjälp av skriptet [här](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , följande exempelskript skapar en liknande Linux VM från ett befintligt bildobjekt:

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

Mer information finns i Azure hanteras bild artiklar [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../../virtual-machines/windows/capture-image-resource.md) och [skapa en virtuell dator från en hanterad avbildning](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Konfiguration

Efter att ha tillämpat 1808 uppdatera eller senare, måste du utföra följande konfigurationssteg innan du använder Managed Disks:

- Om en prenumeration skapades före uppdateringen 1808, följer du stegen nedan för att uppdatera prenumerationen. Annars kan misslyckas distribuera virtuella datorer i den här prenumerationen med felmeddelandet ”internt fel i Diskhanteraren”.
   1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Klicka på **Resursprovidrar**, klicka sedan på **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
   2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
- Om du använder en miljö med flera organisationer, fråga din molnoperator (som kan vara i din organisation eller från service provider) att konfigurera om var och en av dina gäst-kataloger följa stegen i [i den här artikeln](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Annars kan misslyckas distribuera virtuella datorer i en prenumeration som är associerade med den gästkatalogen med felmeddelandet ”internt fel i Diskhanteraren”.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om Azure Stack-datorer](azure-stack-compute-overview.md)
