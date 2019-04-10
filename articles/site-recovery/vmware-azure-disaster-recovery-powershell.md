---
title: Konfigurera haveriberedskap för virtuella VMware-datorer till Azure med PowerShell i Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar replikering och redundans till Azure för haveriberedskap för virtuella VMware-datorer med hjälp av PowerShell i Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 04/08/2019
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: 5490149f199c2d7887716ceae3f035527ad33961
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280187"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Konfigurera haveriberedskap för virtuella VMware-datorer till Azure med PowerShell

I den här artikeln visas hur du replikerar och redundans VMware-datorer till Azure med hjälp av Azure PowerShell.

Lär dig att:

> [!div class="checklist"]
> - Skapa ett Recovery Services-valv och ange valvets sammanhang.
> - Verifiera registrera servern i valvet.
> - Konfigurera replikering, inklusive en replikeringsprincip. Lägg till vCenter-servern och identifiera virtuella datorer.
> - Lägg till en vCenter-server och identifiera
> - Skapa lagringskonton för att lagra data för replikering och replikera de virtuella datorerna.
> - Utför en redundans. Konfigurera inställningar för växling vid fel, utföra en inställning för replikering av virtuella datorer.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](vmware-azure-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-to-azure.md) för alla komponenter.
- Du har Azure PowerShell `Az` modulen. Om du behöver installera eller uppgradera Azure PowerShell kan du följa den här [guide om hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med hjälp av cmdleten Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Ange den prenumeration du vill replikera dina VMware-datorer till. Använd cmdleten Get-AzSubscription för att hämta listan över Azure-prenumerationer som du har åtkomst till. Ange prenumerationen du arbetar med hjälp av cmdleten Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. Skapa en resursgrupp som du skapar Recovery Services-valvet. I exemplet nedan heter VMwareDRtoAzurePS resursgruppen och de har skapats i regionen östra Asien.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Skapa ett Recovery services-valv. I exemplet nedan Recovery services-valv heter VMwareDRToAzurePs och har skapats i regionen östra Asien, och i resursgruppen som skapades i föregående steg.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
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

3. Ladda ned valvregistreringsnyckeln för valvet. Valvregistreringsnyckeln används för att registrera den lokala konfigurationsservern till det här valvet. Registreringen är en del av installationen för Configuration Server-programvara.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Använd hämtade valvregistreringsnyckeln och följ stegen i artiklar som anges nedan för att slutföra installationen och registreringen av konfigurationsservern.
   - [Välja skyddsmål](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Konfigurera källmiljön](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Ange valvets sammanhang

Anger du valvkontexten med hjälp av cmdleten Set-ASRVaultContext. När utförs efterföljande Azure Site Recovery-åtgärder i PowerShell-sessionen i kontexten för det valda valvet.

> [!TIP]
> Azure Site Recovery PowerShell-modulen (Az.RecoveryServices modul) levereras med lättanvända alias för de flesta cmdletarna. Cmdlets i modulen vara i formatet  *\<åtgärden >-**AzRecoveryServicesAsr**\<objekt >* och har motsvarande alias som vara i formatet  *\< Åtgärden >-**ASR**\<objekt >*. Den här artikeln använder cmdlet-alias för enklare läsning.

I exemplet nedan, valvinformationen från $vault används variabeln för att ange valvets sammanhang för PowerShell-session.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Som ett alternativ till cmdlet Set-ASRVaultContext använda en också cmdleten Import-AzRecoveryServicesAsrVaultSettingsFile att ställa in valvets sammanhang. Ange sökvägen där nyckelfilen för valvregistrering finns som parametern - path till Import-AzRecoveryServicesAsrVaultSettingsFile-cmdlet. Exempel:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Följande avsnitt i den här artikeln förutsätter att valvets sammanhang för Azure Site Recovery-åtgärder har ställts in.

## <a name="validate-vault-registration"></a>Verifiera registrering av valvet

I det här exemplet har vi följande:

- En konfigurationsserver (**ConfigurationServer**) har registrerats i valvet.
- En ytterligare processerver (**ScaleOut-process**) har registrerats på *ConfigurationServer*
- Konton (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) har ställts in på konfigurationsservern. Dessa konton används för att lägga till vCenter-servern, att identifiera virtuella datorer och för att push-installation mobilitetstjänstprogramvaran på Windows och Linux-servrar som ska replikeras.

1. Registrerade konfigurationsservrar representeras av ett fabric-objekt i Site Recovery. Hämta listan över fabric objekt i valvet och identifiera konfigurationsservern.

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

2. Identifiera processervrar som kan användas för att replikera virtuella datorer.

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

   Från utdatan ovan ***$ProcessServers [0]*** motsvarar *ScaleOut-process* och ***$ProcessServers [1]*** motsvarar Process Server-rollen på *ConfigurationServer*

3. Identifiera konton som har ställts in på konfigurationsservern.

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

   Från utdatan ovan ***$AccountHandles [0]*** kontots *vCenter_account*, ***$AccountHandles [1]*** till kontot *WindowsAccount*, och ***$AccountHandles [2]*** till kontot *LinuxAccount*

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

I det här steget skapas två replikeringsprinciper. En policy för att replikera virtuella VMware-datorer till Azure och det andra att replikera redundansväxlade virtuella datorer som körs i Azure tillbaka till den lokala VMware-webbplatsen.

> [!NOTE]
> De flesta Azure Site Recovery-åtgärder körs asynkront. När du har initierat en åtgärd kan en Azure Site Recovery-jobb skickas och ett jobb spåra objekt returneras. Det här jobbet spåra objekt kan användas för att övervaka status för åtgärden.

1. Skapa en replikeringsprincip med namnet *ReplicationPolicy* att replikera virtuella VMware-datorer till Azure med de angivna egenskaperna.

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

2. Skapa en replikeringsprincip för återställning efter fel från Azure till lokalt VMware-webbplatsen.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Använda Jobbinformationen i *$Job_FailbackPolicyCreate* att spåra åtgärden att slutföras.

   * Skapa en skyddsbehållarmappning för att mappa replikeringsprinciper med konfigurationsservern.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Lägg till en vCenter-server och identifiera virtuella datorer

Lägg till en vCenter-Server med IP-adress eller värdnamn. Den **-port** parametern anger porten på vCenter-servern för att ansluta till, **-namnet** parametern anger ett eget namn för vCenter-servern och **-konto** parametern anger referensen kontot på konfigurationsservern ska användas för identifiering av virtuella datorer som hanteras av vCenter-servern.

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

## <a name="create-storage-accounts-for-replication"></a>Skapa lagringskonton för replikering

I det här steget skapas storage-konton som ska användas för replikering. Dessa konton används senare för att replikera virtuella datorer. Se till att lagringskontona som skapas i samma Azure-region som valvet. Du kan hoppa över det här steget om du planerar att använda ett befintligt lagringskonto för replikering.

> [!NOTE]
> Att replikera lokala virtuella datorer till ett premium storage-konto, måste du ange ett ytterligare standard storage-konto (logglagringskontot). Logglagringskontot sparar replikeringsloggar som en mellanliggande arkivet tills loggarna kan tillämpas på målet för premium-lagring.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikera virtuella VMware-datorer

Det tar cirka 15-20 minuter för virtuella datorer som ska identifieras från vCenter-servern. När kan identifieras, skapas ett objekt för objekt som kan skyddas i Azure Site Recovery för varje identifierad virtuell dator. I det här steget replikeras tre av de identifierade virtuella datorerna till Azure Storage-konton som har skapats i föregående steg.

Du behöver följande information för att skydda en identifierad virtuell dator:

* Skyddsobjekt replikeras.
* Lagringskontot för att replikera den virtuella datorn. Dessutom kan behövs en logglagring för att skydda virtuella datorer till ett premium storage-konto.
* Processervern som ska användas för replikering. I listan över tillgängliga servrar har hämtats och sparats i den ***$ProcessServers [0]***  *(ScaleOut-process)* och ***$ProcessServers [1]*** *(ConfigurationServer)* variabler.
* Vilket konto du använder för att push-installera mobilitetstjänsten på datorerna. Listan över tillgängliga konton har hämtats och lagras i den ***$AccountHandles*** variabeln.
* Skyddsbehållarmappning för replikeringsprincipen som ska användas för replikering.
* Den resursgrupp där virtuella datorer måste skapas vid redundansväxling.
* Du kan också Azure-nätverk och undernät som den redundansväxlade virtuella datorn ska anslutas.

Nu replikera följande virtuella datorer med hjälp av inställningarna i den här tabellen


|Virtuell dator  |Process Server        |Lagringskonto              |Logglagringskontot  |Princip           |Konto för installation av Mobilitetstjänsten|Målresursgrupp  | Virtuellt målnätverk  |Målundernät  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| Gäller inte                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| Gäller inte                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableReplication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Efter att aktivera replikeringen är klar startas den inledande replikeringen för virtuella datorer. Den inledande replikeringen kan ta en stund beroende på mängden data som ska replikeras och den tillgängliga bandbredden för replikering. När den inledande replikeringen har slutförts flyttar den virtuella datorn till ett skyddat läge. När den virtuella datorn når ett skyddat läge som du kan utföra ett redundanstest för den virtuella datorn kan du lägga till den i återställningsplaner osv.

Du kan kontrollera replikeringsstatus och tillståndet för den virtuella datorn med cmdleten Get-ASRReplicationProtectedItem replikeringen.

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

## <a name="configure-failover-settings"></a>Konfigurera inställningar för växling vid fel

Inställningar för växling vid fel för skyddade datorer kan uppdateras med hjälp av cmdleten Set-ASRReplicationProtectedItem. Några av de inställningar som kan uppdateras via denna cmdlet är:
* Namnet på den virtuella datorn skapas vid redundans
* VM-storlek för den virtuella datorn skapas vid redundans
* Azure-nätverk och undernät som nätverkskort för den virtuella datorn ska anslutas till vid redundans
* Redundans till hanterade diskar
* Apply Azure Hybrid Use Benefit
* Tilldela en statisk IP-adress från virtuellt Målnätverk som ska tilldelas till den virtuella datorn vid redundansväxling.

I det här exemplet vi uppdatera VM-storleken för den virtuella datorn skapas vid redundans för den virtuella datorn *Win2K12VM1* och ange att den virtuella datorn Använd hanterade diskar vid redundansväxling.

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

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. Kör en DR-test (testa redundans) på följande sätt:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. När testjobbet för redundans är klar ser du att en virtuell dator suffix med *”-testa”* (Win2K12VM1-Test i det här fallet) till dess namn har skapats i Azure.
3. Du kan nu ansluta till test-redundansväxlade virtuella datorn och verifiera redundanstestningen.
4. Rensa redundanstestet med hjälp av cmdleten Start-ASRTestFailoverCleanupJob. Den här åtgärden tar bort den virtuella datorn som skapades som en del av redundanstestet.

   ```azurepowershell
   $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Växla över till Azure

I det här steget ska vi inte över den virtuella datorn Win2K12VM1 till en specifik återställningspunkt.

1. Hämta en lista över tillgängliga återställningspunkter som ska användas för redundansen:
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

2. När redundansväxlats, du kan commit igen och konfigurera omvänd replikering från Azure tillbaka till den lokala VMware-webbplatsen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du automatiserar flera uppgifter med hjälp av den [referens för Azure Site Recovery PowerShell](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).
