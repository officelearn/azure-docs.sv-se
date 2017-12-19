---
title: Replikera virtuella Hyper-V-datorer i VMM-moln med Azure Site Recovery och PowerShell (Resource Manager) | Microsoft Docs
description: Replikera virtuella Hyper-V-datorer i VMM-moln med Azure Site Recovery och PowerShell
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: rajanaki
ms.openlocfilehash: cc832d06611c10901d4370dc7467f0b681d89cbd
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Replikera virtuella Hyper-V-datorer i VMM-moln till Azure med PowerShell och Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Klassisk portal](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Klassisk](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Översikt
Azure Site Recovery bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi genom att samordna replikering, redundans och återställning av virtuella datorer i ett antal distributionsscenarier. En fullständig lista över distributionen scenarier finns i [översikt över Azure Site Recovery](site-recovery-overview.md).

Den här artikeln visar hur du använder PowerShell för att automatisera vanliga uppgifter som du behöver utföra när du ställer in Azure Site Recovery replikera Hyper-V virtuella datorer i System Center VMM-moln till Azure-lagring.

Artikeln innehåller kraven för scenariot och visar

* Hur du ställer in ett Recovery Services-valv
* Installera Azure Site Recovery-providern på VMM-källservern
* Registrera servern i valvet, lägga till ett Azure storage-konto
* Installera Azure Recovery Services-agenten på Hyper-V-värdservrar
* Konfigurera skyddsinställningar för VMM-moln som tillämpas på alla skyddade virtuella datorer
* Aktivera skyddet för de virtuella datorerna.
* Testa failover att kontrollera att allt fungerar som förväntat.

Om du stöter på problem med att installera det här scenariot, publicera dina frågor på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker Resource Manager-distributionsmodellen.
>
>

## <a name="before-you-start"></a>Innan du börjar
Kontrollera att du har dessa krav är uppfyllda:

### <a name="azure-prerequisites"></a>Krav för Azure
* Du behöver ett [Microsoft Azure](https://azure.microsoft.com/)-konto. Om du inte har någon, börja med en [kostnadsfritt konto](https://azure.microsoft.com/free). Dessutom kan du läsa om den [priser för Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Du behöver en CSP-prenumeration om du provar ut replikering till en CSP prenumeration scenario. Mer information om CSP-programmet i [hur de registrerar i CSP-programmet](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Du behöver ett Azure v2-lagringskonto (Resource Manager) för lagring av data som replikeras till Azure. Kontot måste geo-replikering aktiverat. Det måste finnas i samma region som Azure Site Recovery-tjänsten och vara kopplade till samma prenumeration och CSP-prenumeration. Mer information om hur du konfigurerar Azure storage finns i [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md) referens.
* Du måste se till att virtuella datorer som du vill skydda uppfyller de [förutsättningar för Azure virtuell dator](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> För närvarande är endast VM nivån åtgärder möjlig via Powershell. Stöd för nivån återställningsåtgärder för planen kommer att göras snart.  Nu är du begränsad till utför misslyckas FELNIVÅ endast på en ”skyddad VM' kornighet och inte på en återställning planera nivå.
>
>

### <a name="vmm-prerequisites"></a>VMM – krav
* Du måste VMM-servern som körs på System Center 2012 R2.
* Någon VMM-server som innehåller virtuella datorer som du vill skydda måste köra Azure Site Recovery-providern. Detta installeras under distributionen av Azure Site Recovery.
* Du behöver minst ett moln på VMM-servern som du vill skydda. Molnet bör innehålla:
  * En eller flera VMM-värdgrupper.
  * En eller flera Hyper-V-värdservrar eller Hyper-V-kluster i varje värdgrupp.
  * En eller flera virtuella datorer på källservern Hyper-V.
* Lär dig mer om hur du konfigurerar VMM-moln:
  * Läs mer om privata VMM-moln i [vad är nytt i privata moln med VMM för System Center 2012 R2](http://go.microsoft.com/fwlink/?LinkId=324952) och i [VMM 2012 och moln](http://go.microsoft.com/fwlink/?LinkId=324956).
  * Lär dig mer om [konfigurera VMM molnet fabric](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * När ditt moln fabric-element är på plats Lär dig mer om att skapa privata moln i [skapa ett privat moln i VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) och i [genomgång: skapa privata moln med System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Hyper-V-krav
* De Hyper-V-värdservrarna måste köra minst **Windows Server 2012** med Hyper-V-rollen eller **Microsoft Hyper-V Server 2012** och har installerat de senaste uppdateringarna.
* Om du kör Hyper-V i ett kluster bör du vara medveten om att klusterutjämning inte skapas automatiskt om du har ett statiskt IP-adressbaserat kluster. Du måste konfigurera klusterutjämningen manuellt. För
* Instruktioner finns i [hur du konfigurerar Hyper-V Replica Broker](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
* Alla Hyper-V-värdserver eller kluster som du vill hantera skydd måste ingå i ett VMM-moln.

### <a name="network-mapping-prerequisites"></a>Krav för nätverksmappning
När du skyddar virtuella datorer i Azure, nätverksmappningen som virtuella datornätverk på VMM-källservern och Azure-målnätverken för att aktivera följande:

* Alla datorer som redundansväxlar i samma nätverk kan ansluta till varandra, oavsett vilken återställningsplan de finns i.
* Om en nätverksgateway har konfigurerats i Azure-målnätverket kan virtuella datorer ansluta till andra lokala virtuella datorer.
* Om du inte konfigurerar nätverksmappning kan kommer endast virtuella datorer som växlar över i samma återställningsplan att kunna ansluta till varandra efter en redundansväxling till Azure.

Om du vill distribuera nätverksmappning behöver du följande:

* De virtuella datorerna som du vill skydda på VMM-källservern måste vara anslutna till ett virtuellt datornätverk. Nätverket ska kopplas till ett logiskt nätverk som är associerat med molnet.
* Ett Azure-nätverk som replikerade virtuella datorer kan ansluta till efter redundansväxling. Du väljer det här nätverket vid tidpunkten för redundansväxling. Nätverket måste finnas i samma region som din Azure Site Recovery-prenumeration.

Mer information om nätverksmappning i

* [Konfigurera logiska nätverk i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Konfigurera Virtuella datornätverk och gateways i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [Konfigurera och övervaka virtuella nätverk i Azure](https://azure.microsoft.com/documentation/services/virtual-network/)

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
2. Skapa ett nytt Recovery Services-valv och spara det ASR valv objektet i variabeln (kommer att användas senare). Du kan också hämta ASR valvet objektet post skapas med hjälp av cmdleten Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Steg 3: Ange Återställningstjänstvalvet-kontext

Ange valvet-kontexten genom att köra den nedan kommando.

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

## <a name="step-5-create-an-azure-storage-account"></a>Steg 5: Skapa ett Azure storage-konto

Om du inte har ett Azure storage-konto, skapa ett konto med geo-replikering är aktiverat i samma geo som valvet genom att köra följande kommando:

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Observera att lagringskontot måste finnas i samma region som Azure Site Recovery-tjänsten och vara associerat med samma prenumeration.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Steg 6: Installera Azure Recovery Services-agenten
1. Hämta Azure Recovery Services-agenten på [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) och installera det på varje Hyper-V-värdserver i VMM-moln som du vill skydda.
2. Kör följande kommando på alla VMM-värdar:

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Steg 7: Konfigurera moln skyddsinställningarna
1. Skapa en replikeringsprincip till Azure genom att köra följande kommando:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. Hämta en skyddsbehållaren genom att köra följande kommandon:

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Hämta information om principen till en variabel med det jobb som har skapats och nämna eget principnamnet:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Starta associering av skyddsbehållaren med replikeringsprincipen:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. När jobbet har slutförts, kör du följande kommando:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. När jobbet har slutförts bearbetning, kör du följande kommando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [Övervakaraktiviteten](#monitor).

## <a name="step-8-configure-network-mapping"></a>Steg 8: Konfigurera nätverksmappning
Innan du börjar använda nätverksmappning kontrollerar du att de virtuella datorerna på VMM-källservern är anslutna till ett virtuellt datornätverk. Dessutom kan skapa en eller flera virtuella Azure-nätverk.

Mer information om hur du skapar ett virtuellt nätverk med Azure Resource Manager och PowerShell, i [skapa ett virtuellt nätverk med en plats-till-plats VPN-anslutning med Azure Resource Manager och PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Observera att flera virtuella datornätverk kan mappas till ett enda Azure-nätverk. Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts den replikerade virtuella datorn till det målundernätverket efter redundansväxling. Om det inte finns något målundernät med ett matchande namn, ansluts den virtuella datorn till det första undernätet i nätverket.

1. Det första kommandot hämtar servrar för det aktuella Azure Site Recovery-valvet. Kommandot lagrar Microsoft Azure Site Recovery-servrar i variabeln $Servers matris.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Det andra kommandot hämtar site recovery nätverket för den första servern i matrisen $Servers. Kommandot lagrar nätverken i variabeln $Networks.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. Tredje kommandot hämtar virtuella Azure-nätverk och värdet i variabeln $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. Den slutliga cmdleten skapar en mappning mellan det primära nätverket och virtuell dator i Azure-nätverk. Cmdlet anger det primära nätverket som det första elementet i $Networks. Cmdlet anger ett virtuellt datornätverk som det första elementet i $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>Steg 9: Aktivera skydd för virtuella datorer
När servrar, moln och nätverk har konfigurerats korrekt och kan du aktivera skydd för virtuella datorer i molnet.

 Observera följande:

* Virtuella datorer måste uppfylla kraven för Azure. Kontrollera dessa i [krav och Support](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) i Planeringsguiden.
* Diskegenskaper måste anges för den virtuella datorn för att aktivera skydd, operativsystem och operativsystem. Du kan ange egenskapen när du skapar en virtuell dator i VMM med hjälp av en mall för virtuella datorer. Du kan också ange dessa egenskaper för befintliga virtuella datorer på flikarna **Allmänt** och **Maskinvarukonfiguration** i egenskaperna för de virtuella datorerna. Om du inte anger dessa egenskaper i VMM kan du konfigurera dem på Azure Site Recovery-portalen.

1. Kör följande kommando för att hämta skyddsbehållaren för att aktivera skydd:

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Hämta skyddad entitet (VM) genom att köra följande kommando:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. Aktivera DR för den virtuella datorn genom att köra följande kommando:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>Testa distributionen
Om du vill testa distributionen av du kör en testa redundansväxling för en enskild virtuell dator eller skapa en återställningsplan som består av flera virtuella datorer och kör ett test failover för planen. Test av redundansväxling simulerar din failover- och återställningsmekanismen i ett isolerat nätverk. Tänk på följande:

* Om du vill ansluta till den virtuella datorn i Azure med hjälp av fjärrskrivbord efter failover aktivera anslutning till fjärrskrivbord på den virtuella datorn innan du kör redundanstestet.
* Efter redundansväxling, ska du använda en offentlig IP-adress för att ansluta till den virtuella datorn i Azure med hjälp av fjärrskrivbord. Om du vill göra det ser du till att det inte finns några domänprinciper som hindrar dig från att ansluta till en virtuell dator med en offentlig adress.

Om du vill kontrollera åtgärden slutfördes, följer du stegen i [Övervakaraktiviteten](#monitor).

### <a name="run-a-test-failover"></a>Köra ett redundanstest
- Starta redundanstestningen genom att köra följande kommando:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Kör en planerad redundans
- Starta planerad redundans genom att köra följande kommando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Kör en oplanerad redundans
- Starta den oplanerade redundansväxlingen genom att köra följande kommando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

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
