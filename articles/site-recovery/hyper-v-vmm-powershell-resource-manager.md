---
title: Replikera Hyper-V-datorer i Virtual Machine Manager-moln till en sekundär plats med PowerShell (Azure Resource Manager) | Microsoft Docs
description: Beskriver hur du replikerar virtuella Hyper-V-datorer i Virtual Machine Manager-moln till en sekundär plats för Virtual Machine Manager med hjälp av PowerShell (Resource Manager)
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 896b3cb3c40673dfbc7269cab1d37790f78912b0
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041364"
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Replikera Hyper-V-datorer till en sekundär plats med hjälp av PowerShell (Resource Manager)

Den här artikeln visar hur du automatiserar stegen för replikering av Hyper-V-datorer i System Center Virtual Machine Manager-moln till ett moln för Virtual Machine Manager i en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- Granska den [scenariots arkitektur och komponenter](hyper-v-vmm-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-to-sec-site.md) för alla komponenter.
- Kontrollera att Virtual Machine Manager-servrar och Hyper-V-värdar är kompatibla med [supportkrav](site-recovery-support-matrix-to-sec-site.md).
- Kontrollera att de virtuella datorerna som du vill replikera uppfylla [replikerade datorn support](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning

[Nätverksmappning](hyper-v-vmm-network-mapping.md) mappningar mellan en lokal Virtual Machine Manager-VM-nätverk i käll- och målmolnen. Mappningen gör följande:

- Ansluter virtuella datorer till lämplig mål VM-nätverk efter redundansväxling. 
- Optimalt placerar virtuella replikdatorerna på mål-Hyper-V-värdservrar. 
- Om du inte konfigurerar nätverksmappning ska replikering av VMs inte anslutas till ett Virtuellt datornätverk efter redundansväxling.

Förbered Virtual Machine Manager på följande sätt:

* Kontrollera att du har [logiska nätverk i Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) på käll- och Virtual Machine Manager-servrar:

    - Det logiska nätverket på källservern ska associeras med det källmoln som Hyper-V-värdarna finns.
    - Det logiska nätverket på målservern ska associeras med målmolnet.
* Kontrollera att du har [Virtuella datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på käll- och Virtual Machine Manager-servrarna. Virtuella datornätverk bör vara kopplat till det logiska nätverket på varje plats.
* Ansluta virtuella datorer på Hyper-V-värdar för källan till VM-källnätverket. 

## <a name="prepare-for-powershell"></a>Förbereda för PowerShell

Kontrollera att du har Azure PowerShell som är redo att sätta igång:

- Om du redan använder PowerShell, uppgradera till version 0.8.10 eller senare. [Läs mer](/powershell/azureps-cmdlets-docs) om hur du ställer in PowerShell.
- När du ställer in och konfigurera PowerShell kan du granska den [tjänsten cmdletar](/powershell/azure/overview).
- Mer information om hur du använder parametervärden indata och utdata i PowerShell på [börjar](/powershell/azure/get-started-azureps) guide.

## <a name="set-up-a-subscription"></a>Konfigurera en prenumeration
1. Logga in på ditt Azure-konto från PowerShell.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzureRmAccount #-Credential $Cred
2. Hämta en lista över dina prenumerationer, med prenumerations-ID. Observera ID för prenumerationen som du vill skapa Recovery Services-valvet. 

        Get-AzureRmSubscription
3. Ställ in prenumerationen för valvet.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv
1. Skapa en resursgrupp i Azure Resource Manager om du inte har något.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Skapa ett nytt Recovery Services-valv. Spara valvobjekt i en variabel som ska användas senare. 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Du kan hämta objektet valv när du har skapat med hjälp av cmdleten Get-AzureRMRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Ange valvets sammanhang
1. Hämta ett befintligt valv.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Ange valvets sammanhang.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Installera Site Recovery-providern
1. Skapa en katalog på Virtual Machine Manager-datorn genom att köra följande kommando:

       New-Item c:\ASR -type directory
2. Extrahera filerna med hjälp av installationsfilen för hämtade providern.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installera providern och vänta på att installationen ska slutföras.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Registrera servern i valvet.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Skapa och koppla en replikeringsprincip
1. Skapa en replikeringsprincip i det här fallet för Hyper-V 2012 R2 på följande sätt:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Virtual Machine Manager-molnet kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server, men replikeringsprincipen är för en specifik version av ett operativsystem. Om du har olika värdar som körs på olika operativsystem kan du skapa separata replikeringsprinciper för varje system. Om du har fem värdar som kör Windows Server 2012 och tre värdar som körs på Windows Server 2012 R2 kan du till exempel skapa två replikeringsprinciper. Du skapar en för varje typ av operativsystem.

2. Hämta primära skyddsbehållaren (primära Virtual Machine Manager-moln) och återställning skyddsbehållaren (återställning Virtual Machine Manager-moln).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Hämta den replikeringsprincip som du skapade med hjälp av det egna namnet.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. Börja associationen mellan skyddsbehållaren (Virtual Machine Manager-moln) med replikeringsprincipen.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Vänta tills principen association jobbet är slutfört. Om du vill kontrollera om jobbet har slutförts, använder du följande PowerShell-kodavsnitt:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. När jobbet har slutförts bearbetning, kör du följande kommando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [övervaka](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurera nätverksmappning
1. Använd det här kommandot för att hämta servrar för det aktuella valvet. Kommandot lagrar Site Recovery-servrar i $Servers matrisvariabel.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Kör detta kommando för att hämta nätverk för Virtual Machine Manager-källservern och målservern för Virtual Machine Manager.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Virtual Machine Manager källservern kan vara det första eller andra i server-matris. Kontrollera Virtual Machine Manager-servernamn och hämta nätverk på rätt sätt.


3. Denna cmdlet skapar en mappning mellan det primära nätverket och det recovery-nätverket. Den anger det primära nätverket som det första elementet i $PrimaryNetworks. Den anger recovery-nätverket som det första elementet i $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Aktivera skydd för virtuella datorer
När servrar, moln och nätverk har konfigurerats korrekt och att aktivera skydd för virtuella datorer i molnet.

1. Om du vill aktivera skydd, kör du följande kommando för att hämta skyddsbehållaren:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Hämta skyddad entitet (VM), enligt följande:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Aktivera replikering för den virtuella datorn.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Köra ett redundanstest

Kör ett redundanstest för en enskild virtuell dator för att testa distributionen. Du kan också skapa en återställningsplan som innehåller flera virtuella datorer och köra ett redundanstest för planen. Ett redundanstest simulerar redundans- och återställningsmekanismen i ett isolerat nätverk.

1. Hämta den virtuella datorn där virtuella datorer att redundansväxla.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2. Utför ett redundanstest.

   För en enskild virtuell dator:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   För en återställningsplan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [övervaka](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Kör planerad och oplanerad redundans

1. Utför en planerad redundansväxling.

   För en enskild virtuell dator:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   För en återställningsplan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Utför en oplanerad redundans.

   För en enskild virtuell dator:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   För en återställningsplan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Övervakaraktivitet
Använd följande kommandon för att övervaka redundans. Vänta tills bearbetningen till Slutför mellan jobb.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Nästa steg

[Läs mer](/powershell/module/azurerm.recoveryservices.backup/#recovery) om Site Recovery med PowerShell och Resource Manager-cmdletar.
