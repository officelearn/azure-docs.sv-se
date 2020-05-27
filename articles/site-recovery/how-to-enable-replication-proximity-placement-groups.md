---
title: Replikera virtuella Azure-datorer som körs i närhets placerings grupper
description: Lär dig hur du replikerar virtuella Azure-datorer som körs i närhets placerings grupper med hjälp av Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 204ac3be46ac7ba0e1ea96e50379ca417b1299ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847641"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replikera virtuella Azure-datorer som körs i närhets placerings grupper till en annan region

Den här artikeln beskriver hur du replikerar, redundans och återställning efter fel för virtuella datorer som körs i en närhets placerings grupp till en sekundär region.

[Närhets placerings grupper](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) är en logisk grupperings funktion för virtuella Azure-datorer som du kan använda för att minska svars tiden mellan virtuella datorer som är kopplad till dina program. När de virtuella datorerna distribueras inom samma närhets placerings grupp, är de fysiskt placerade så nära varandra som möjligt. Placerings grupper för närhet är särskilt användbara för att tillgodose kraven för svars känsliga arbets belastningar.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Haveri beredskap med närhets placerings grupper

I ett typiskt scenario kan du ha dina virtuella datorer som körs i en närhets grupp för att undvika nätverks fördröjningen mellan olika nivåer av ditt program. Detta kan ge din program optimala nätverks fördröjning, men du vill skydda dessa program med hjälp av Site Recovery för ett region nivå haveri. Site Recovery replikerar data från en region till en annan Azure-region och tar upp datorer i Disaster Recovery-regionen i en händelse av redundans.

## <a name="considerations"></a>Överväganden

- Det bästa arbetet är att redundansväxla/redundansväxla de virtuella datorerna till en närhets placerings grupp. Men om den virtuella datorn inte kan hamna i närheten vid växling vid fel/återställning efter fel, kommer redundansväxlingen/failback fortfarande att ske, och virtuella datorer skapas utanför en närhets placerings grupp.
-  Om en tillgänglighets uppsättning fästs på en närhets placerings grupp och under redundansväxling/failback-datorer i tillgänglighets uppsättningen har en tilldelnings begränsning skapas de virtuella datorerna utanför både tillgänglighets uppsättningen och närhets placerings gruppen.
-  Site Recovery för närhets placerings grupper stöds inte för ohanterade diskar.

## <a name="prerequisites"></a>Krav

