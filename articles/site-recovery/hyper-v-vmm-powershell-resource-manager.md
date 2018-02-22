---
title: "Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats med PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Beskriver hur du replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär VMM-plats med hjälp av PowerShell (Resource Manager)"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: a5e36546494223b20844303f3f76782746658411
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-using-powershell-resource-manager"></a>Replikera virtuella Hyper-V-datorer till en sekundär plats med hjälp av PowerShell (Resource Manager)

Den här artikeln lär du dig för att automatisera steg för replikering av Hyper-V virtuella datorer i System Center Virtual Machine Manager (VMM)-moln till en VMM-moln på en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- Granska de [scenariots arkitektur och komponenter](hyper-v-vmm-architecture.md).
- Granska de [supportkrav](site-recovery-support-matrix-to-sec-site.md) för alla komponenter.
- Kontrollera att VMM-servrar och Hyper-V-värdar uppfyller [supportkrav](site-recovery-support-matrix-to-sec-site.md).
- Kontrollera att virtuella datorer du vill replikera följa [replikeras stöd för datorer](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)


## <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning

[Nätverksmappning](hyper-v-vmm-network-mapping.md) mappningar mellan lokala VMM VM-nätverk i käll- och moln. Mappning gör följande:

- Ansluter virtuella datorer till lämpliga mål VM-nätverk efter en redundansväxling. 
- Optimalt placerar Replikdatorerna på mål-Hyper-V-värdservrar. 
- Om du inte konfigurerar nätverksmappning ska replikering VMs inte anslutas till ett Virtuellt datornätverk efter växling vid fel.

Förbered VMM på följande sätt:

1. Kontrollera att du har [logiska nätverk i VMM](https://docs.microsoft.com/system-center/vmm/network-logical) på käll- och VMM-servrarna.
    - Det logiska nätverket på källservern ska associeras med det källmoln där Hyper-V-värdar finns.
    - Det logiska nätverket på målservern ska vara associerat med målmolnet.
1. Kontrollera att du har [Virtuella datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på käll- och VMM-servrarna. Virtuella datornätverk som ska kopplas till det logiska nätverket på varje plats.
2. Ansluta virtuella datorer på Hyper-V-värdar för källa till källnätverket. 

## <a name="prepare-for-powershell"></a>Förbereda för PowerShell

Kontrollera att du har Azure PowerShell gå vidare.

- Om du redan använder PowerShell, måste du uppgradera till version 0.8.10 eller senare.  [Lär dig mer](/powershell/azureps-cmdlets-docs) om hur du konfigurerar PowerShell.
- När du har skapat och konfigurerat PowerShell, granska den [tjänsten cmdlets](/powershell/azure/overview).
- Mer information om hur du använder parametervärden indata och utdata i Azure PowerShell den [komma igång](/powershell/azure/get-started-azureps) guide.

## <a name="set-up-a-subscription"></a>Ställ in en prenumeration
1. Logga in på ditt Azure-konto från Azure PowerShell:

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Hämta en lista över dina prenumerationer med ID: N för prenumeration. Notera ID för den prenumeration som du vill skapa återställningstjänsten valvet.   

        Get-AzureRmSubscription
3. Ställ in prenumerationen för valvet:

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
1. Skapa en resursgrupp i Azure Resource Manager om du inte har någon.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Skapa ett nytt Recovery Services-valv och spara objektet valv i en variabel som ska användas senare: 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Du kan hämta valvet objektet när du har skapat, med hjälp av cmdleten Get-AzureRMRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Ange valvet-kontext
1. Hämta ett befintligt valv:

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Ange valvet kontext:

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-azure-site-recovery-provider"></a>Installera Azure Site Recovery-providern
1. Skapa en katalog på VMM-datorn genom att köra följande kommando:

       New-Item c:\ASR -type directory
2. Extrahera filerna med hjälp av nedladdade installationsfilen för providern: pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installera providern och vänta på att installationen ska slutföras:

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

4. Registrera servern i valvet:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Skapa och koppla en replikeringsprincip
1. Skapa en replikeringsprincip (i det här fallet för Hyper-V 2012 R2) enligt följande:

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
    > VMM-molnet kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server, men replikeringsprinciper är för en viss version av ett operativsystem. Om du har olika värdar som körs på olika operativsystem, skapa separata replikeringsprinciper för varje dator. Om du har fem värdar som kör Windows 2012 för servrar och tre på Windows Server 2012 R2 kan du till exempel skapa två replikeringsprinciper – en för varje typ av operativsystem.

2. Hämta primära skyddsbehållaren (primära VMM c) och återställning skyddsbehållaren (återställning VMM-moln):

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Hämta replikeringsprincip som du skapade med eget namn:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Starta associationen för skyddsbehållaren (VMM-moln) med replikeringsprincipen:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Vänta tills princip association jobbet ska slutföras. Du kan kontrollera om jobbet har slutförts med följande kodavsnitt i PowerShell:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   När jobbet har slutförts bearbetning, kör du följande kommando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [övervaka aktiviteten](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurera nätverksmappning
1. Använd detta kommando för att hämta servrar för det aktuella valvet. Kommandot lagrar Azure Site Recovery-servrar i variabeln $Servers matris.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Att köra detta kommando retrievet nätverk för VMM-källservern och målservern för VMM.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > VMM-källservern kan vara det första eller andra i matrisen servrar. Kontrollera att VMM-servernamn och hämta nätverken på lämpligt sätt.


3. Denna cmdlet skapar en mappning mellan det primära nätverket och återställningsnätverket. Det anger primära nätverket som det första elementet i $PrimaryNetworks och återställningsnätverket som det första elementet i $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Aktivera skydd för virtuella datorer
När servrar, moln och nätverk har konfigurerats korrekt och kan du aktivera skydd för virtuella datorer i molnet.

1. Kör följande kommando för att hämta skyddsbehållaren för att aktivera skydd:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Hämta entiteten skydd (VM) på följande sätt:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Aktivera replikering för den virtuella datorn:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Köra ett redundanstest

Att testa distributionen av du kan köra ett redundanstest för en enskild virtuell dator eller skapa en återställningsplan som innehåller flera virtuella datorer, och och köra ett redundanstest för planen. Ett redundanstest simulerar redundans- och återställningsmekanismen i ett isolerat nätverk.

1. Hämta den virtuella datorn som virtuella datorer växlar:

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Utför ett redundanstest på följande sätt:
    - För en enda virtuell dator

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
    - För en återställningsplan:

        $recoveryplanname = ”test-återställningsplanen”

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [övervaka aktiviteten](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Köra planerade och oplanerade redundansväxlingar

1. Utför en planerad redundans på följande sätt:
    -  För en enskild virtuell dator:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
    - För en återställningsplan

        $recoveryplanname = ”test-återställningsplanen”

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan
2. Genomför en oplanerad redundans på följande sätt:
    - För en enda virtuell dator
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

    - För en återställningsplan:

        $recoveryplanname = ”test-återställningsplanen”

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Övervakaraktivitet
Du kan använda följande kommandon för att övervaka failver aktivitet. Observera att du har ska vänta mellan jobb för bearbetning till slut.

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

[Lär dig mer](/powershell/module/azurerm.recoveryservices.backup/#recovery) om Site Recovery med Azure Resource Manager PowerShell-cmdlets.
