---
title: "Replikera virtuella Hyper-V-datorer i Virtual Machine Manager-moln till en sekundär plats med PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Beskriver hur du replikerar virtuella Hyper-V-datorer i Virtual Machine Manager-moln till en sekundär plats i Virtual Machine Manager med hjälp av PowerShell (Resource Manager)"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: ea4c2ed287619b92dba1b9b966cc0d52e0eb89c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Replikera virtuella Hyper-V-datorer till en sekundär plats med hjälp av PowerShell (Resource Manager)

Den här artikeln visar hur du automatiserar stegen för replikering av Hyper-V virtuella datorer i System Center Virtual Machine Manager-moln till ett moln för Virtual Machine Manager i en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- Granska de [scenariots arkitektur och komponenter](hyper-v-vmm-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-to-sec-site.md) för alla komponenter.
- Kontrollera att Virtual Machine Manager-servrar och Hyper-V-värdar uppfyller [supportkrav](site-recovery-support-matrix-to-sec-site.md).
- Kontrollera att de virtuella datorerna som du vill replikera följa [replikerade datorn support](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning

[Nätverksmappning](hyper-v-vmm-network-mapping.md) mappningar mellan lokala virtuella Datorhanteraren VM-nätverk i käll- och moln. Mappning gör följande:

- Ansluter virtuella datorer till lämpliga mål VM-nätverk efter en redundansväxling. 
- Optimalt placerar Replikdatorerna på mål-Hyper-V-värdservrar. 
- Om du inte konfigurerar nätverksmappning ska replikering VMs inte anslutas till ett Virtuellt datornätverk efter växling vid fel.

Förbered Virtual Machine Manager på följande sätt:

* Kontrollera att du har [logiska nätverk i Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) på de käll- och Virtual Machine Manager-servrarna:

    - Det logiska nätverket på källservern ska associeras med det källmoln där Hyper-V-värdar finns.
    - Det logiska nätverket på målservern ska vara associerat med målmolnet.
* Kontrollera att du har [Virtuella datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på de käll- och Virtual Machine Manager-servrarna. Virtuella datornätverk som ska kopplas till det logiska nätverket på varje plats.
* Ansluta virtuella datorer på Hyper-V-värdar för källa till källnätverket. 

## <a name="prepare-for-powershell"></a>Förbereda för PowerShell

Kontrollera att du har Azure PowerShell redo att sätta igång:

- Om du redan använder PowerShell, uppgradera till version 0.8.10 eller senare. [Lär dig mer](/powershell/azureps-cmdlets-docs) om hur du ställer in PowerShell.
- När du skapar och konfigurerar PowerShell, granska den [tjänsten cmdlets](/powershell/azure/overview).
- Mer information om hur du använder parametervärden indata och utdata i PowerShell den [Kom igång](/powershell/azure/get-started-azureps) guide.

## <a name="set-up-a-subscription"></a>Ställ in en prenumeration
1. Logga in på ditt Azure-konto från PowerShell.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Hämta en lista över dina prenumerationer med ID: N för prenumeration. Observera ID för den prenumeration som du vill skapa Recovery Services-valvet. 

        Get-AzureRmSubscription
3. Ställ in prenumerationen för valvet.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
1. Skapa en resursgrupp i Azure Resource Manager om du inte har någon.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Skapa ett nytt Recovery Services-valv. Spara valvet objektet i en variabel som ska användas senare. 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Du kan hämta valvet objektet när du har skapat med hjälp av cmdleten Get-AzureRMRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Ange valvet-kontext
1. Hämta en befintlig valvet.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Ange valvet kontext.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Installera Site Recovery-providern
1. Skapa en katalog på Virtual Machine Manager-dator genom att köra följande kommando:

       New-Item c:\ASR -type directory
2. Extrahera filerna med hjälp av hämtade provider-installationsfilen.

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
    > Virtual Machine Manager-moln kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server, men replikeringsprinciper är för en viss version av ett operativsystem. Om du har olika värdar som körs på olika operativsystem, kan du skapa separata replikeringsprinciper för varje dator. Om du har fem värdar som kör Windows Server 2012 och tre värdar som körs på Windows Server 2012 R2 kan du till exempel skapa två replikeringsprinciper. Du skapar en för varje typ av operativsystem.

2. Hämta primära skyddsbehållaren (primära Virtual Machine Manager-moln) och återställning skyddsbehållaren (återställning Virtual Machine Manager-moln).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Hämta replikeringsprincip som du skapat med hjälp av det egna namnet.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. Starta associationen för skyddsbehållaren (Virtual Machine Manager-moln) till replikeringsprincipen.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Vänta tills princip association jobbet är slutfört. Använd följande PowerShell-fragment för att kontrollera om jobbet har slutförts:

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

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [övervaka aktiviteten](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurera nätverksmappning
1. Använd detta kommando för att hämta servrar för det aktuella valvet. Kommandot lagrar Site Recovery-servrar i variabeln $Servers matris.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Kör detta kommando för att hämta nätverken för Virtual Machine Manager källservern och målservern för Virtual Machine Manager.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Virtual Machine Manager källservern kan vara det första eller andra i matrisen server. Kontrollera Virtual Machine Manager-servernamn och hämta nätverken på lämpligt sätt.


3. Denna cmdlet skapar en mappning mellan det primära nätverket och återställningsnätverket. Den anger det primära nätverket som det första elementet i $PrimaryNetworks. Den anger återställningsnätverket som det första elementet i $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Aktivera skydd för virtuella datorer
När servrar, moln och nätverk har konfigurerats korrekt och att aktivera skydd för virtuella datorer i molnet.

1. Kör följande kommando för att hämta skyddsbehållaren för att aktivera skydd:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Hämta entiteten skydd (VM), enligt följande:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Aktivera replikering för den virtuella datorn.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Köra ett redundanstest

Testa distributionen genom att köra ett redundanstest för en enskild virtuell dator. Du kan också skapa en återställningsplan som innehåller flera virtuella datorer och köra ett redundanstest för planen. Ett redundanstest simulerar redundans- och återställningsmekanismen i ett isolerat nätverk.

1. Hämta den virtuella datorn som virtuella datorer växlar.

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

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [övervaka aktiviteten](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Köra planerade och oplanerade redundansväxlingar

1. Utför en planerad redundans.

   För en enskild virtuell dator:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   För en återställningsplan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Genomför en oplanerad redundans.

   För en enskild virtuell dator:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   För en återställningsplan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Övervakaraktivitet
Använd följande kommandon för att övervaka aktivitet för växling vid fel. Vänta tills bearbetningen till Slutför mellan jobb.

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

[Lär dig mer](/powershell/module/azurerm.recoveryservices.backup/#recovery) om Site Recovery med Resource Manager PowerShell-cmdlets.
