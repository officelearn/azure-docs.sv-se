---
title: Konfigurera VMware-haveriberedskap med PowerShell i Azure Site Revoery
description: Lär dig hur du konfigurerar replikering och redundans till Azure för haveriberedskap av virtuella datorer med hjälp av PowerShell i Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: d2dfaab3d01ea29b0f9ecba1e9d748415bed2edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257204"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Konfigurera haveriberedskap av virtuella virtuella datorer med VMware till Azure med PowerShell

I den här artikeln ser du hur du replikerar och redundans VMware virtuella datorer till Azure med Azure PowerShell.

Lär dig att:

> [!div class="checklist"]
> - Skapa ett Recovery Services-valv och ange valvkontexten.
> - Verifiera serverregistrering i valvet.
> - Ställ in replikering, inklusive en replikeringsprincip. Lägg till din vCenter-server och upptäck virtuella datorer.
> - Lägga till en vCenter-server och upptäck
> - Skapa lagringskonton för att lagra replikeringsloggar eller data och replikera de virtuella datorerna.
> - Utför en redundans. Konfigurera redundansinställningar, utför en inställningarna för att replikera virtuella datorer.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar:

- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](vmware-azure-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-to-azure.md) för alla komponenter.
- Du har Azure `Az` PowerShell-modulen. Om du behöver installera eller uppgradera Azure PowerShell följer du den här [guiden för att installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med cmdlet connect-azaccount:

```azurepowershell
Connect-AzAccount
```
Välj den Azure-prenumeration som du vill replikera dina virtuella VMware-datorer till. Använd cmdleten Get-AzSubscription för att få en lista över Azure-prenumerationer som du har åtkomst till. Välj den Azure-prenumeration som ska fungera med select-azsubscription cmdlet.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. Skapa en resursgrupp där du vill skapa valvet för Återställningstjänster. I exemplet nedan heter resursgruppen VMwareDRtoAzurePS och skapas i regionen Östasien.

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

2. Skapa ett återhämtningstjänstvalv. I exemplet nedan heter valvet för återställningstjänster VMwareDRToAzurePs och skapas i regionen Östasien och i resursgruppen som skapades i föregående steg.

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

3. Hämta valvregistreringsnyckeln för valvet. Valvregistreringsnyckeln används för att registrera den lokala konfigurationsservern i det här valvet. Registrering är en del av installationsprocessen för Configuration Server-programvaran.

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

4. Använd den nedladdade registreringsnyckeln för valvet och följ stegen i artiklarna nedan för att slutföra installation och registrering av konfigurationsservern.
   - [Välj dina skyddsmål](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Konfigurera källmiljön](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Ange valvkontext

Ange valvkontexten med cmdleten Set-ASRVaultContext. När du har angett utförs efterföljande Azure Site Recovery-åtgärder i PowerShell-sessionen i kontexten för det valda valvet.

> [!TIP]
> Azure Site Recovery PowerShell-modulen (Az.RecoveryServices-modulen) levereras med lättanvända alias för de flesta cmdlets. Cmdlets i modulen har formen * \<Operation>-**AzRecoveryServicesAsr**\<Object>* och har likvärdiga alias som har formen * \<Operation>-**ASR-objekt**\<>*. Du kan byta ut cmdlet alias för enkel användning.

I exemplet nedan används arkivinformationen från variabeln $vault för att ange arkivkontexten för PowerShell-sessionen.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Som ett alternativ till cmdleten Set-ASRVaultContext kan man också använda cmdleten Import-AzRecoveryServicesAsrVaultSettingsFile för att ställa in valvkontexten. Ange sökvägen där nyckelfilen för valvregistrering finns som parametern -path till cmdleten Import-AzRecoveryServicesAsrVaultSettingsFile. Ett exempel:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Efterföljande avsnitt i den här artikeln förutsätter att arkivkontexten för Azure Site Recovery-åtgärder har angetts.

## <a name="validate-vault-registration"></a>Validera registrering av valv

I det här exemplet har vi följande:

- En konfigurationsserver (**ConfigurationServer**) har registrerats i det här valvet.
- Ytterligare en processserver (**ScaleOut-ProcessServer**) har registrerats *på ConfigurationServer*
- Konton (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) har ställts in på konfigurationsservern. Dessa konton används för att lägga till vCenter-servern, för att identifiera virtuella datorer och för att push-installera programvaran för mobilitetstjänster på Windows- och Linux-servrar som ska replikeras.

1. Registrerade konfigurationsservrar representeras av ett fabric-objekt i Site Recovery. Hämta listan över tygobjekt i valvet och identifiera konfigurationsservern.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
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

2. Identifiera de processservrar som kan användas för att replikera datorer.

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

   Från utdata ovan ***$ProcessServers[0]*** motsvarar *ScaleOut-ProcessServer* och ***$ProcessServers[1]*** motsvarar rollen Process Server på *ConfigurationServer*

3. Identifiera konton som har konfigurerats på konfigurationsservern.

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

   Från utdata ovan ***$AccountHandles[0]*** motsvarar kontot *vCenter_account*, ***$AccountHandles[1]*** till konto *WindowsAccount*och ***$AccountHandles[2]*** till kontot *LinuxAccount*

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

I det här steget skapas två replikeringsprinciper. En princip för att replikera virtuella VMware-datorer till Azure och den andra för att replikera misslyckades över virtuella datorer som körs i Azure tillbaka till den lokala VMware-platsen.

> [!NOTE]
> De flesta Azure Site Recovery-åtgärder körs asynkront. När du initierar en åtgärd skickas ett Azure Site Recovery-jobb och ett jobbspårningsobjekt returneras. Det här jobbspårningsobjektet kan användas för att övervaka åtgärdens status.

1. Skapa en replikeringsprincip med namnet *ReplicationPolicy* för att replikera virtuella VMware-datorer till Azure med de angivna egenskaperna.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

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

2. Skapa en replikeringsprincip som ska användas för återställning efter fel från Azure till den lokala VMware-platsen.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Använd jobbinformationen i *$Job_FailbackPolicySkapera* för att spåra åtgärden till slutförd.

   * Skapa en mappning av skyddsbehållare för att mappa replikeringsprinciper med konfigurationsservern.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

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
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Lägga till en vCenter-server och identifiera virtuella datorer

Lägg till en vCenter-server efter IP-adress eller värdnamn. **Parametern -port** anger porten på vCenter-servern för anslutning till, **-Namnparametern** anger ett eget namn som ska användas för vCenter-servern och parametern **-Account** anger kontoreferensen på konfigurationsservern som ska användas för att identifiera virtuella datorer som hanteras av vCenter-servern.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

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

**Om du vill skriva till hanterad disk använder du [Powershell Az.RecoveryServices-modul 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) och framåt.** Det kräver bara att skapa ett logglagringskonto. Vi rekommenderar att du använder en standardkontotyp och LRS-redundans eftersom den endast används för att lagra tillfälliga loggar. Kontrollera att lagringskontot skapas i samma Azure-region som valvet.

Om du använder en version av Modulen Az.RecoveryServices som är äldre än 2.0.0 använder du följande steg för att skapa lagringskonton. Dessa lagringskonton används senare för att replikera virtuella datorer. Kontrollera att lagringskontona skapas i samma Azure-region som valvet. Du kan hoppa över det här steget om du planerar att använda ett befintligt lagringskonto för replikering.

> [!NOTE]
> När du replikerar lokala virtuella datorer till ett premiumlagringskonto måste du ange ytterligare ett standardlagringskonto (logglagringskonto). Logglagringskontot innehåller replikeringsloggar som ett mellanliggande arkiv tills loggarna kan tillämpas på premiumlagringsmålet.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Virtuella datorer med replikera vmware

Det tar cirka 15-20 minuter för virtuella datorer att upptäckas från vCenter-servern. När ett skyddat objekt har identifierats skapas ett skyddat objekt i Azure Site Recovery för varje upptäckt virtuell dator. I det här steget replikeras tre av de identifierade virtuella datorerna till Azure Storage-konton som skapats i föregående steg.

Du behöver följande information för att skydda en upptäckt virtuell dator:

* Det skyddade objektet som ska replikeras.
* Lagringskontot som den virtuella datorn ska replikeras till (endast om du replikerar till lagringskontot). 
* En logglagring behövs för att skydda virtuella datorer till ett premiumlagringskonto eller till en hanterad disk.
* Processservern som ska användas för replikering. Listan över tillgängliga processservrar har hämtats och sparats i ***variablerna $ProcessServers[0]****(ScaleOut-ProcessServer)* och ***$ProcessServers[1]*** *(ConfigurationServer).*  
* Kontot som ska användas för att push-installera programvaran mobilitetstjänst på maskinerna. Listan över tillgängliga konton har hämtats och lagrats i ***variabeln $AccountHandles.***
* Skyddsbehållarens mappning för replikeringsprincipen som ska användas för replikering.
* Resursgruppen där virtuella datorer måste skapas vid redundans.
* Det virtuella Azure-nätverket och undernätet som det gick över den virtuella datorn ska också anslutas till.

Replikera nu följande virtuella datorer med de inställningar som anges i den här tabellen


|Virtuell dator  |Bearbeta server        |Lagringskonto              |Logga lagringskonto  |Princip           |Konto för installation av mobilitetstjänster|Målgrupp för målresurs  | Rikta in dig på virtuellt nätverk  |Målundernät  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |Ej tillämpligt| logstoragekonto1                 |Replikeringspolicy|LinuxKonto                             |VMwareDRToAzurePs      |ASR-vnet                 |Undernät-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstoragekonto1       |logstoragekonto1   |Replikeringspolicy|WindowsKonto                           |VMwareDRToAzurePs      |ASR-vnet                 |Undernät-1       |   
|CentOSVM2       |ConfigurationServer   |replikeringsöverensatförstoringskonto1| Ej tillämpligt                 |Replikeringspolicy|LinuxKonto                             |VMwareDRToAzurePs      |ASR-vnet                 |Undernät-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

När aktivera replikeringsjobbet har slutförts startas den första replikeringen för de virtuella datorerna. Inledande replikering kan ta en stund beroende på hur mycket data som ska replikeras och den bandbredd som är tillgänglig för replikering. När den första replikeringen är klar flyttas den virtuella datorn till ett skyddat tillstånd. När den virtuella datorn når ett skyddat tillstånd kan du utföra en test redundans för den virtuella datorn, lägga till den i återställningsplaner etc.

Du kan kontrollera replikeringstillståndet och replikeringshälsan för den virtuella datorn med cmdleten Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurera redundansinställningar

Redundansinställningar för skyddade datorer kan uppdateras med cmdleten Set-ASRReplicationProtectedItem. Några av de inställningar som kan uppdateras via denna cmdlet är:
* Namnet på den virtuella datorn som ska skapas vid redundans
* VM-storleken på den virtuella datorn som ska skapas vid redundans
* Virtuella Azure-nätverk och -undernät som nätverkskorten för den virtuella datorn ska anslutas till vid redundans
* Redundans till hanterade diskar
* Använd Azure Hybrid-användningsförmån
* Tilldela en statisk IP-adress från det virtuella målnätverket som ska tilldelas den virtuella datorn vid redundans.

I det här exemplet uppdaterar vi den virtuella datorns storlek för den virtuella datorn som ska skapas vid redundans för den virtuella datorn *Win2K12VM1* och anger att den virtuella datorn använder hanterade diskar vid redundans.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
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

1. Kör en DR-borr (test redundans) enligt följande:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. När test redundansjobbet har slutförts kommer du att märka att en virtuell dator som suffixeras med *"-Test"* (Win2K12VM1-Test i det här fallet) till dess namn skapas i Azure.
3. Du kan nu ansluta till testet misslyckades över virtuell dator och validera test redundansen.
4. Rensa testmissilen med cmdleten Start-ASRTestFailoverCleanupJob. Den här åtgärden tar bort den virtuella datorn som skapats som en del av testundanundanpassningen.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Redundansväxla till Azure

I det här steget växlar vi över den virtuella datorn Win2K12VM1 till en specifik återställningspunkt.

1. Hämta en lista över tillgängliga återställningspunkter som ska användas för redundansen:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. När det har misslyckats över lyckades du genomföra redundansåtgärden och ställa in omvänd replikering från Azure tillbaka till den lokala VMware-platsen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du automatiserar fler uppgifter med azure [site recovery powershell-referensen](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).
