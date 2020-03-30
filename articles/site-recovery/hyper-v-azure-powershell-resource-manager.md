---
title: Hyper-V VM-haveriberedskap med Azure Site Recovery och PowerShell
description: Automatisera haveriberedskap av virtuella hyper-virtuella datorer till Azure med Azure Site Recovery-tjänsten med PowerShell och Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257997"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Konfigurera haveriberedskap till Azure för virtuella hyper-v-datorer med PowerShell och Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) bidrar till din strategi för affärskontinuitet och haveriberedskap (BCDR) genom att dirigera replikering, redundans och återställning av virtuella Azure-datorer (VIRTUELLA datorer) och lokala virtuella datorer och fysiska servrar.

I den här artikeln beskrivs hur du använder Windows PowerShell, tillsammans med Azure Resource Manager, för att replikera virtuella hyper-virtuella datorer till Azure. Exemplet som används i den här artikeln visar hur du replikerar en enda virtuell dator som körs på en Hyper-V-värd, till Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell tillhandahåller cmdlets för att hantera Azure med Windows PowerShell. Site Recovery PowerShell-cmdletar, som är tillgängliga med Azure PowerShell för Azure Resource Manager, hjälper dig att skydda och återställa dina servrar i Azure.

Du behöver inte vara powershell-expert för att kunna använda den här artikeln, men du måste förstå grundläggande begrepp, till exempel moduler, cmdlets och sessioner. Mer information finns i [PowerShell-dokumentationen](/powershell) och [använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Microsoft-partner i CSP-programmet (Cloud Solution Provider) kan konfigurera och hantera skydd av kundservrar till sina respektive CSP-prenumerationer (arklientprenumerationer).

## <a name="before-you-start"></a>Innan du börjar

Se till att du har dessa förutsättningar på plats:

- Ett [Microsoft Azure-konto.](https://azure.microsoft.com/) Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). Dessutom kan du läsa om [azure Site Recovery Manager prissättning](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Information om den här versionen och hur du installerar den finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps).

Dessutom har det specifika exemplet som beskrivs i den här artikeln följande förutsättningar:

- En Hyper-V-värd som kör Windows Server 2012 R2 eller Microsoft Hyper-V Server 2012 R2 som innehåller en eller flera virtuella datorer. Hyper-V-servrar bör anslutas till Internet, antingen direkt eller via en proxy.
- De virtuella datorer som du vill replikera bör uppfylla [dessa förutsättningar](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Steg 1: Logga in på ditt Azure-konto

1. Öppna en PowerShell-konsol och kör det här kommandot för att logga in på ditt Azure-konto. Cmdleten visar en webbsida som uppmanar dig `Connect-AzAccount`att ange dina kontouppgifter: .
   - Alternativt kan du inkludera dina kontouppgifter som `Connect-AzAccount` en parameter i cmdleten med parametern **Autentiseringsuppgifter.**
   - Om du är en CSP-partner som arbetar för en klientorganisations räkning anger du kunden som klient med hjälp av deras klientid eller klient primära domännamn. Exempel: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Associera prenumerationen som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Kontrollera att din prenumeration är registrerad för att använda Azure-leverantörerna för Återställningstjänster och webbplatsåterställning med hjälp av följande kommandon:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Kontrollera att i kommandoutdata är **RegistrationState** inställt **på Registrerad**, du kan gå vidare till steg 2. Om inte, bör du registrera den saknade leverantören i din prenumeration genom att köra dessa kommandon:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Kontrollera att leverantörerna har registrerats med hjälp av följande kommandon:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Steg 2: Ställ in valvet

1. Skapa en Azure Resource Manager-resursgrupp där du kan skapa valvet eller använda en befintlig resursgrupp. Skapa en ny resursgrupp enligt följande. Variabeln `$ResourceGroupName` innehåller namnet på den resursgrupp som du vill skapa och variabeln $Geo innehåller Azure-regionen där resursgruppen ska skapas (till exempel "Södra Brasilien").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Om du vill hämta en lista över `Get-AzResourceGroup` resursgrupper i prenumerationen kör du cmdleten.
1. Skapa ett nytt Azure Recovery Services-valv enligt följande:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Du kan hämta en lista över `Get-AzRecoveryServicesVault` befintliga valv med cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Steg 3: Ange valvkontexten för Återställningstjänster

Ange valvkontexten på följande sätt:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Steg 4: Skapa en Hyper-V-webbplats

1. Skapa en ny Hyper-V-webbplats enligt följande:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Den här cmdleten startar ett site recovery-jobb för att skapa platsen och returnerar ett jobbobjekt för platsåterställning. Vänta tills jobbet har slutförts och kontrollera att jobbet har slutförts.
1. Använd `Get-AzRecoveryServicesAsrJob` cmdlet för att hämta jobbobjektet och kontrollera jobbets aktuella status.
1. Generera och hämta en registreringsnyckel för webbplatsen enligt följande:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Kopiera den nedladdade nyckeln till Hyper-V-värden. Du behöver nyckeln för att registrera Hyper-V-värden på webbplatsen.

## <a name="step-5-install-the-provider-and-agent"></a>Steg 5: Installera leverantören och agenten

1. Hämta installationsprogrammet för den senaste versionen av providern från [Microsoft](https://aka.ms/downloaddra).
1. Kör installationsprogrammet på Hyper-V-värden.
1. I slutet av installationen fortsätter till registreringssteget.
1. Ange den hämtade nyckeln och slutföra registreringen av Hyper-V-värden när den uppmanas att göra det.
1. Kontrollera att Hyper-V-värden är registrerad på webbplatsen enligt följande:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Om du kör en Hyper-V-kärnserver laddar du ned installationsfilen och gör så här:

1. Extrahera filerna från _AzureSiteRecoveryProvider.exe_ till en lokal katalog genom att köra det här kommandot:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Kör följande kommando:

   ```console
   .\setupdr.exe /i
   ```

   Resultaten loggas till _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registrera servern genom att köra det här kommandot:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Steg 6: Skapa en replikeringsprincip

Innan du börjar bör det angivna lagringskontot vara i samma Azure-region som valvet och ha geo-replikering aktiverat.

1. Skapa en replikeringsprincip enligt följande:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Kontrollera det returnerade jobbet för att säkerställa att replikeringsprincipen lyckas.

1. Hämta skyddsbehållaren som motsvarar platsen enligt följande:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Associera skyddsbehållaren med replikeringsprincipen enligt följande:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Vänta tills associationsjobbet har slutförts.

1. Hämta mappningen av skyddsbehållaren.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Steg 7: Aktivera vm-skydd

1. Hämta det skyddade objektet som motsvarar den virtuella dator som du vill skydda enligt följande:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Skydda den virtuella datorn. Om den virtuella datorn som du skyddar har mer än en disk kopplad till den anger du operativsystemdisken med parametern **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Vänta tills de virtuella datorerna har nått ett skyddat tillstånd efter den första replikeringen. Detta kan ta ett tag, beroende på faktorer som mängden data som ska replikeras och den tillgängliga uppströms bandbredden till Azure. När ett skyddat tillstånd är på plats uppdateras jobbtillståndet och stateDescription på följande sätt:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Uppdatera återställningsegenskaper (till exempel vm-rollstorleken) och Azure-nätverket som du vill koppla det virtuella nätverkskortet till efter redundans.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Om du vill replikera till CMK-aktiverade hanterade diskar i Azure gör du följande steg med Az PowerShell 3.3.0 och framåt:
>
> 1. Aktivera redundans till hanterade diskar genom att uppdatera vm-egenskaper
> 1. Använd `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet för att hämta disk-ID:n för varje disk i det skyddade objektet
> 1. Skapa ett ordlisteobjekt med cmdlet `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` för att innehålla mappning av disk-ID till diskkrypteringsuppsättning. Dessa diskkrypteringsuppsättningar ska skapas i föraningen av dig i målregionen.
> 1. Uppdatera egenskaperna för `Set-AzRecoveryServicesAsrReplicationProtectedItem` virtuella datorer med cmdlet genom att skicka ordlisteobjektet i parametern **DiskIdToDiskEncryptionSetMap.**

## <a name="step-8-run-a-test-failover"></a>Steg 8: Kör en testväxling

1. Kör en testväxling enligt följande:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Kontrollera att testdatorn har skapats i Azure. Test redundansjobbet pausas när testdatorn har skapats i Azure.
1. Om du vill rensa och slutföra testundansen kör du:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Nästa steg

[Läs mer](/powershell/module/az.recoveryservices) om Azure Site Recovery med Azure Resource Manager PowerShell-cmdlets.
