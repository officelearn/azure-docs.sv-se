---
title: "Replikera och redundansväxla virtuella VMware-datorer till Azure med Azure Site Recovery PowerShell | Microsoft Docs"
description: "Replikera och redundansväxla virtuella VMware-datorer till Azure med Azure Site Recovery PowerShell"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 14/12/2017
ms.author: bsiva
ms.openlocfilehash: 3cf2478eb810961604e1218731f5303abd0f611a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="replicate-and-failover-vmware-virtual-machines-to-azure-using-azure-site-recovery-powershell"></a>Replikera och redundansväxla virtuella VMware-datorer till Azure med Azure Site Recovery PowerShell

I den här artikeln visas hur du replikerar och redundans virtuella VMware-datorer till Azure med Azure PowerShell. 

Lär dig att:

> [!div class="checklist"]
> - Skapa ett Recovery Services-valv.
> - Ange valvet kontext.
> - Verifiera att din konfigurationsservern och skala ut servrar är registrerade för valvet.
> - Skapa en replikeringsprincip och koppla den för användning med konfigurationsservern.
> - Lägg till en vCenter-server och identifiera virtuella VMware-datorer.
> - Skapa storage-konton för att replikera virtuella datorer.
> - Replikera virtuella VMware-datorer till Azure storage-konton.
> - Konfigurera inställningar för växling vid fel för replikering av virtuella datorer.
> - Utför ett redundanstest, verifiera, och rensa redundanstestet.
> - Redundans till Azure.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:
- Se till att du förstår de [scenariots arkitektur och komponenter](concepts-vmware-to-azure-architecture.md).
- Granska de [supportkrav](site-recovery-support-matrix-to-azure.md) för alla komponenter.
- Du har version 5.0.1 eller större på AzureRm PowerShell-modulen. Om du behöver installera eller uppgradera Azure PowerShell följer detta [Guide för att installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på Microsoft Azure-prenumerationen

Logga in på Azure-prenumerationen med cmdlet Login-AzureRmAccount

```azurepowershell
Login-AzureRmAccount
```
Välj den Azure-prenumeration du vill replikera dina virtuella datorer i VMware till. Använd cmdleten Get-AzureRmSubscription för att hämta listan över Azure-prenumerationer som du har åtkomst till. Välj den Azure-prenumerationen att arbeta med hjälp av cmdleten Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

* Skapa en resursgrupp att skapa Recovery Services-valvet. I exemplet nedan resursgruppen heter VMwareDRtoAzurePS och skapas i Östasien region.

```azurepowershell
New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
```
```
ResourceGroupName : VMwareDRtoAzurePS
Location          : eastasia
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
```
   
* Skapa ett Recovery services-valv. I exemplet nedan Recovery services-ventilen heter VMwareDRToAzurePs och skapas i Östasien region och i resursgruppen som du skapade i föregående steg.

```azurepowershell
New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
```
```
Name              : VMwareDRToAzurePs
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
Type              : Microsoft.RecoveryServices/vaults
Location          : eastasia
ResourceGroupName : VMwareDRToAzurePs
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 

* Hämta valvregistreringsnyckeln för valvet. Valvregistreringsnyckeln används för att registrera konfigurationsservern lokalt för det här valvet. Registreringen är en del av konfigurationsservern Programvaruinstallation.

```azurepowershell
#Get the vault object by name and resource group and save it to the $vault PowerShell variable 
$vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

#Download vault registration key to the path C:\Work
Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
```
```
FilePath
--------
C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
```

Använd hämtade valvregistreringsnyckeln och följ stegen i artiklar som anges nedan för att slutföra installationen och registrering av konfigurationsservern.
* [Välja skyddsmål](site-recovery-set-up-vmware-to-azure.md#choose-your-protection-goals)
* [Konfigurera källmiljön](site-recovery-set-up-vmware-to-azure.md#set-up-the-source-environment) 

## <a name="set-the-vault-context"></a>Ange valvet-kontext

> [!TIP]
> Azure Site Recovery PowerShell-modulen (AzureRm.RecoveryServices.SiteRecovery modul) levereras med lättanvända alias för de flesta cmdlets. Cmdletar i modulen vara i formatet  *\<åtgärden >-**AzureRmRecoveryServicesAsr**\<objekt >* och har motsvarande alias som utformas  *\<åtgärden >-**ASR**\<objekt >*. Den här artikeln använder cmdlet-alias för att underlätta läsning.

Ange valvet samband med cmdlet Set-ASRVaultContext. När utförs efterföljande Azure Site Recovery-åtgärder i PowerShell-sessionen i kontexten för det valda valvet. I exemplet nedan, valvet information från $vault används variabeln för att ange valvet kontexten för PowerShell-session.
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

Nedanstående avsnitt i den här artikeln förutsätter att kontexten valv för Azure Site Recovery-åtgärder har ställts in.

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>Verifiera att din konfigurationsservern och skala ut servrar är registrerade för valvet

 Anta att:
- En konfigurationsservern med namnet *ConfigurationServer* har registrerats för det här valvet
- Ytterligare Process en Server med namnet *ScaleOut process* har registrerats på *ConfigurationServer*
- Konton som heter *vCenter_account*, *WindowsAccount*, och *LinuxAccount* har ställts in på konfigurationsservern. Dessa konton som används för att lägga till vCenter-servern att identifiera virtuella datorer, och push-installera programvaran för tjänsten mobility på Windows- och Linux-servrar som ska replikeras.

Registrerade Konfigurationsservrar representeras av ett fabric-objekt i Azure Site Recovery. I det här steget hämta listan över fabric objekt i valvet och identifiera konfigurationsservern.

```azurepowershell
# Verify that the Configuration server is successfully registered to the vault
$ASRFabrics = Get-ASRFabric
$ASRFabrics.count
```
```
1
```
```azurepowershell
#Print details of the Configuration Server
$ASRFabrics[0]
```
```
Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
FriendlyName          : ConfigurationServer
ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                        /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
FabricType            : VMware
SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
```

* Identifiera de servrar som kan användas för att replikera virtuella datorer.

```azurepowershell
$ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
for($i=0; $i -lt $ProcessServers.count; $i++) {
 "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
}
```
```
0     ScaleOut-ProcessServer
1     ConfigurationServer
```

Från resultatet ovan ***$ProcessServers [0]*** motsvarar *ScaleOut process* och ***$ProcessServers [1]*** motsvarar rollen Processervern på *ConfigurationServer*

* Identifiera konton som har ställts in på konfigurationsservern.

```azurepowershell
$AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
#Print the account details
$AccountHandles
```
```
AccountId AccountName
--------- -----------
1         vCenter_account
2         WindowsAccount
3         LinuxAccount
```

Från resultatet ovan ***$AccountHandles [0]*** kontots *vCenter_account*, ***$AccountHandles [1]*** konto *WindowsAccount*, och ***$AccountHandles [2]*** konto *LinuxAccount*

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>Skapa en replikeringsprincip och koppla den för användning med konfigurationsservern

I det här steget skapas två replikeringsprinciper. En princip för att replikera virtuella VMware-datorer till Azure och den andra att replikera misslyckades över virtuella datorer som körs i Azure tillbaka till lokala VMware-platsen.

> [!NOTE]
> De flesta Azure Site Recovery-åtgärder körs asynkront. När du initierar en åtgärd utförs en Azure Site Recovery-jobbet har skickats och ett jobb spåra objekt returneras. Det här jobbet spåra objektet kan användas för att övervaka status för åtgärden.

* Skapa en replikeringsprincip med namnet *ReplicationPolicy* att replikera virtuella VMware-datorer till Azure med de angivna egenskaperna.

```azurepowershell
$Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

# Track Job status to check for completion
while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
}

