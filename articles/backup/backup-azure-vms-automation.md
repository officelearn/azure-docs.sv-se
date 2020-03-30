---
title: Säkerhetskopiera och återställa virtuella Azure-datorer med PowerShell
description: Beskriver hur du säkerhetskopierar och återställer virtuella Azure-datorer med Azure Backup med PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 733a06a84aa170f1361ea74d126ec9752586fce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247987"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Säkerhetskopiera och återställa virtuella Azure-datorer med PowerShell

I den här artikeln beskrivs hur du säkerhetskopierar och återställer en Virtuell Azure-dator i ett [Azure Backup](backup-overview.md) Recovery Services-valv med PowerShell-cmdletar.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
>
> * Skapa ett Recovery Services-valv och ange valvkontexten.
> * Definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * Utlösa ett säkerhetskopieringsjobb på begäran för skyddade virtuella datorer Innan du kan säkerhetskopiera (eller skydda) en virtuell dator måste du slutföra [förutsättningarna](backup-azure-arm-vms-prepare.md) för att förbereda din miljö för att skydda dina virtuella datorer.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* [Granska](backup-architecture.md#architecture-built-in-azure-vm-backup) arkitekturen för azure vm-säkerhetskopiering, [lär dig mer om](backup-azure-vms-introduction.md) säkerhetskopieringsprocessen och [granska](backup-support-matrix-iaas.md) support, begränsningar och förutsättningar.
* Granska PowerShell-objekthierarkin för Återställningstjänster.

## <a name="recovery-services-object-hierarchy"></a>Objekthierarki för Återställningstjänster

Objekthierarkin sammanfattas i följande diagram.

![Objekthierarki för Återställningstjänster](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska [cmdletreferensen](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) **Az.RecoveryServices** i Azure-biblioteket.

## <a name="set-up-and-register"></a>Ställa in och registrera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här börjar du:

1. [Ladda ned den senaste versionen av PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Hitta azure backup PowerShell-cmdletar som är tillgängliga genom att skriva följande kommando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services-valvet visas. Följande bild är ett exempel på vad du ser. Det är inte en komplett lista över cmdlets.

    ![lista över återställningstjänster](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Logga in på ditt **Azure-konto med Connect-AzAccount**. Den här cmdleten visar en webbsida som uppmanar dig att ange dina kontouppgifter:

    * Alternativt kan du inkludera dina kontouppgifter som en parameter i cmdleten **Connect-AzAccount** med parametern **-Autentiseringsuppgifter.**
    * Om du är CSP-partner som arbetar för en klientorganisations räkning anger du kunden som klient, med hjälp av deras tenantID eller tenant primary domain name. Till exempel: **Connect-AzAccount -Tenant "fabrikam.com"**

4. Associera prenumerationen som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Om du använder Azure Backup för första gången måste du använda cmdleten **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** för att registrera Azure Recovery Service-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Du kan kontrollera att de registrerade leverantörerna med hjälp av följande kommandon:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    I kommandoutdata ska **RegistrationState** ändras till **Registrerad**. Om inte, bara köra **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet igen.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följande steg leder dig genom att skapa ett Recovery Services-valv. Ett Recovery Services-valv skiljer sig från ett valv för säkerhetskopiering.

1. Valvet för återställningstjänster är en Resource Manager-resurs, så du måste placera det i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med cmdleten **[New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** När du skapar en resursgrupp anger du namn och plats för resursgruppen.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Använd cmdleten [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) för att skapa valvet för återställningstjänster. Var noga med att ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Ange vilken typ av lagringsredundans som ska användas. Du kan använda [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [GEO Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet -BackupStorageRedundancy för testvault är inställt på GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visa valven i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault:](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)

```powershell
Get-AzRecoveryServicesVault
```

Utdata liknar följande exempel, lägg märke till att associerade ResourceGroupName och Plats tillhandahålls.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Säkerhetskopiera virtuella Azure-datorer

Använd ett Recovery Services-valv för att skydda dina virtuella datorer. Innan du använder skyddet anger du arkivkontexten (typen av data som skyddas i valvet) och verifierar skyddsprincipen. Skyddsprincipen är schemat när säkerhetskopieringsjobben körs och hur länge varje ögonblicksbild av säkerhetskopiering behålls.

### <a name="set-vault-context"></a>Ange valvkontext

Innan du aktiverar skydd på en virtuell dator använder du [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) för att ange arkivkontexten. När valvet sammanhang är inställt gäller det alla efterkommande cmdletar. I följande exempel anges valvets kontext för valvet, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID:et

Vi planerar att ta bort kontextinställningen för valvet i enlighet med Azure PowerShell-riktlinjer. I stället kan du lagra eller hämta valv-ID:t och skicka det till relevanta kommandon. Så om du inte har angett valvkontexten eller vill ange kommandot för att köra för ett visst valv, skickar du valv-ID:t som "-vaultID" till alla relevanta kommandon enligt följande:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Eller

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Ändra inställningar för lagringsreplikering

Använd [kommandot Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) för att ställa in konfigurationen för lagringsreplikering av valvet till LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Lagringsredundans kan bara ändras om det inte finns några säkerhetskopior som skyddas till valvet.

### <a name="create-a-protection-policy"></a>Skapa en skyddsprincip

När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning. Principen för standardskydd utlöser ett säkerhetsjobb varje dag vid en viss tidpunkt. Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. Du kan använda standardprincipen för att snabbt skydda din virtuella dator och redigera principen senare med olika information.

Använd **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** för att visa de skyddsprinciper som är tillgängliga i valvet. Du kan använda den här cmdleten för att hämta en specifik princip eller för att visa de principer som är associerade med en arbetsbelastningstyp. Följande exempel hämtar principer för arbetsbelastningstyp, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

Utdata ser ut ungefär så här:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Tidszonen för fältet BackupTime i PowerShell är UTC. Men när säkerhetskopieringstiden visas i Azure-portalen justeras tiden till din lokala tidszon.
>
>

En princip för skydd för säkerhetskopiering är associerad med minst en bevarandeprincip. En bevarandeprincip definierar hur länge en återställningspunkt behålls innan den tas bort.

* Använd [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) för att visa standardlagringsprincipen.
* På samma sätt kan du använda [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) för att hämta standardschemaprincipen.
* Cmdleten [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) skapar ett PowerShell-objekt som innehåller principinformation för säkerhetskopiering.
* Schema- och bevarandeprincipobjekten används som indata till cmdleten New-AzRecoveryServicesBackupProtectionPolicy.

Som standard definieras en starttid i schemaprincipobjektet. Använd följande exempel för att ändra starttiden till önskad starttid. Den önskade starttiden bör också vara i UTC. I exemplet nedan förutsätter att önskad starttid är 01:00 UTC för dagliga säkerhetskopior.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Du behöver bara ange starttiden på 30 minuter. I exemplet ovan kan det bara vara "01:00:00" eller "02:30:00". Starttiden kan inte vara "01:15:00"

I följande exempel lagras schemaprincipen och bevarandeprincipen i variabler. I exemplet används dessa variabler för att definiera parametrarna när du skapar en skyddsprincip, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

Utdata ser ut ungefär så här:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Aktivera skydd

När du har definierat skyddsprincipen måste du fortfarande aktivera principen för ett objekt. Använd [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) för att aktivera skydd. För att aktivera skydd krävs två objekt - objektet och principen. När principen har associerats med valvet utlöses arbetsflödet för säkerhetskopiering vid den tidpunkt som definieras i principschemat.

> [!IMPORTANT]
> När du använder PS för att aktivera säkerhetskopiering för flera virtuella datorer samtidigt, se till att en enda princip inte har mer än 100 virtuella datorer associerade med den. Detta är en [rekommenderad bästa praxis](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy). För närvarande blockerar PS-klienten inte uttryckligen om det finns fler än 100 virtuella datorer men checken är planerad att läggas till i framtiden.

Följande exempel möjliggör skydd för objektet, V2VM, med hjälp av principen NewPolicy. Exemplen skiljer sig beroende på om den virtuella datorn är krypterad och vilken typ av kryptering.

Så här aktiverar du skyddet för **icke-krypterade virtuella datorer i Resource Manager:**

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Om du vill aktivera skyddet för krypterade virtuella datorer (krypterade med BEK och KEK) måste du ge Azure Backup-tjänsten behörighet att läsa nycklar och hemligheter från nyckelvalvet.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Om du vill aktivera skyddet för **krypterade virtuella datorer (endast krypterat med BEK)** måste du ge Azure Backup-tjänsten behörighet att läsa hemligheter från nyckelvalvet.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Om du använder Azure Government-molnet använder du värdet ff281ffe-705c-4f53-9f37-a40e6f2c68f3 för parametern ServicePrincipalName i [Cmdlet Set-AzKeyVaultAccessPolicy.](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)
>

## <a name="monitoring-a-backup-job"></a>Övervaka ett säkerhetskopieringsjobb

Du kan övervaka tidskrävande åtgärder, till exempel säkerhetskopieringsjobb, utan att använda Azure-portalen. Om du vill ha status för ett pågående jobb använder du cmdleten [Get-AzRecoveryservicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) Den här cmdleten hämtar säkerhetskopieringsjobben för ett visst valv och det valvet anges i arkivkontexten. I följande exempel får status för ett pågående jobb som en matris och statusen lagras i variabeln $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

Utdata ser ut ungefär så här:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

I stället för att avsöka dessa jobb för slutförande - vilket är onödig extra kod - använd [cmdleten Wait-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) Den här cmdleten pausar körningen tills antingen jobbet är slutfört eller det angivna timeout-värdet har uppnåtts.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Hantera säkerhetskopior av virtuella Azure-datorer

### <a name="modify-a-protection-policy"></a>Ändra en skyddsprincip

Om du vill ändra skyddsprincipen använder du [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) för att ändra SchedulePolicy- eller RetentionPolicy-objekten.

#### <a name="modifying-scheduled-time"></a>Ändra schemalagd tid

När du skapar en skyddsprincip tilldelas den som standard en starttid. Följande exempel visar hur du ändrar starttiden för en skyddsprincip.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Ändra kvarhållning

I följande exempel ändras kvarhållningen av återställningspunkten till 365 dagar.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurera lagring av ögonblicksbilder för direkt återställning

> [!NOTE]
> Från Az PS version 1.6.0 och framåt, kan man uppdatera omedelbar återställning ögonblicksbild lagringsperiod i politiken med Powershell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Standardvärdet blir 2, användaren kan ställa in värdet med en minut på 1 och max 5. För veckovisa principer för säkerhetskopiering är perioden inställd på 5 och kan inte ändras.

### <a name="trigger-a-backup"></a>Utlösa en säkerhetskopia

Använd [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) för att utlösa ett säkerhetskopieringsjobb. Om det är den första säkerhetskopian är det en fullständig säkerhetskopia. Efterföljande säkerhetskopior tar en inkrementell kopia. I följande exempel krävs att en vm-säkerhetskopia behålls i 60 dagar.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Utdata ser ut ungefär så här:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Tidszonen för Fälten StartTime och EndTime i PowerShell är UTC. Men när tiden visas i Azure-portalen justeras tiden till din lokala tidszon.
>
>

### <a name="change-policy-for-backup-items"></a>Ändra princip för säkerhetskopieringsobjekt

Användaren kan antingen ändra befintlig princip eller ändra principen för det säkerhetskopierade objektet från Princip1 till Princip2. Om du vill växla principer för ett säkerhetskopierat objekt hämtar du relevant princip och säkerhetskopierar objektet och använder kommandot [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) med säkerhetskopieringsobjekt som parameter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Kommandot väntar tills konfigurera säkerhetskopian är klar och returnerar följande utdata.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Sluta skydda

#### <a name="retain-data"></a>Behålla data

Om användaren vill stoppa skyddet kan de använda cmdleten [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Detta stoppar schemalagda säkerhetskopior men de data som säkerhetskopierats hittills behålls för alltid.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

För att helt ta bort lagrade säkerhetskopieringsdata i valvet, bara lägga till "-RemoveRecoveryPoints" flagga / växla till ["inaktivera" skydd kommandot](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Återställa en virtuell Azure-dator

Det finns en viktig skillnad mellan att återställa en virtuell dator med Azure-portalen och återställa en virtuell dator med PowerShell. Med PowerShell slutförs återställningen när diskarna och konfigurationsinformationen från återställningspunkten har skapats. Återställningsåtgärden skapar inte den virtuella datorn. Om du vill skapa en virtuell dator från disken finns i avsnittet [Skapa den virtuella datorn från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Om du inte vill återställa hela den virtuella datorn, men vill återställa eller återställa några filer från en Azure VM-säkerhetskopia, läser du [avsnittet filåterställning](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Återställningsåtgärden skapar inte den virtuella datorn.
>
>

Följande bild visar objekthierarkin från RecoveryServicesVault ner till BackupRecoveryPoint.

![Objekthierarki för Återställningstjänster som visar BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Om du vill återställa säkerhetskopierade data identifierar du det säkerhetskopierade objektet och återställningspunkten som innehåller point-in-time-data. Använd [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) för att återställa data från valvet till ditt konto.

De grundläggande stegen för att återställa en virtuell Azure-dator är:

* Välj VM.
* Välj en återställningspunkt.
* Återställ diskarna.
* Skapa den virtuella datorn från lagrade diskar.

### <a name="select-the-vm"></a>Välj den virtuella datorn

Om du vill hämta PowerShell-objektet som identifierar rätt säkerhetskopieringsobjekt startar du från behållaren i valvet och arbetar dig ner för objekthierarkin. Om du vill välja den behållare som representerar den virtuella datorn använder du cmdleten [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) och pipe som till cmdleten [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt

Använd [cmdleten Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) för att visa alla återställningspunkter för säkerhetskopieringsobjektet. Välj sedan återställningspunkten för att återställa. Om du är osäker på vilken återställningspunkt som ska användas är det en bra idé att välja den senaste RecoveryPointType = AppConsistent-punkten i listan.

I följande skript är **variabeln, $rp,** en matris med återställningspunkter för det valda säkerhetskopieringsobjektet från de senaste sju dagarna. Matrisen sorteras i omvänd tidsordning med den senaste återställningspunkten vid index 0. Använd standardindexering av PowerShell-matris för att välja återställningspunkten. I exemplet väljer $rp[0] den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Utdata ser ut ungefär så här:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Återställa diskarna

Använd cmdleten [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) för att återställa data och konfiguration för ett säkerhetskopieringsobjekt till en återställningspunkt. När du har identifierat en återställningspunkt använder du den som värde för parametern **-RecoveryPoint.** I ovanstående prov var **$rp[0]** återställningspunkten att använda. I följande exempelkod är **$rp[0]** den återställningspunkt som ska användas för att återställa disken.

Så här återställer du diskarna och konfigurationsinformationen:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Återställa hanterade diskar

> [!NOTE]
> Om den säkerhetskopierade virtuella datorn har hanterade diskar och du vill återställa dem som hanterade diskar har vi infört funktionen från Azure PowerShell RM-modul v 6.7.0. och framåt
>
>

Ange ytterligare en parameter **TargetResourceGroupName** för att ange den RG som hanterade diskar ska återställas till.

> [!NOTE]
> Vi rekommenderar starkt att du använder parametern **TargetResourceGroupName** för att återställa hanterade diskar eftersom det resulterar i betydande prestandaförbättringar. Från Azure Powershell Az-modul 1.0 och framåt är den här parametern obligatorisk vid återställning med hanterade diskar
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

**VMConfig.JSON-filen** återställs till lagringskontot och de hanterade diskarna återställs till det angivna mål-RG.The VMConfig.JSON file will be restored to the storage account and the managed disks will be restored to the specified target RG.

Utdata ser ut ungefär så här:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Använd cmdleten [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) för att vänta på att återställningsjobbet ska slutföras.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

När återställningsjobbet har slutförts använder du cmdleten [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) för att hämta information om återställningen. Egenskapen JobDetails har den information som behövs för att återskapa den virtuella datorn.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

När du har återställt diskarna går du till nästa avsnitt för att skapa den virtuella datorn.

## <a name="replace-disks-in-azure-vm"></a>Ersätta diskar i Azure VM

Så här ersätter du diskarna och konfigurationsinformationen:

* Steg 1: [Återställa diskarna](backup-azure-vms-automation.md#restore-the-disks)
* Steg 2: [Koppla bort datadisken med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
* Steg 3: [Koppla datadisk till Windows VM med PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>Skapa en virtuell dator från återställda diskar

När du har återställt diskarna använder du följande steg för att skapa och konfigurera den virtuella datorn från disken.

> [!NOTE]
>
> 1. AzureAz-modul 3.0.0 eller senare krävs. <br>
> 2. Om du vill skapa krypterade virtuella datorer från återställda diskar måste din Azure-roll ha behörighet att utföra åtgärden, **Microsoft.KeyVault/vaults/deploy/action**. Om din roll inte har den här behörigheten skapar du en anpassad roll med den här åtgärden. Mer information finns [i Anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md). <br>
> 3. När du har återställt diskar kan du nu hämta en distributionsmall som du direkt kan använda för att skapa en ny virtuell dator. Inga fler olika PS-cmdlets för att skapa hanterade/ohanterade virtuella datorer som är krypterade/okrypterade.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Skapa en virtuell dator med distributionsmallen

Den resulterande jobbinformationen ger mallen URI som kan efterfrågas och distribueras.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Mallen är inte direkt tillgänglig eftersom den finns under en kunds lagringskonto och den angivna behållaren. Vi behöver den fullständiga webbadressen (tillsammans med en tillfällig SAS-token) för att komma åt den här mallen.

1. Extrahera först mallnamnet från mallenBlobURI. Formatet nämns nedan. Du kan använda den delade åtgärden i Powershell för att extrahera det slutliga mallnamnet från den här URL:en.

```http
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

2. Då den fullständiga webbadressen kan genereras som förklaras [här](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-powershell#provide-sas-token-during-deployment).

```powershell
Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
$templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
```

3. Distribuera mallen för att skapa en ny virtuell dator enligt beskrivningen [här](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
```

### <a name="create-a-vm-using-the-config-file"></a>Skapa en virtuell dator med konfigurationsfilen

I följande avsnitt visas steg som krävs för att skapa en virtuell dator med hjälp av filen VMConfig.

> [!NOTE]
> Vi rekommenderar starkt att du använder distributionsmallen som beskrivs ovan för att skapa en virtuell dator. Detta avsnitt (punkterna 1-6) kommer att inaktuella snart.

1. Fråga de återställda diskegenskaperna för jobbinformationen.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Ange Azure-lagringskontexten och återställ JSON-konfigurationsfilen.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Använd JSON-konfigurationsfilen för att skapa vm-konfigurationen.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Koppla OS-disken och datadiskarna. Det här steget innehåller exempel för olika hanterade och krypterade VM-konfigurationer. Använd exemplet som passar din VM-konfiguration.

* **Icke-hanterade och icke-krypterade virtuella datorer** – Använd följande exempel för icke-hanterade, icke-krypterade virtuella datorer.

```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
```

* **Icke-hanterade och krypterade virtuella datorer med Azure AD (endast BEK)** - För icke-hanterade, krypterade virtuella datorer med Azure AD (krypterad endast med BEK) måste du återställa hemligheten till nyckelvalvet innan du kan bifoga diskar. Mer information finns i [Återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Följande exempel visar hur du kopplar till operativsystem och datadiskar för krypterade virtuella datorer. När du ställer in OS-disken ska du ange den relevanta OS-typen.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Icke-hanterade och krypterade virtuella datorer med Azure AD (BEK och KEK)** – För icke-hanterade, krypterade virtuella datorer med Azure AD (krypterad med BEK och KEK) återställer du nyckeln och hemligheten till nyckelvalvet innan diskarna bifogas. Mer information finns i [Återställa en krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Följande exempel visar hur du kopplar till operativsystem och datadiskar för krypterade virtuella datorer.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
```

* **Icke-hanterade och krypterade virtuella datorer utan Azure AD (endast BEK)** – För icke-hanterade, krypterade virtuella datorer utan Azure AD (krypterad endast med BEK), om **källnyckelVault/hemlighet inte är tillgängliga** återställer du hemligheterna till nyckelvalvet med hjälp av proceduren i [Återställa en icke-krypterad virtuell dator från en Azure Backup-återställningspunkt](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypteringsinformation på den återställda OS-blobben (det här steget krävs inte för datablobb). Den $dekurl kan hämtas från den återställda keyVault.

Skriptet nedan behöver endast köras när källnyckelnVault/secret inte är tillgänglig.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

När **hemligheterna är tillgängliga** och krypteringsinformationen också anges på OS Blob, bifoga diskarna med skriptet nedan.

Om källnyckelnVault/hemligheter redan är tillgängliga behöver skriptet ovan inte köras.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Icke-hanterade och krypterade virtuella datorer utan Azure AD (BEK och KEK)** – För icke-hanterade, krypterade virtuella datorer utan Azure AD (krypterade med BEK & KEK), om **källnyckelVault/key/secret inte är tillgängliga** återställer du nyckeln och hemligheterna till nyckelvalvet med hjälp av proceduren i [Återställa en icke-krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypteringsinformation på den återställda OS-blobben (det här steget krävs inte för datablobb). Den $dekurl och $kekurl kan hämtas från den återställda keyVault.

Skriptet nedan behöver endast köras när källnyckelnVault/key/secret inte är tillgänglig.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

När **nyckeln / hemligheter är tillgängliga** och krypteringsinformationen är inställd på OS Blob, bifoga diskarna med skriptet nedan.

Om källnyckelnVault/key/secrets är tillgängliga behöver skriptet ovan inte köras.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Hanterade och icke-krypterade virtuella datorer** – För hanterade icke-krypterade virtuella datorer ansluter du de återställda hanterade diskarna. Detaljerad information finns i [Bifoga en datadisk till en Windows VM med PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Hanterade och krypterade virtuella datorer med Azure AD (endast BEK)** - För hanterade krypterade virtuella datorer med Azure AD (krypterad endast med BEK) bifogar du de återställda hanterade diskarna. Detaljerad information finns i [Bifoga en datadisk till en Windows VM med PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Hanterade och krypterade virtuella datorer med Azure AD (BEK och KEK)** - För hanterade krypterade virtuella datorer med Azure AD (krypterad med BEK och KEK) bifogar du de återställda hanterade diskarna. Detaljerad information finns i [Bifoga en datadisk till en Windows VM med PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Hanterade och krypterade virtuella datorer utan Azure AD (endast BEK)** -För hanterade, krypterade virtuella datorer utan Azure AD (krypterad endast med BEK), om **källnyckelVault/hemlighet inte är tillgängliga** återställer hemligheterna till nyckelvalvet med hjälp av proceduren i [Återställa en icke-krypterad virtuell dator från en Azure Backup-återställningspunkt](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypteringsinformation på den återställda OS-disken (det här steget krävs inte för datadisken). Den $dekurl kan hämtas från den återställda keyVault.

Skriptet nedan behöver endast köras när källnyckelnVault/secret inte är tillgänglig.  

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

När hemligheterna är tillgängliga och krypteringsinformationen har angetts på OS-disken, för att ansluta de återställda hanterade diskarna, se [Bifoga en datadisk till en Windows VM med PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Hanterade och krypterade virtuella datorer utan Azure AD (BEK och KEK)** – För hanterade, krypterade virtuella datorer utan Azure AD (krypterade med BEK & KEK), om **källnyckelvault/nyckel/hemlighet inte är tillgängliga** återställer du nyckeln och hemligheterna till nyckelvalvet med hjälp av proceduren i [Återställa en icke-krypterad virtuell dator från en återställningspunkt för Azure Backup](backup-azure-restore-key-secret.md). Kör sedan följande skript för att ange krypteringsinformation på den återställda OS-disken (det här steget krävs inte för datadiskar). Den $dekurl och $kekurl kan hämtas från den återställda keyVault.

Skriptet nedan behöver endast köras när källnyckelnVault/key/secret inte är tillgänglig.

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
$encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

När nyckeln/hemligheterna är tillgängliga och krypteringsinformationen har angetts på OS-disken, för att ansluta de återställda hanterade diskarna, se [Bifoga en datadisk till en Windows VM med PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Ställ in nätverksinställningarna.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Skapa den virtuella datorn.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Tryck på ADE-förlängningen.
   Om ADE-tilläggen inte skjuts markeras datadiskarna som okrypterade, så det är obligatoriskt att stegen nedan körs:

   * **För virtuell dator med Azure AD** - Använd följande kommando för att manuellt aktivera kryptering för datadiskarna  

     **ENDAST BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK och KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **För virtuell dator utan Azure AD** - Använd följande kommando för att manuellt aktivera kryptering för datadiskarna.

     Om den under kommandokörningen frågar efter AADClientID måste du uppdatera din Azure PowerShell.

     **ENDAST BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK och KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Se till att manuellt ta bort JASON-filer som skapats som en del av krypterad VM-återställningsdiskprocess.

## <a name="restore-files-from-an-azure-vm-backup"></a>Återställa filer från en säkerhetskopia av Den virtuella datorn i Azure

Förutom att återställa diskar kan du också återställa enskilda filer från en Azure VM-säkerhetskopia. Funktionen för återställningsfiler ger åtkomst till alla filer i en återställningspunkt. Hantera filerna via Utforskaren som du skulle för normala filer.

De grundläggande stegen för att återställa en fil från en azure VM-säkerhetskopia är:

* Välj den virtuella datorn
* Välj en återställningspunkt
* Montera diskarna för återställningspunkt
* Kopiera de nödvändiga filerna
* Avmontera disken

### <a name="select-the-vm"></a>Välj den virtuella datorn

Om du vill hämta PowerShell-objektet som identifierar rätt säkerhetskopieringsobjekt startar du från behållaren i valvet och arbetar dig ner för objekthierarkin. Om du vill välja den behållare som representerar den virtuella datorn använder du cmdleten [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) och pipe som till cmdleten [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt

Använd [cmdleten Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) för att visa alla återställningspunkter för säkerhetskopieringsobjektet. Välj sedan återställningspunkten för att återställa. Om du är osäker på vilken återställningspunkt som ska användas är det en bra idé att välja den senaste RecoveryPointType = AppConsistent-punkten i listan.

I följande skript är **variabeln, $rp,** en matris med återställningspunkter för det valda säkerhetskopieringsobjektet från de senaste sju dagarna. Matrisen sorteras i omvänd tidsordning med den senaste återställningspunkten vid index 0. Använd standardindexering av PowerShell-matris för att välja återställningspunkten. I exemplet väljer $rp[0] den senaste återställningspunkten.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Utdata ser ut ungefär så här:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Montera diskarna för återställningspunkt

Använd cmdleten [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) för att få skriptet att montera alla diskar i återställningspunkten.

> [!NOTE]
> Diskarna monteras som iSCSI-anslutna diskar på datorn där skriptet körs. Montering sker omedelbart, och du ådrar dig inga avgifter.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

Utdata ser ut ungefär så här:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Kör skriptet på datorn där du vill återställa filerna. Om du vill köra skriptet måste du ange det angivna lösenordet. När diskarna har bifogats använder du Utforskaren i Utforskaren för att bläddra bland de nya volymerna och filerna. Mer information finns i artikeln Säkerhetskopiering, [Återställ filer från Azures säkerhetskopiering av virtuella datorer](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Avmontera diskarna

När de nödvändiga filerna har kopierats använder du [Disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) för att avmontera diskarna. Var noga med att avmontera diskarna så att åtkomsten till filerna på återställningspunkten tas bort.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Nästa steg

Om du föredrar att använda PowerShell för att interagera med dina Azure-resurser läser du PowerShell-artikeln, [Distribuera och hantera säkerhetskopiering för Windows Server](backup-client-automation.md). Om du hanterar DPM-säkerhetskopior läser du artikeln [Distribuera och hantera säkerhetskopiering för DPM](backup-dpm-automation.md).
