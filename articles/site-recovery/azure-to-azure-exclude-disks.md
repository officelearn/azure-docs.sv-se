---
title: Azure Site Recovery - undanta diskar vid replikering av virtuella Azure-datorer med hjälp av Azure PowerShell | Microsoft Docs
description: Lär dig hur du undantar diskar Azure-datorer under Azure Site Recovery med hjälp av Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678283"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Undanta diskar från PowerShell replikering av virtuella Azure-datorer

Den här artikeln beskriver hur du undantar diskar när du replikerar virtuella Azure-datorer. Du kan undanta diskar för att optimera replikeringsbandbredden eller de resurser som använder dessa diskar. Den här funktionen är för närvarande bara tillgänglig via Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar:

- Se till att du förstår de [katastrofåterställning arkitektur och komponenter](azure-to-azure-architecture.md).
- Granska [kraven för stöd](azure-to-azure-support-matrix.md) för alla komponenter.
- Se till att du har AzureRm PowerShell ”Az” modulen. Om du vill installera eller uppdatera PowerShell, se [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Kontrollera att du har skapat ett recovery services-valv och skyddade virtuella datorer i minst en gång. Om du inte gjort detta, följer du processen vid [konfigurera haveriberedskap för virtuella Azure-datorer med Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Varför ska jag undanta diskar från replikering
Du kan behöva undanta diskar från replikering eftersom:

- Den virtuella datorn har nått [Azure Site Recovery-gränserna för att replikera data ändras priserna](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Data på den undantagna disken är inte viktigt eller behöver inte replikeras.

- Vill du spara lagrings- och nätverksresurser genom att inte replikera data.

## <a name="how-to-exclude-disks-from-replication"></a>Hur du undantar diskar från replikering

I vårt exempel replikera vi en virtuell dator som har ett operativsystem och tre datadiskar som finns i regionen östra USA till regionen USA, västra 2. Namnet på den virtuella datorn är *AzureDemoVM*. Vi undanta disk 1 och hålla diskar 2 och 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Hämta information om de virtuella datorerna att replikera

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

Få information om den virtuella datorns diskar. Den här informationen används senare när du startar replikeringen av den virtuella datorn.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikera virtuella Azure-datorer

I följande exempel förutsätter vi att du redan har ett cachelagringskonto och replikeringsprincip mappningar. Om du inte har dessa saker, följer du processen vid [konfigurera haveriberedskap för virtuella Azure-datorer med Azure PowerShell](azure-to-azure-powershell.md).

Replikera virtuella Azure-datorer med *hanterade diskar*.

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

När start-replikeringsåtgärden lyckas replikeras virtuella datorns data till återställningsregionen.

Du kan gå till Azure portal och se de replikerade virtuella datorerna under ”replikerade objekt”.

Replikeringen startar genom att seeda en kopia av replikeringsdiskarna för den virtuella datorn i återställningsregionen. Det här steget kallas fasen den inledande replikeringen.

När replikeringen har slutförts flyttar replikering till fasen differentiell synkronisering. Nu kan är den virtuella datorn skyddad. Välj den skyddade virtuella datorn för att se om alla diskar undantas.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [som kör ett redundanstest](site-recovery-test-failover-to-azure.md).