#Display job status
$Job_PolicyCreate
```
```
Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                   9-479f-430d-b76b-6bc7eb2d0b3e
Type             :
JobType          : AddProtectionProfile
DisplayName      : Create replication policy
ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:49:24 AM
EndTime          : 11/24/2017 2:49:23 AM
TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
TargetObjectType : ProtectionProfile
TargetObjectName : ReplicationPolicy
AllowedActions   :
Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
Errors           : {}
```

* Skapa en replikeringsprincip för återställning efter fel från Azure till lokala VMware-platsen.

```azurepowershell
$Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
```

Använd Jobbinformationen i *$Job_FailbackPolicyCreate* att spåra åtgärden att slutföras.

* Skapa en skyddsbehållare för att mappa replikeringsprinciper med konfigurationsservern.

```azurepowershell
#Get the protection container corresponding to the Configuration Server
$ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

#Get the replication policies to map by name.
$ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
$FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

# Associate the replication policies to the protection container corresponding to the Configuration Server. 

$Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
}
$Job_AssociatePolicy.State

$Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
}
$Job_AssociateFailbackPolicy.State

```

## <a name="add-a-vcenter-server-and-discover-vmware-virtual-machines"></a>Lägg till en vCenter-server och identifiera virtuella VMware-datorer

Lägg till en vCenter-Server med IP-adress eller värdnamn. Den **-port** parametern anger vilken port på vCenter-servern kan ansluta till **-namnet** parametern anger ett eget namn för vCenter-servern och **-konto** parametern anger referensen kontot på konfigurationsservern du använder för att identifiera virtuella datorer som hanteras av vCenter-servern.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts"></a>Skapa lagringskonton

I det här steget skapas storage-konton som ska användas för replikering. Dessa storage-konton används senare för att replikera virtuella datorer. Se till att storage-konton skapas i samma Azure-region som valvet. Du kan hoppa över det här steget om du planerar att använda ett befintligt lagringskonto för replikering.

> [!NOTE]
> Du måste ange en ytterligare standardlagringskonto (loggen storage-konto) för att replikera lokala virtuella datorer till ett premiumlagringskonto. Storage-konto loggen innehåller replikeringsloggar som en mellanliggande arkivet tills loggarna kan tillämpas på målet för premium-lagring.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>Replikera virtuella VMware-datorer

Det tar ungefär 15-20 minuter för virtuella datorer som ska identifieras från vCenter-servern. När identifieras, skapas ett skyddsobjekt objekt i Azure Site Recovery för varje identifierad virtuell dator. I det här steget replikeras tre av de identifierade virtuella datorerna till Azure Storage-konton som har skapats i föregående steg.

Du behöver följande information för att skydda en identifierad virtuell dator:
* Skyddsobjekt replikeras.
* Lagringskontot för att replikera den virtuella datorn till. Dessutom kan behövs en logg lagring för att skydda virtuella datorer till ett premiumlagringskonto.
* Process-Server som ska användas för replikering. Listan över tillgängliga servrar har hämtats och sparats i den ***$ProcessServers [0]****(ScaleOut-process)* och ***$ProcessServers [1]*** *(ConfigurationServer)* variabler.  
* Kontot du använder för att push-installation Mobility service programvara på datorerna. Listan med tillgängliga konton har hämtats och lagras i den ***$AccountHandles*** variabeln.
* Skyddsbehållare för replikeringsprincip som ska användas för replikering.
* Resursgruppen där virtuella datorer måste skapas för växling vid fel.
* Du kan också Azure-nätverk och undernät som den redundansväxlade virtuella datorn ska anslutas.

Replikera nu följande virtuella datorer med hjälp av inställningarna i den här tabellen


|Virtuell dator  |Processervern        |Lagringskonto              |Logga Storage-konto  |Princip           |Konto för installation av Mobilitetstjänsten service|Målresursgruppen  | Mål virtuellt nätverk  |Mål-undernät  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-process|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Undernät 1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| Gäller inte                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Undernät 1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| Gäller inte                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Undernät 1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

När jobbet aktivera replikeringen är klar startas inledande replikering för virtuella datorer. Den inledande replikeringen kan ta en stund beroende på mängden data som ska replikeras och den tillgängliga bandbredden för replikering. Den virtuella datorn flyttas till ett skyddat läge när den inledande replikeringen är klar. När den virtuella datorn når ett skyddat läge kan du utföra ett test av redundansen för den virtuella datorn, lägger du till den återställningsplaner osv.

Du kan kontrollera replikeringsstatus och replikeringshälsa för den virtuella datorn med cmdleten Get-ASRReplicationProtectedItem.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>Konfigurera inställningar för växling vid fel för replikering av virtuella datorer

Inställningar för växling vid fel för skyddade datorer kan uppdateras med cmdlet Set-ASRReplicationProtectedItem. Några av de inställningar som kan uppdateras via denna cmdlet är:
* Namnet på den virtuella datorn skapas vid växling vid fel
* VM-storlek för den virtuella datorn skapas vid växling vid fel
* Azure-nätverk och undernät som nätverkskort för den virtuella datorn ska anslutas till på redundanskluster
* Växling till hanterade diskar
* Använd Azure Hybrid Använd förmån
* Tilldela en statisk IP-adress från det virtuella målnätverket som ska tilldelas den virtuella datorn på växling vid fel.

I det här exemplet vi uppdatera VM-storlek för den virtuella datorn skapas vid växling vid fel för den virtuella datorn *Win2K12VM1* och ange att den virtuella datorn Använd hanterade diskar på växling vid fel.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Utför ett redundanstest, verifiera, och rensa testa redundans

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
När testjobbet för växling vid fel är klar ser du att en virtuell dator med suffixet *”-Test”* (Win2K12VM1-Test i detta fall) till dess namn har skapats i Azure. 

Nu kan du ansluta till testet redundansväxlade virtuella datorn och verifiera redundanstestningen.

Rensa testa redundans med cmdleten Start-ASRTestFailoverCleanupJob. Den här åtgärden tar bort den virtuella datorn skapas som en del av redundanstestet.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>Redundans till Azure

I det här steget ska vi växling vid fel den virtuella datorn Win2K12VM1 till en specifik återställningspunkt.

```azurepowershell
# Get the list of available recovery points for Win2K12VM1
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
```
```
CrashConsistent 11/24/2017 5:28:25 PM
```
```azurepowershell

#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 60;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
$Job_Failover.State
```
```
Succeeded
```

När redundansväxla, kan du genomföra redundans åtgärden och installationsprogrammet omvänd replikering från Azure tillbaka till lokala VMware-platsen.

## <a name="next-steps"></a>Nästa steg
Visa den [Azure Site Recovery PowerShell-referens ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) att lära dig hur du kan utföra andra uppgifter som att skapa Återställningsplaner och testa redundans för återställningsplaner med hjälp av PowerShell.
