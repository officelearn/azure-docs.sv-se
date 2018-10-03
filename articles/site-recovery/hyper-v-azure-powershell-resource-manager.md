---
title: Replikera virtuella Hyper-V-datorer med PowerShell och Azure Resource Manager | Microsoft Docs
description: Automatisera replikeringen av Hyper-V-datorer till Azure med Azure Site Recovery med PowerShell och Azure Resource Manager.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: b71c381c3ebc2b36c36b862c00de02144f94bd0f
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043683"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Konfigurera katastrofåterställning till Azure för Hyper-V-datorer med PowerShell och Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) bidrar till verksamhetskontinuitet och haveriberedskap (BCDR) genom att samordna replikering, redundans och återställning av virtuella Azure-datorer (VM) och lokala datorer och fysiska servrar.

Den här artikeln beskriver hur du använder Windows PowerShell, tillsammans med Azure Resource Manager för att replikera Hyper-V-datorer till Azure. Exemplet i den här artikeln visar hur du replikerar en enda virtuell dator som körs på en Hyper-V-värd till Azure.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell tillhandahåller cmdletar för att hantera Azure med hjälp av Windows PowerShell. Site Recovery PowerShell-cmdletar, tillgängliga med Azure PowerShell för Azure Resource Manager kan hjälpa dig att skydda och återställa dina servrar i Azure.

