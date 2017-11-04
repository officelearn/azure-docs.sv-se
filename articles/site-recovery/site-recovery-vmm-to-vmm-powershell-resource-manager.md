---
title: "Replikera virtuella Hyper-V-datorer i VMM till en sekundär plats med PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Beskriver hur du distribuerar Azure Site Recovery för att samordna replikering, redundans och återställning av Hyper-V virtuella datorer i VMM-moln till en sekundär VMM-plats med hjälp av PowerShell (Resource Manager)"
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: sutalasi
ms.openlocfilehash: c978c2e31e775f56824d765491f6d7b73648b8ae
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär VMM-plats med hjälp av PowerShell (Resource Manager)

Den här artikeln visar hur du använder PowerShell för att automatisera vanliga uppgifter när du ställer in Azure Site Recovery replikera Hyper-V virtuella datorer i System Center VMM-moln till System Center VMM-moln på en sekundär plats.



## <a name="on-premises-prerequisites"></a>Krav för det lokala systemet
Här är vad du behöver i de primära och sekundära lokala platserna distribuera det här scenariot:

| **Förutsättningar** | **Detaljer** |
| --- | --- |
| **VMM** |Vi rekommenderar att du distribuerar en VMM-server på den primära platsen och en VMM-servern på den sekundära platsen.<br/><br/> Du kan också replikera mellan moln på en VMM-server. Om du vill göra detta behöver du minst två moln som konfigurerats på VMM-servern.<br/><br/> VMM-servrar måste köra minst System Center 2012 SP1 med de senaste uppdateringarna.<br/><br/> Varje VMM-server måste ha en eller flera moln har konfigurerats och alla moln måste ha Hyper-V kapacitetsprofilen ange. <br/><br/>Molnen måste innehålla en eller flera VMM-värdgrupper. VMM-servrar ska ha tillgång till internet. |
| **Hyper-V** |Hyper-V-servrar måste köra minst Windows Server 2012 med Hyper-V-rollen och har installerat de senaste uppdateringarna.<br/><br/> En Hyper-V-server måste innehålla en eller flera virtuella datorer.<br/><br/>  Hyper-V-värdservrar ska finnas i värdgrupper i de primära och sekundära VMM-molnen.<br/><br/> Om du kör Hyper-V i ett kluster i Windows Server 2012 R2 bör du installera [uppdatera 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Om du kör Hyper-V i ett kluster på Windows Server 2012 Observera att klusterutjämning skapas inte automatiskt om du har en statisk IP-adress-kluster. Du måste konfigurera klusterutjämningen manuellt. [Läs mer](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Leverantör** |Under distributionen av Site Recovery installerar du Azure Site Recovery-providern på VMM-servrar. Providern kommunicerar med Site Recovery via HTTPS 443 för att samordna replikeringen. Datareplikering sker mellan primära och sekundära Hyper-V-servrar över LAN- eller en VPN-anslutning.<br/><br/> Providern som körs på VMM-servern behöver åtkomst till dessa webbadresser: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.windows.net; *. backup.windowsazure.com; *. blob.core.windows.net; *. store.core.windows.net.<br/><br/> Dessutom tillåter brandväggen kommunikation från VMM-servrar för att den [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) och tillåta protokollet HTTPS (443). |

### <a name="network-mapping-prerequisites"></a>Krav för nätverksmappning
Mappar nätverksmappningen mellan VMM VM-nätverk på de primära och sekundära VMM-servrarna att göra följande:

* Placera optimalt Replikdatorerna på sekundära Hyper-V-värdar efter växling vid fel.
* Ansluta Replikdatorerna till lämpliga VM-nätverk.
* Om du inte konfigurerar nätverket mappning replik virtuella datorer inte ansluts till något nätverk efter redundansväxling.
* Om du vill konfigurera nätverk är mappning under Site Recovery distributionen här vad du behöver:

  * Se till att de virtuella datorerna på Hyper-V-källvärdservern är anslutna till ett VM-nätverk i VMM. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.
  * Kontrollera att det sekundära moln som du vill använda för återställning har ett motsvarande Virtuellt datornätverk. Det Virtuella datornätverket ska kopplas till ett logiskt nätverk som är associerad med det sekundära molnet.

Lär dig mer om hur du konfigurerar VMM-nätverk i den nedan artiklar

* [Konfigurera logiska nätverk i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Konfigurera Virtuella datornätverk och gateways i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)


### <a name="powershell-prerequisites"></a>PowerShell-krav
Kontrollera att du har Azure PowerShell gå vidare. Om du redan använder PowerShell, måste du uppgradera till version 0.8.10 eller senare. Information om hur du konfigurerar PowerShell finns i [Guide för att installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs). När du har skapat och konfigurerat PowerShell, kan du visa alla tillgängliga cmdlets för tjänsten [här](/powershell/azure/overview).

Mer information om tips som kan du använda cmdlet: ar, till exempel hur parametervärden indata och utdata hanteras vanligtvis av Azure PowerShell finns i [Guide för att komma igång med Azure-Cmdlets](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Steg 1: Ställ in prenumerationen
1. Från Azure powershell, logga in på ditt Azure-konto: använda följande cmdlets

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Hämta en lista över dina prenumerationer. Detta visar också en lista över subscriptionIDs för var och en av prenumerationerna. Anteckna prenumerations-ID för prenumerationen som du vill skapa recovery services-valvet    

        Get-AzureRmSubscription
3. Ange den prenumeration som recovery services-ventilen är skapas av nämna prenumerations-ID

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Steg 2: Skapa ett Recovery Services-valv
1. Skapa en resursgrupp i Azure Resource Manager om du inte redan har en

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Skapa ett nytt Recovery Services-valv och spara objektet Recovery Services-valv i en variabel (kommer att användas senare). Du kan också hämta post-skapa valv objektet med hjälp av cmdleten Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Steg 3: Ange kontext för Recovery Services-valvet
1. Om du har ett valv som redan har skapats kan du köra den nedan att hämta valvet.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Ange valvet-kontexten genom att köra den nedan kommando.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Steg 4: Installera Azure Site Recovery-providern
1. Skapa en katalog på VMM-datorn genom att köra följande kommando:

       New-Item c:\ASR -type directory
2. Extrahera filerna med hjälp av hämtade providern genom att köra följande kommando

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installera providern med hjälp av följande kommandon:

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

   Vänta på att installationen ska slutföras.
4. Registrera servern i valvet med följande kommando:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-and-associate-a-replication-policy"></a>Steg 5: Skapa och koppla en replikeringsprincip
1. Skapa en replikeringsprincip för Hyper-V 2012 R2 genom att köra följande kommando:

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
    > VMM-molnet kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server (som anges i Hyper-V-krav), men är OS-version som är specifika för replikeringsprincipen. Om du har olika värdar som körs på olika operativsystemversioner skapa separata replikeringsprinciper för varje typ av OS-version. Om du har fem värdar som kör Windows 2012 för servrar och tre på Windows Server 2012 R2 kan du till exempel skapa två replikeringsprinciper – en för varje typ av versioner av operativsystemet.

1. Hämta primära skyddsbehållaren (primära VMM-moln) och återställning skyddsbehållaren (återställning VMM-moln) genom att köra följande kommandon:

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Hämta en princip som du skapade i steg 1 med eget namn för principen

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Starta associationen för skyddsbehållaren (VMM-moln) med replikeringsprincipen:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Vänta tills princip association jobbet ska slutföras. Du kan kontrollera om jobbet har slutförts med följande kodavsnitt i PowerShell.

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

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [Övervakaraktiviteten](#monitor).

## <a name="step-6-configure-network-mapping"></a>Steg 6: Konfigurera nätverksmappning
1. Det första kommandot hämtar servrar för det aktuella Azure Site Recovery-valvet. Kommandot lagrar Microsoft Azure Site Recovery-servrar i variabeln $Servers matris.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Kommandot Hämta Site Recovery-nätverk för VMM-källservern och målservern för VMM.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > VMM-källservern kan vara det första och det andra i matrisen servrar. Kontrollera namnen på VMM-servrar och hämta nätverken på rätt sätt


1. Den slutliga cmdleten skapar en mappning mellan det primära nätverket och återställningsnätverket. Cmdlet anger det primära nätverket som det första elementet i $PrimaryNetworks och återställning nätverk som det första elementet i $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>Steg 7: Konfigurera lagring-mappning
1. Den nedan kommando hämtar listan över lagringsklassificeringar i $storageclassifications variabel.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. Följande kommandon hämta källa klassificeringen i $SourceClassificaion variabeln och mål klassificeringen i $TargetClassification variabeln.

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > Klassificeringarna källa och mål kan vara alla element i matrisen. Referera till utdata från den nedan kommando för att räkna index för käll- och klassificeringar på $storageclassifications matris.

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object - egenskapen FriendlyName, Id | Formatera tabell


1. Den nedan cmdlet skapar en mappning mellan klassificeringen källa och mål-klassificering.

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>Steg 8: Aktivera skydd för virtuella datorer
När servrar, moln och nätverk har konfigurerats korrekt och kan du aktivera skydd för virtuella datorer i molnet.

1. Kör följande kommando för att hämta skyddsbehållaren för att aktivera skydd:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Hämta skyddad entitet (VM) genom att köra följande kommando:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Aktivera replikering för den virtuella datorn genom att köra följande kommando:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>Testa distributionen
Om du vill testa distributionen kan du köra ett redundanstest för en enskild virtuell dator eller skapa en återställningsplan som består av flera virtuella datorer och köra ett redundanstest för planen. Ett redundanstest simulerar redundans- och återställningsmekanismen i ett isolerat nätverk.

> [!NOTE]
> Du kan skapa en återställningsplan för ditt program i Azure-portalen.
>
>

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [Övervakaraktiviteten](#monitor).

### <a name="run-a-test-failover"></a>Köra ett redundanstest
1. Kör den nedan cmdlets för att hämta dina virtuella datorer till för det Virtuella datornätverket som du vill testa redundans.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Utför ett redundanstest av en virtuell dator genom att göra följande:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. Utför ett redundanstest av en återställningsplan genom att göra följande:

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>Kör en planerad redundans
1. Utför en planerad redundansväxling av en virtuell dator genom att göra följande:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. Utför en planerad redundansväxling av en återställningsplan genom att göra följande:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Kör en oplanerad redundans
1. Utföra en oplanerad redundans för en virtuell dator genom att göra följande:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. utföra en oplanerad redundans för en återställningsplan genom att göra följande:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a>Övervakaraktivitet
Du kan använda följande kommandon för att övervaka aktiviteten. Observera att du har ska vänta mellan jobb för bearbetning till slut.

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
[Läs mer](/powershell/module/azurerm.recoveryservices.backup/#recovery) om Azure Site Recovery med Azure Resource Manager PowerShell-cmdlets.
