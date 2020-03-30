---
title: Undanta Azure VM-diskar från replikering med Azure Site Recovery och Azure PowerShell
description: Lär dig hur du utesluter diskar av virtuella Azure-datorer under Azure Site Recovery med hjälp av Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973680"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Exkludera diskar från PowerShell-replikering av virtuella Azure-datorer

I den här artikeln beskrivs hur du utesluter diskar när du replikerar virtuella Azure-datorer. Du kan utesluta diskar för att optimera den förbrukade replikeringsbandbredden eller de målresurser som diskarna använder. För närvarande är den här funktionen endast tillgänglig via Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar:

- Se till att du förstår [arkitekturen och komponenterna för haveriberedskap](azure-to-azure-architecture.md).
- Granska [kraven för stöd](azure-to-azure-support-matrix.md) för alla komponenter.
- Kontrollera att du har AzureRm PowerShell "Az"-modulen. Information om hur du installerar eller uppdaterar PowerShell finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Kontrollera att du har skapat ett valv för återställningstjänster och skyddade virtuella datorer minst en gång. Om du inte har gjort dessa saker följer du processen vid [Konfigurera haveriberedskap för virtuella Azure-datorer med Azure PowerShell](azure-to-azure-powershell.md).
- Om du letar efter information om hur du lägger till diskar i en Azure VM-aktiverad för replikering [läser du den här artikeln](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Varför utesluta diskar från replikering
Du kan behöva utesluta diskar från replikering eftersom:

- Din virtuella dator har nått [Azure Site Recovery-gränser för att replikera dataändringshastigheter](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- De data som spottas på den uteslutna disken är inte viktiga eller behöver inte replikeras.

- Du vill spara lagrings- och nätverksresurser genom att inte replikera data.

## <a name="how-to-exclude-disks-from-replication"></a>Så här utesluter du diskar från replikering

I vårt exempel replikerar vi en virtuell dator som har ett operativsystem och tre datadiskar som finns i regionen östra USA till regionen Västra USA 2. Namnet på den virtuella datorn är *AzureDemoVM*. Vi utesluter disk 1 och håller diskar 2 och 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Få information om de virtuella datorerna som ska replikeras

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Få information om den virtuella datorns diskar. Den här informationen används senare när du startar replikering av den virtuella datorn.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikera en virtuell Azure-dator

I följande exempel förutsätter vi att du redan har ett cachelagringskonto, replikeringsprincip och mappningar. Om du inte har dessa saker följer du processen vid [Konfigurera haveriberedskap för virtuella Azure-datorer med Azure PowerShell](azure-to-azure-powershell.md).

Replikera en virtuell Azure-dator med *hanterade diskar*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

När start-replikeringsåtgärden lyckas replikeras VM-data till återställningsregionen.

Du kan gå till Azure-portalen och se de replikerade virtuella datorerna under "replikerade objekt".

Replikeringsprocessen startar genom att dirigera en kopia av replikerande diskar för den virtuella datorn i återställningsregionen. Den här fasen kallas den inledande replikeringsfasen.

När den första replikeringen är klar går replikeringen vidare till differentialsynkroniseringsfasen. Nu är den virtuella datorn skyddad. Välj den skyddade virtuella datorn för att se om några diskar är uteslutna.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du kör en testväxling](site-recovery-test-failover-to-azure.md).