1. Kontrol lera att du har modulen Azure PowerShell AZ. Om du behöver installera eller uppgradera Azure PowerShell, följ den här [guiden för att installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Konfigurera Site Recovery för Virtual Machines i närhets placerings grupp

### <a name="azure-to-azure"></a>Azure till Azure

1. [Logga in](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription) på ditt konto och ange din prenumeration.
2. Hämta information om den virtuella dator som du planerar att replikera enligt vad som anges [här](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated).
3. [Skapa](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault) Recovery Services-valvet och [Ange](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context) valv kontexten.
4. Förbered valvet för att starta replikering av den virtuella datorn. Detta innebär att du skapar ett [Service Fabric-objekt](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) för både primär-och återställnings regioner.
5. [Skapa](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric) en Site Recovery skydds behållare för både den primära och återställnings infrastrukturen.
6. [Skapa](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy) en replikeringsprincip.
7. Skapa en skydds container mappning mellan primär-och återställnings skydds behållare med hjälp av [de här](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) stegen och [here](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)en skydds behållar mappning för återställning efter fel
8. Skapa ett lagrings konto för cache genom att följa [dessa](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account) steg.
9. Skapa de nätverks mappningar som krävs enligt vad som anges [här](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings).
10. Använd PowerShell-cmdleten nedan för att replikera en virtuell Azure-dator med Managed disks – 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType -RecoveryProximityPlacementGroupId $recPpg.Id

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
När åtgärden starta replikeringen har slutförts replikeras data för virtuella datorer till återställnings regionen.

Replikeringen börjar genom att initiera en kopia av de replikerade diskarna för den virtuella datorn i återställnings regionen. Den här fasen kallas för fasen inledande replikering.

När den inledande replikeringen har slutförts flyttas replikeringen till fasen differentiell synkronisering. Den virtuella datorn är nu skyddad och en redundanstest kan utföras på den. Replikeringstillståndet för det replikerade objektet som representerar den virtuella datorn går till skyddat tillstånd när den inledande replikeringen har slutförts.

Övervaka replikeringstillståndet och replikeringens hälso tillstånd för den virtuella datorn genom att hämta information om det skyddade replikerings objekt som motsvarar det. 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Följ [dessa](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover) steg om du vill göra ett redundanstest, validera och rensa redundanstest.
12. Om du vill redundansväxla följer du stegen som anges [här](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure).
13. Om du vill återaktivera skyddet och återställning efter fel till käll regionen använder du PowerShell-cmdleten nedan –

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. Om du vill inaktivera replikering följer du stegen [här](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication).

### <a name="vmware-to-azure"></a>VMware till Azure

1. Se till att [förbereda dina lokala VMware-servrar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises) för haveri beredskap till Azure.
2. Logga in på ditt konto och Ställ in din prenumeration enligt vad som anges [här](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure).
3. [Konfigurera ett Recovery Services](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault) valv och [Ange valv kontext](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context).
4. [Verifiera](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration) din valv registrering.
5. [Skapa](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy) en replikeringsprincip.
6. [Lägg till](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms) en vCenter-Server och identifiera virtuella datorer och [skapa](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication) lagrings konton för replikering.
7. Om du vill replikera VMware-Virtual Machines läser du informationen här och följer PowerShell-cmdleten nedan –

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. Du kan kontrol lera replikeringstillståndet och replikeringens hälso tillstånd för den virtuella datorn med cmdleten Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. Konfigurera inställningarna för redundans genom att följa stegen [här](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings).
10. [Kör](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover) ett redundanstest. 
11. Redundansväxla till Azure med hjälp av [de här](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure) stegen.

### <a name="hyper-v-to-azure"></a>Hyper-V till Azure

1. Se till att [förbereda dina lokala Hyper-V-servrar](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) för haveri beredskap till Azure.
2. [Logga in](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account) på Azure.
3. [Konfigurera](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault) valvet och [Ange](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context) Recovery Services valv kontext.
4. [Skapa](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site) en Hyper-V-plats.
5. [Installera](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent) providern och agenten.
6. [Skapa](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy) en replikeringsprincip.
7. Aktivera replikering med hjälp av stegen nedan – 
    
    a. Hämta det skydds bara objekt som motsvarar den virtuella dator som du vill skydda, enligt följande:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Skydda den virtuella datorn. Om den virtuella dator som du skyddar har fler än en disk ansluten, anger du operativ system disken med hjälp av parametern OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. Vänta tills de virtuella datorerna når ett skyddat tillstånd efter den inledande replikeringen. Detta kan ta en stund, beroende på faktorer som mängden data som ska replikeras och den tillgängliga överordnade bandbredden till Azure. När ett skyddat tillstånd är på plats uppdateras jobbets tillstånd och StateDescription enligt följande: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Uppdatera återställnings egenskaper (till exempel storleken på den virtuella dator rollen) och det Azure-nätverk som du vill ansluta VM-NÄTVERKSKORTet till efter redundansväxlingen.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Kör ett redundanstest [.](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover)


## <a name="next-steps"></a>Nästa steg

Följ stegen som beskrivs [här](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback)om du vill utföra skydd och återställning efter fel för VMware till Azure.

Om du vill utföra redundans för Hyper-V till Azure följer du stegen som beskrivs [här](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) och utför återställning efter fel genom att följa stegen som beskrivs [här](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback).

Mer information finns i [redundansväxlingen i Site Recovery](site-recovery-failover.md).
