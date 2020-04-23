---
title: Säkerhetskopiera Azure Files med PowerShell
description: I den här artikeln får du lära dig hur du säkerhetskopierar Azure Files med hjälp av tjänsten Azure Backup och PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 865cfc6daa7568236b0306ba591b42a9f7704dd4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101186"
---
# <a name="back-up-azure-files-with-powershell"></a>Säkerhetskopiera Azure Files med PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att säkerhetskopiera en Azure Files fil resurs med hjälp av ett [Azure Backup](backup-overview.md) Recovery Services-valv.

Den här artikeln förklarar hur du:

> [!div class="checklist"]
>
> * Konfigurera PowerShell och registrera Azure Recovery Services-providern.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhets kopiering för en Azure-filresurs.
> * Kör ett säkerhets kopierings jobb.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* Granska PowerShell-objektcachen för Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektets hierarki

Objektets hierarki sammanfattas i följande diagram.

![Recovery Services objektets hierarki](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska referens referensen för [cmdleten](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** i Azure-biblioteket.

## <a name="set-up-and-install"></a>Konfigurera och installera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera PowerShell på följande sätt:

1. [Ladda ned den senaste versionen av AZ PowerShell](/powershell/azure/install-az-ps). Den lägsta version som krävs är 1.0.0.

    > [!WARNING]
    > Den lägsta PS-versionen som krävs för säkerhets kopiering av Azure-filresursen är **AZ. RecoveryServices 2.6.0**. Uppgradera din version för att undvika eventuella problem med de befintliga skripten. Installera den lägsta versionen med följande PS-kommando:

    ```powershell
    Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    ```

2. Hitta Azure Backup PowerShell-cmdlet: ar med det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Granska alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services valvet visas. Här är ett exempel på vad du kan se. Det är inte en fullständig lista över cmdletar.

    ![Lista över Recovery Services-cmdletar](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Logga in på ditt Azure-konto med **Connect-AzAccount**.
5. På webb sidan som visas uppmanas du att ange dina autentiseringsuppgifter för kontot.

    * Alternativt kan du inkludera dina konto uppgifter som en parameter i cmdleten **Connect-AzAccount** med **-Credential**.
    * Om du är en CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras tenantID eller primära domän namn. Ett exempel är **Connect-AzAccount-Tenant** fabrikam.com.

6. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Om du använder Azure Backup för första gången ska du använda cmdleten **register-AzResourceProvider** för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifiera att providern har registrerats:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. I kommandot utdata kontrollerar du att **RegistrationState** ändras till **registrerad**. Om den inte gör det kör du cmdleten **register-AzResourceProvider** igen.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Recovery Services valvet är en Resource Manager-resurs, så du måste placera den i en resurs grupp. Du kan använda en befintlig resurs grupp, eller så kan du skapa en resurs grupp med cmdleten **New-AzResourceGroup** . När du skapar en resurs grupp anger du namn och plats för resurs gruppen.

Följ dessa steg om du vill skapa ett Recovery Services-valv.

1. Ett valv placeras i en resurs grupp. Om du inte har en befintlig resurs grupp skapar du en ny med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). I det här exemplet skapar vi en ny resurs grupp i regionen USA, västra.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Använd cmdleten [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) för att skapa valvet. Ange samma plats för valvet som användes för resurs gruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Ange vilken typ av redundans som ska användas för valv lagringen.

   * Du kan använda [Lokalt Redundant lagring](../storage/common/storage-redundancy-lrs.md) eller [Geo-redundant lagring](../storage/common/storage-redundancy-grs.md).
   * I följande exempel anges alternativet **-BackupStorageRedundancy** för[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd för **testvault** som är inställt på **interredundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Utdata ser ut ungefär så här. Observera att den associerade resurs gruppen och platsen tillhandahålls.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ange valv kontext

Lagra Valve-objektet i en variabel och ange valv kontexten.

* Många Azure Backup-cmdletar kräver att Recovery Services Vault-objektet är inmatat, så det är praktiskt att lagra valvet i en variabel.
* Valvets sammanhang är typen av data som skyddas i valvet. Ange den med [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). När kontexten har angetts gäller den för alla efterföljande cmdletar.

I följande exempel anges valv kontexten för **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID: t

Vi planerar att föråldra inställningen för valv kontext i enlighet med Azure PowerShell rikt linjer. I stället kan du lagra eller hämta valv-ID: t och skicka det till relevanta kommandon. Så om du inte har angett valv kontexten eller vill ange kommandot som ska köras för ett visst valv, måste du skicka valv-ID: t till "-vaultID" till alla relevanta kommandon enligt följande:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En säkerhets kopierings princip anger schemat för säkerhets kopior och hur länge återställnings punkter för säkerhets kopiering ska behållas:

* En säkerhets kopierings princip är associerad med minst en bevarande princip. En bevarande princip definierar hur länge en återställnings punkt ska sparas innan den tas bort.
* Visa standard kvarhållning av säkerhets kopierings princip med [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visa standard schema för säkerhets kopiering med [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Du använder cmdleten [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) för att skapa en ny säkerhets kopierings princip. Du har angett schema-och bevarande princip objekt.

Som standard definieras en start tid i objektet Schemalägg princip. Använd följande exempel för att ändra start tiden till önskad start tid. Den önskade start tiden ska också vara i UTC-tid. I exemplet nedan förutsätter du att den önskade start tiden är 01:00 AM UTC för dagliga säkerhets kopieringar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Du behöver bara ange start tiden i 30 minuter. I ovanstående exempel kan det bara vara "01:00:00" eller "02:30:00". Start tiden får inte vara "01:15:00"

I följande exempel lagras schema principen och bevarande principen i variabler. Den använder sedan variablerna som parametrar för en ny princip (**NewAFSPolicy**). **NewAFSPolicy** tar en daglig säkerhets kopiering och behåller den i 30 dagar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata ser ut ungefär så här.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Aktivera säkerhets kopiering

När du har definierat säkerhets kopierings principen kan du aktivera skyddet för Azure-filresursen med hjälp av principen.

### <a name="retrieve-a-backup-policy"></a>Hämta en princip för säkerhets kopiering

Du hämtar relevant princip objekt med [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Använd denna cmdlet för att hämta en princip eller för att visa de principer som är kopplade till en arbets belastnings typ.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Hämta en princip för en arbets belastnings typ

I följande exempel hämtas principer för **migreringsåtgärden**för arbets belastnings typen.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Utdata ser ut ungefär så här.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Tids zonen för fältet **BackupTime** i PowerShell är Universal Coordinated Time (UTC). När tiden för säkerhets kopieringen visas i Azure Portal justeras tiden till din lokala tidszon.

### <a name="retrieve-a-specific-policy"></a>Hämta en speciell princip

Följande princip hämtar säkerhets kopierings principen med namnet **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Aktivera säkerhets kopiering och tillämpa princip

Aktivera skydd med [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). När principen är associerad med valvet utlöses säkerhets kopieringarna enligt princip schemat.

I följande exempel aktive ras skyddet för Azure-filresurs- **testAzureFileShare** i Storage Account **testStorageAcct**, med principen **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Kommandot väntar tills det konfigurera skydds jobbet har avslut ATS och ger liknande utdata som visas.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Viktigt meddelande – identifiering av säkerhets kopierings objekt för AFS-säkerhetskopieringar

I det här avsnittet beskrivs en viktig ändring i AFS-säkerhetskopiering som förberedelse för GA.

När du aktiverar säkerhets kopiering för AFS tillhandahåller användaren kundens egna fil resurs namn som entitetsnamnet och ett säkerhets kopierings objekt skapas. Säkerhets kopie objektets ' name ' är en unik identifierare som skapats av Azure Backup-tjänsten. Vanligt vis omfattar identifieraren användarvänligt namn. Men för att hantera det viktiga scenariot för mjuk borttagning, där en fil resurs kan tas bort och en annan fil resurs kan skapas med samma namn, är den unika identiteten för Azure-filresurs nu ett ID i stället för kundens egna namn. För att kunna veta den unika identiteten/namnet på varje objekt, kör du ```Get-AzRecoveryServicesBackupItem``` bara kommandot med de relevanta filtren för BackupManagementType och WorkloadType för att hämta alla relevanta objekt och observera sedan fältet namn i det returnerade PS-objektet/svaret. Vi rekommenderar alltid att du listar objekt och sedan hämtar deras unika namn från fältet "namn" som svar. Använd det här värdet för att filtrera objekten med parametern "namn". Använd annars FriendlyName-parametern för att hämta objektet med det kundens egna namn/identifierare.

> [!WARNING]
> Kontrol lera att PS-versionen har uppgraderats till den lägsta versionen för "AZ. RecoveryServices 2.6.0" för AFS-säkerhetskopieringar. Med den här versionen är friendlyName-filtret tillgängligt för ```Get-AzRecoveryServicesBackupItem``` -kommando. Överför Azure-filresursens namn till friendlyName-parametern. Om du skickar namnet på Azure-filresursen till parametern "namn", genererar den här versionen en varning för att skicka det egna namnet till en egen namn parameter. Om du inte installerar den här lägsta versionen kan det leda till att befintliga skript Miss lyckas. Installera den lägsta versionen av PS med följande kommando.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Utlös en säkerhets kopiering på begäran

Använd [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) för att köra en säkerhets kopiering på begäran för en skyddad Azure-filresurs.

1. Hämta lagrings kontot från behållaren i valvet som innehåller dina säkerhets kopierings data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Om du vill starta ett säkerhets kopierings jobb får du information om Azure-filresursen med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Kör en säkerhets kopiering på begäran med[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Kör säkerhets kopiering på begäran på följande sätt:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Kommandot returnerar ett jobb med ett ID som ska spåras, som du ser i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Ögonblicks bilder av Azure-filresurser används när säkerhets kopieringen görs, så vanligt vis jobbet slutförs när kommandot returnerar utdata.

### <a name="using-a-runbook-to-schedule-backups"></a>Använda en Runbook för att schemalägga säkerhets kopieringar

Om du letar efter exempel skript kan du referera till [exempel skriptet på GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) med hjälp av en Azure Automation Runbook.

>[!NOTE]
> Säkerhets kopierings principen för Azure-filresurs har nu stöd för att konfigurera säkerhets kopiering med dagliga/veckovis/månads Visa per månad.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](backup-afs.md) att säkerhetskopiera Azure Files i Azure Portal.
