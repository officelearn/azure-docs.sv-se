---
title: Azure Site Recovery - exkludera disken under replikering av virtuella Azure-datorer med Azure PowerShell | Microsoft Docs
description: Lär dig hur du undantar en disk för Azure-datorer med Azure Site Recovery med hjälp av Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 1c278d810df7e5ba8701529a59987c9bb16fa40c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044133"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Undanta diskar från replikering av virtuella Azure-datorer till Azure med hjälp av Azure PowerShell

Den här artikeln beskriver hur du undantar diskar vid replikering av virtuella Azure-datorer. Det här undantaget kan optimera replikeringsbandbredden som används eller optimera resurser som används av dessa diskar på målsidan. Den här funktionen är för närvarande exponeras endast via Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [kraven för stöd](azure-to-azure-support-matrix.md) för alla komponenter.
- Du har Azure PowerShell `Az` modulen. Om du behöver installera eller uppgradera Azure PowerShell kan du följa den här [guide om hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/install-az-ps).
- Du redan har skapat Recovery services-valv och har gjort skydd av virtuella datorer minst en gång. Om inte sedan göra det med hjälp av dokumentation som nämns [här](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Varför ska jag undanta en disk från replikering?
Vanliga orsaker till att diskar undantas från replikering:

- Den virtuella datorn har nått [Azure Site Recovery-gränserna för att replikera data ändras priser](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- Data på den undantagna disken var inte viktiga eller behövde inte replikeras.

- Du kan spara lagrings- och nätverksresurser genom att inte replikera dataomsättningen.


## <a name="how-to-exclude-disks-from-replication"></a>Hur undantar jag en disk från replikering?

I exemplet i den här artikeln har en virtuell dator som har 1 OS- och 3 datadiskar i regionen USA, östra kommer att replikeras till regionen USA, västra 2. Namnet på den virtuella datorn som används i exemplet är AzureDemoVM. och vi ska utesluta Disk 1 och behåller disk 2 och 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Hämta information om virtuella datorer som ska replikeras

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


Hämta diskinformation för diskar på den virtuella datorn. Diskinformation kommer att användas senare när starta replikering för den virtuella datorn.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Replikera virtuella Azure-datorer

I det exemplet nedan har vi antas att du redan har ett cachelagringskonto, replikeringsprincip och mappningar. Om inte sedan göra det med hjälp av dokumentation som nämns [här](azure-to-azure-powershell.md) 


Replikera virtuella Azure-datorer med **hanterade diskar**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

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


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

När replikering startåtgärd lyckas, replikeras data för virtuella datorer till återställningsregionen.

Du kan gå till Azure-portalen och under replikerade objekt som du kan se de virtuella datorerna komma replikeras.
Replikeringen startar genom att först seeding en kopia av replikeringsdiskarna för den virtuella datorn i återställningsregionen. Det här steget kallas för den inledande replikering-fasen.

När den inledande replikeringen har slutförts flyttar replikering till differentiell synkroniseringsfasen. Nu kan är den virtuella datorn skyddad. Klicka på den skyddade virtuella datorn > diskar för att se som om disken är exkluderad eller inte.

## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om hur du kör ett redundanstest.