Du behöver inte vara en expert PowerShell att använda den här artikeln, men du behöver att förstå grundläggande begrepp, t.ex moduler, cmdlets och sessioner. Läs [komma igång med Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx), och [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Microsoft-partner i programmet Cloud Solution Provider (CSP) kan konfigurera och hantera skydd av kunden servrar till sina respektive CSP-prenumerationer (klient-prenumerationer).
>
>

## <a name="before-you-start"></a>Innan du börjar
Kontrollera att du har dessa krav på plats:

* En [Microsoft Azure](https://azure.microsoft.com/) konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). Dessutom kan du läsa om [priser för Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Information om den här versionen och hur du installerar den finns i [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* Den [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) och [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) moduler. Du kan hämta de senaste versionerna av dessa moduler från den [PowerShell-galleriet](https://www.powershellgallery.com/)

Specifika exemplet som beskrivs i den här artikeln har dessutom följande krav:

* Hyper-V-värd som kör Windows Server 2012 R2 eller Microsoft Hyper-V Server 2012 R2 som innehåller en eller flera virtuella datorer. Hyper-V-servrar bör vara ansluten till Internet, antingen direkt eller via en proxyserver.
* De virtuella datorerna som du vill replikera bör överensstämma med [förutsättningarna](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Steg 1: Logga in på ditt Azure-konto

1. Öppna en PowerShell-konsol och kör detta kommando för att logga in på ditt Azure-konto. Cmdlet: en som öppnas en webbsida uppmanar dig att autentiseringsuppgifterna för ditt konto: **Connect-AzureRmAccount**.
    - Alternativt kan du kan inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect-AzureRmAccount** cmdlet, med hjälp av den **-Credential** parametern.
    - Om du är CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller -klient. Till exempel: **Connect-AzureRmAccount-klient ”fabrikam.com”**
2. Koppla den prenumeration du vill använda med konto, eftersom ett konto kan ha flera prenumerationer:

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Kontrollera att din prenumeration har registrerats för att använda Azure-providers för Recovery Services- och Site Recovery kan använda följande kommandon:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Kontrollera att i utdata från kommandot den **RegistrationState** är inställd på **registrerad**, Fortsätt till steg 2. Om inte, du bör registrera saknas-providern i din prenumeration genom att köra följande kommandon:

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery` `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Kontrollera att Providers, registrerad med följande kommandon:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>Steg 2: Konfigurera valvet

1. Skapa en Azure Resource Manager-resursgrupp där du vill skapa valvet eller Använd en befintlig resursgrupp. Skapa en ny resursgrupp på följande sätt. $ResourceGroupName variabeln som innehåller namnet på resursgruppen som du vill skapa och variabeln $Geo innehåller Azure-region där du vill skapa en resursgrupp (till exempel ”södra Brasilien”).

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. Att erhålla en lista över resursgrupper i prenumerationen som kör den **Get-AzureRmResourceGroup** cmdlet.
2. Skapa ett nytt Azure Recovery Services-valv på följande sätt:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Du kan hämta en lista över befintliga valv med den **Get-AzureRmRecoveryServicesVault** cmdlet.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Steg 3: Ange kontext för Recovery Services-valv

Ange valvkontexten på följande sätt:

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Steg 4: Skapa en Hyper-V-plats

1. Skapa en ny Hyper-V-plats på följande sätt:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. Denna cmdlet startar ett Site Recovery-jobb för att skapa platsen och returnerar ett jobbobjekt för Site Recovery. Vänta tills jobbet slutförts och kontrollera att jobbet har slutförts.
3. Använd den **cmdlet Get-AzureRmSiteRecoveryJob**, för att hämta objektet och kontrollera den aktuella statusen för jobbet.
4. Skapa och ladda ned en registreringsnyckel för platsen, på följande sätt:

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. Kopiera den nedladdade nyckeln till Hyper-V-värden. Du behöver för att registrera Hyper-V-värden till webbplatsen.

## <a name="step-5-install-the-provider-and-agent"></a>Steg 5: Installera providern och agenten

1. Hämta installationsprogrammet för den senaste versionen av providern från [Microsoft](https://aka.ms/downloaddra).
2. Kör installationsprogrammet på theHyper-V-värd.
3. Fortsätta att registreringssteget i slutet av installationen.
4. När du uppmanas, ange den hämta nyckeln och slutför registreringen av Hyper-V-värden.
5. Kontrollera att Hyper-V-värd har registrerats till webbplatsen på följande sätt:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Steg 6: Skapa en replikeringsprincip

Innan du börjar bör du Observera att det angivna lagringskontot ska vara i samma Azure-region som valvet och ska ha geo-replikering aktiverat.

1. Skapa en replikeringsprincip enligt följande:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Kontrollera det returnerade jobbet för att säkerställa att skapandet en replikeringsprincip lyckas.

3. Hämta skyddsbehållaren som motsvarar platsen, på följande sätt:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Associera skyddsbehållaren med replikeringsprincipen, enligt följande:

     $Policy = get-AzureRmSiteRecoveryPolicy - FriendlyName $PolicyName $associationJob = Start AzureRmSiteRecoveryPolicyAssociationJob-principen $Policy - PrimaryProtectionContainer $protectionContainer

4. Vänta tills jobbet är kopplingen har slutförts.

## <a name="step-7-enable-vm-protection"></a>Steg 7: Aktivera skydd av virtuell dator

1. Hämta protection entiteten som motsvarar den virtuella datorn som du vill skydda på följande sätt:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Skydda den virtuella datorn. Om den virtuella datorn som du skyddar innehåller mer än en disk som är kopplade till den, anger du operativsystemets disk med hjälp av den *OSDiskName* parametern.

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. Vänta tills de virtuella datorerna ska nå ett skyddat läge efter den första replikeringen. Det kan ta en stund, beroende på faktorer som mängden data som ska replikeras och den tillgängliga bandbredden för uppströms till Azure. När ett skyddat läge är på plats, uppdateras jobbstatus och StateDescription på följande sätt: 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Uppdatera recovery egenskaper (till exempel VM-rollstorlek) och Azure-nätverk som du vill koppla VM NIC efter en redundansväxling.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Steg 8: Köra ett redundanstest
1. Kör ett redundanstest på följande sätt:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Kontrollera att testet VM skapas i Azure. Testjobb för redundans är inaktiverad när du har skapat den Virtuella testdatorn i Azure.
3. Om du vill rensa och utför redundanstestet, kör du:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Nästa steg
[Läs mer](https://docs.microsoft.com/powershell/module/azurerm.siterecovery) om Azure Site Recovery med Azure Resource Managers PowerShell-cmdletar.
