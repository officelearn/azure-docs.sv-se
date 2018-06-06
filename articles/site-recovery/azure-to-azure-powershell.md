---
title: Azure Site Recovery - inställningar och testa katastrofåterställning för virtuella Azure-datorer med hjälp av Azure PowerShell | Microsoft Docs
description: Lär dig hur du ställer in katastrofåterställning för virtuella Azure-datorer med hjälp av Azure PowerShell för Azure Site Recovery.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 05/31/2018
ms.author: bsiva
ms.openlocfilehash: 3fa9ee27a1b9717d8011b7b46a1116f1f1ac1df5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716337"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Ställ in katastrofåterställning för virtuella Azure-datorer med hjälp av Azure PowerShell

I den här artikeln lär du konfigurera och testa katastrofåterställning för virtuella Azure-datorer med hjälp av Azure PowerShell. 

Lär dig att:

> [!div class="checklist"]
> - Skapa ett Recovery Services-valv.
> - Ange valvet kontexten för PowerShell-session.
> - Förbered valvet för att starta replikering av Azure virtuella datorer.
> - Skapa datornätverksmappningar.
> - Skapa storage-konton för att replikera virtuella datorer.
> - Replikera virtuella Azure-datorer till en återställning region för katastrofåterställning.
> - Utför ett redundanstest, verifiera, och rensa redundanstestet.
> - Redundans till regionen som recovery.

> [!NOTE]
> Inte alla scenario funktioner som är tillgängliga via portalen kan vara tillgängliga via Azure PowerShell. Några av de funktioner för scenariot som stöds för närvarande inte via Azure PowerShell är:
> - Möjligheten att replikera virtuella Azure-datorer som använder hanterade diskar.
> - Möjligheten att ange att alla diskar i en virtuell dator ska replikeras utan att explicit ange varje disk för den virtuella datorn.  

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [kraven för stöd](azure-to-azure-support-matrix.md) för alla komponenter.
- Du har version 5.7.0 eller senare av AzureRm PowerShell-modulen. Om du behöver installera eller uppgradera Azure PowerShell följer detta [Guide för att installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på Microsoft Azure-prenumerationen

Logga in på Azure-prenumerationen med cmdlet Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Välj din Azure-prenumeration. Använd cmdleten Get-AzureRmSubscription för att hämta listan över Azure-prenumerationer som du har åtkomst till. Välj den Azure-prenumerationen att arbeta med hjälp av cmdleten Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Hämta information om minst en virtuell dator replikeras

I exemplet i den här artikeln, kommer en virtuell dator i östra USA replikeras till och återställas i regionen västra USA 2. Den virtuella datorn replikeras är en virtuell dator med en OS-disk och en enskild disk. Namnet på den virtuella datorn som används i exemplet är AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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

Hämta diskinformation för diskarna för den virtuella datorn. Diskinformation kommer att användas senare när startar replikeringen för den virtuella datorn.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Skapa en resursgrupp att skapa Recovery Services-valvet. 

> [!IMPORTANT]
> * Recovery services-valvet och virtuella datorer som skyddas måste vara på olika platser i Azure.
> * Resursgruppen för Recovery services-valvet och virtuella datorer som skyddas måste vara på olika platser i Azure.
> * Recovery services-valvet och resursgruppen som tillhör den, kan finnas i samma Azure-plats.
 
I exemplet i den här artikeln är den virtuella datorn som skyddas i östra USA. Återställning regionen som valts för katastrofåterställning är regionen västra USA 2. Recovery services-valvet och resursgruppen för valvet, är båda i recovery region (västra USA 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```
   
Skapa ett Recovery services-valv. I exemplet nedan ett Recovery Services-valv med namnet a2aDemoRecoveryVault skapas i regionen västra USA 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 
## <a name="set-the-vault-context"></a>Ange valvet-kontext

> [!TIP]
> Azure Site Recovery PowerShell-modulen (AzureRm.RecoveryServices.SiteRecovery modul) levereras med lättanvända alias för de flesta cmdlets. Cmdletar i modulen vara i formatet  *\<åtgärden >-**AzureRmRecoveryServicesAsr**\<objekt >* och ha motsvarande alias som utformas  *\<Åtgärden >-**ASR**\<objekt >*. Den här artikeln använder cmdlet-alias för att underlätta läsning.

Ange valvet kontexten för användning i PowerShell-sessionen. Hämta filen valvet inställningar till för att göra detta, och importera den hämta filen i PowerShell-session för att ange valvet kontext. 

När utförs efterföljande Azure Site Recovery-åtgärder i PowerShell-sessionen i kontexten för det valda valvet. 

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Förbereda valvet för att starta replikering av Azure virtuella datorer

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Skapa ett Site Recovery fabric-objekt som representerar primary(source) region

Fabric-objektet i valvet representerar en Azure-region. Primära fabric-objektet är fabric-objekt som skapas för att representera Azure-region som virtuella datorer som skyddas till valvet tillhör. I exemplet i den här artikeln är den virtuella datorn som skyddas i östra USA.

> [!NOTE]
> Azure Site Recovery-åtgärder körs asynkront. När du initierar en åtgärd utförs en Azure Site Recovery-jobbet har skickats och ett jobb spåra objekt returneras. Använda jobbet spåra objekt att hämta senaste status för jobbet (Get-ASRJob) och för att övervaka status för åtgärden.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Om virtuella datorer från Azure-regioner skyddas till samma valvet, skapa en fabric-objekt för varje datakälla Azure-region.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. Skapa ett Site Recovery fabric-objekt som representerar regionen återställning

Återställning fabric-objektet representerar återställningen Azure-plats. Virtuella datorer kommer att replikeras till och återställas till recovery-region som representeras av recovery infrastrukturen (vid en redundansväxling). Återställningen Azure-regionen används i det här exemplet är västra USA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Skapa en behållare för Site Recovery-skydd i primära fabric

Skyddsbehållaren är en behållare som används för att gruppera replikerade objekt inom en infrastrukturresurs.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. Skapa en behållare för Site Recovery-skydd i infrastrukturen för återställning

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5. Skapa replikeringsprincip

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Skapa en skyddsbehållare mellan den primära platsen och återställningsplatsen skyddsbehållaren

En skyddsbehållare mappar primära skyddsbehållaren med en behållare för recovery-skydd och en replikeringsprincip. Skapa en mappning för varje replikeringsprincip som du använder för att replikera virtuella datorer mellan två skydd behållare.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. Skapa en skyddsbehållare för återställning efter fel (omvänd replikering efter en växling vid fel)

Efter en växling, när du är redo att hämta den virtuella datorn tillbaka till den ursprungliga Azure-regionen, återställning av du. För återställning efter fel, den misslyckade över virtuella datorn är omvänd replikeras från den misslyckade över region till den ursprungliga regionen. Roller i den ursprungliga och återställning region växla för omvänd replikering. Den ursprungliga regionen nu blir området återställning och vad ursprungligen recovery region nu blir den primära regionen. Skyddsbehållare för omvänd replikering representerar avstängda rollerna i regioner ursprungliga och återställning.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy 
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Skapa cachen lagringskonton och mål-lagringskonton

En cache-lagringskonto är ett standardlagringskonto i samma Azure-region som den virtuella datorn replikeras. Cache-lagringskontot används för att hålla replikeringsändringar tillfälligt innan ändringarna flyttas till återställningen Azure-region. Du kan välja att (men måste inte) ange olika cache lagringskonton för olika diskar för en virtuell dator.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

För virtuella datorer som inte använder hanterade diskar, är mål-lagringskontot lagringskonton i recovery region som diskar för den virtuella datorn replikeras. Mål-lagringskontot kan vara ett standardlagringskonto eller ett premiumlagringskonto. Välj den typ av lagringskonto krävs baserat på förändringstakten för data (i/o Skriv hastighet) för diskarna och Azure Site Recovery stöds omsättning gränser för lagringstyp.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Skapa nätverksmappningar

En nätverksmappningen mappar virtuella nätverk i den primära regionen till virtuella nätverk i området för återställning. Nätverksmappningen anger virtuella Azure-nätverket i området återställning som en virtuell dator i det primära virtuella nätverket ska gå över till. Ett Azure virtual network kan mappas till bara en enda virtuella Azure-nätverket i en region för återställning.

- Skapa en Azure-nätverk i recovery region ska gå över till

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Hämta det primära virtuella nätverket (vnet som den virtuella datorn är ansluten till)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to
    
    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Skapa nätverksmappningen mellan det primära virtuella nätverket och det virtuella nätverket för återställning
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
    
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    
   ```
- Skapa nätverksmappningen för omvänd riktning (återställning)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
        
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replikera virtuella Azure-datorn

Replikera den virtuella Azure-datorn.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

När replikeringsåtgärden start lyckas, replikeras virtuella data till regionen som recovery. 

Replikeringen startar genom att dirigera först en kopia av replikeringsdiskarna för den virtuella datorn i området för återställning. Det här steget kallas fasen inledande replikering. 

När den inledande replikeringen har slutförts flyttar replikering till fasen differentiell synkronisering. Nu är den virtuella datorn är skyddad och en redundanstestet kan utföras på den. Replikeringstillståndet på den replikerade objekt som representerar den virtuella datorn försätts i tillståndet ”skyddad” när den inledande replikeringen är klar.

Övervaka replikeringsstatus och replikeringshälsa för den virtuella datorn genom att hämta information om det replikeringsskyddade objektet som motsvarar den.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Utför ett redundanstest, verifiera, och rensa testa redundans

När replikeringen för den virtuella datorn har uppnått ett skyddat läge, kan en redundanstestet utföras på den virtuella datorn (på det replikeringsskyddade objektet för den virtuella datorn.)

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Att testa redundans.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Vänta tills redundanstestet att slutföra.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
När testjobbet för växling vid fel är klar kan du ansluta till testet redundansväxlade virtuella datorn och verifiera redundanstestningen.

När testet är klart på testet redundansväxlade virtuella datorn, testa redundans för Rensa test kopia genom att starta rensningen. Den här åtgärden tar bort test kopia av den virtuella datorn som har skapats av du testar redundansen.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Redundans till Azure

Växlingen vid fel den virtuella datorn till en specifik återställningspunkt.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

När redundansväxla, kan du utföra redundansväxlingen.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

Starta omvänd replikering för det replikeringsskyddade objektet med hjälp av cmdlet Update AzureRmRecoveryServicesAsrProtectionDirection efter en växling, när du är redo att gå tillbaka till den ursprungliga regionen.

## <a name="next-steps"></a>Nästa steg
Visa den [Azure Site Recovery PowerShell-referens ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) att lära dig hur du kan utföra andra uppgifter som att skapa Återställningsplaner och testa redundans för återställningsplaner med hjälp av PowerShell.
