---
title: Säkerhetskopiera Azure-filer med PowerShell
description: I den här artikeln kan du läsa om hur du säkerhetskopierar Azure-filer med hjälp av Azure Backup-tjänsten och PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273545"
---
# <a name="back-up-azure-files-with-powershell"></a>Säkerhetskopiera Azure-filer med PowerShell

I den här artikeln beskrivs hur du använder Azure PowerShell för att säkerhetskopiera en Azure Files-filresurs med hjälp av ett [Azure Backup](backup-overview.md) Recovery Services-valv.

I den här artikeln beskrivs hur du:

> [!div class="checklist"]
>
> * Konfigurera PowerShell och registrera Azure Recovery Services Provider.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhetskopiering för en Azure-filresurs.
> * Kör ett säkerhetskopieringsjobb.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* Läs om förhandsversionsfunktionerna för [säkerhetskopiering av Azure-filresurser](backup-afs.md).
* Granska PowerShell-objekthierarkin för Återställningstjänster.

## <a name="recovery-services-object-hierarchy"></a>Objekthierarki för Återställningstjänster

Objekthierarkin sammanfattas i följande diagram.

![Objekthierarki för Återställningstjänster](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska [cmdletreferensen](/powershell/module/az.recoveryservices) **Az.RecoveryServices** i Azure-biblioteket.

## <a name="set-up-and-install"></a>Konfigurera och installera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera PowerShell enligt följande:

1. [Ladda ner den senaste versionen av Az PowerShell](/powershell/azure/install-az-ps). Den minsta version som krävs är 1.0.0.

> [!WARNING]
> Den minsta version av PS som krävs för förhandsversionen var "Az 1.0.0". På grund av kommande ändringar för GA kommer den minsta PS-versionen som krävs att vara "Az.RecoveryServices 2.6.0". Det är mycket viktigt att uppgradera alla befintliga PS-versioner till den här versionen. Annars bryts de befintliga skripten efter GA. Installera den minsta versionen med följande PS-kommandon

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Hitta Azure Backup PowerShell-cmdletar med det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Granska alias och cmdlets för Azure Backup, Azure Site Recovery och Recovery Services-valvet visas. Här är ett exempel på vad du kan se. Det är inte en komplett lista över cmdlets.

    ![Lista över cmdlets för återställningstjänster](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Logga in på ditt **Azure-konto med Connect-AzAccount**.
5. På webbsidan som visas uppmanas du att mata in dina kontouppgifter.

    * Alternativt kan du inkludera dina kontouppgifter som en parameter i cmdleten **Connect-AzAccount** med **-Autentiseringsuppgifter**.
    * Om du är en CSP-partner som arbetar för en klientorganisations räkning anger du kunden som klient med hjälp av deras klientid eller klient primära domännamn. Ett exempel är **Connect-AzAccount -Tenant** fabrikam.com.

6. Associera prenumerationen som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Om du använder Azure Backup för första gången använder du cmdleten **Register-AzResourceProvider** för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Kontrollera att de registrerade leverantörerna:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Kontrollera att **RegistrationState** ändras till **Registrerad**. Om den inte gör det, kör **Register-AzResourceProvider** cmdlet igen.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Valvet för återställningstjänster är en Resource Manager-resurs, så du måste placera det i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med cmdleten **New-AzResourceGroup.** När du skapar en resursgrupp anger du namn och plats för resursgruppen.

Följ dessa steg för att skapa ett Recovery Services-valv.

1. Ett valv placeras i en resursgrupp. Om du inte har en befintlig resursgrupp skapar du en ny med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). I det här exemplet skapar vi en ny resursgrupp i regionen Västra USA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Använd cmdleten [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) för att skapa valvet. Ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Ange vilken typ av redundans som ska användas för arkivlagringen.

   * Du kan använda [lokalt redundant lagring](../storage/common/storage-redundancy-lrs.md) eller [geoupptundsst lagring](../storage/common/storage-redundancy-grs.md).
   * I följande exempel anges alternativet **-BackupStorageRedundancy** för[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd för **testvault** inställt på **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valven i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Utdata liknar följande. Observera att den associerade resursgruppen och platsen tillhandahålls.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ange valvkontext

Lagra arkivobjektet i en variabel och ange arkivkontexten.

* Många Azure Backup-cmdlets kräver Valvet för Återställningstjänster som indata, så det är praktiskt att lagra valvobjektet i en variabel.
* Valvets sammanhang är typen av data som skyddas i valvet. Ställ in den med [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). När kontexten har ställts in gäller den för alla efterföljande cmdlets.

I följande exempel anges valvkontexten för **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID:et

Vi planerar att ta bort kontextinställningen för valvet i enlighet med Azure PowerShell-riktlinjer. I stället kan du lagra eller hämta valv-ID:t och skicka det till relevanta kommandon. Så om du inte har angett valvkontexten eller vill ange kommandot för att köra för ett visst valv, skickar du valv-ID:t som "-vaultID" till alla relevanta kommandon enligt följande:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En princip för säkerhetskopiering anger schemat för säkerhetskopieringar och hur länge återställningspunkter för säkerhetskopiering ska behållas:

* En princip för säkerhetskopiering är associerad med minst en bevarandeprincip. En bevarandeprincip definierar hur länge en återställningspunkt behålls innan den tas bort.
* Visa standardbehållningen av principer för säkerhetskopiering med [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visa standardschemat för säkerhetskopieringsprincipen med [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Du kan använda cmdleten [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) för att skapa en ny princip för säkerhetskopiering. Du matar in schema- och bevarandeprincipobjekten.

Som standard definieras en starttid i schemaprincipobjektet. Använd följande exempel för att ändra starttiden till önskad starttid. Den önskade starttiden bör också vara i UTC. I exemplet nedan förutsätter att önskad starttid är 01:00 UTC för dagliga säkerhetskopior.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Du behöver bara ange starttiden på 30 minuter. I exemplet ovan kan det bara vara "01:00:00" eller "02:30:00". Starttiden kan inte vara "01:15:00"

I följande exempel lagras schemaprincipen och bevarandeprincipen i variabler. Sedan används dessa variabler som parametrar för en ny princip (**NewAFSPolicy**). **NewAFSPolicy** tar en daglig säkerhetskopia och behåller den i 30 dagar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata liknar följande.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Aktivera säkerhetskopiering

När du har definierat säkerhetskopieringsprincipen kan du aktivera skyddet för Azure-filresursen med hjälp av principen.

### <a name="retrieve-a-backup-policy"></a>Hämta en princip för säkerhetskopiering

Du hämtar det relevanta principobjektet med [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Använd den här cmdleten för att hämta en specifik princip eller för att visa de principer som är associerade med en arbetsbelastningstyp.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Hämta en princip för en arbetsbelastningstyp

I följande exempel hämtas principer för arbetsbelastningstypen **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Utdata liknar följande.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Tidszonen för **fältet BackupTime** i PowerShell är universell koordinerad tid (UTC). När säkerhetskopieringstiden visas i Azure-portalen justeras tiden till din lokala tidszon.

### <a name="retrieve-a-specific-policy"></a>Hämta en specifik princip

Följande princip hämtar säkerhetskopieringsprincipen **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Aktivera säkerhetskopiering och tillämpa princip

Aktivera skydd med [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). När principen är associerad med valvet utlöses säkerhetskopior i enlighet med principschemat.

I följande exempel kan skydd för **Azure-filresurstestAzureFileShare** i lagringskontotestStoracct med principen **dailyafs**. **testStorageAcct**

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Kommandot väntar tills konfigurationsskyddsjobbet är klart och ger en liknande utdata, som visas.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Viktigt meddelande - Identifiering av säkerhetskopieringsobjekt för AFS-säkerhetskopior

I det här avsnittet beskrivs en viktig förändring i AFS-säkerhetskopiering inför GA.

När du aktiverar säkerhetskopieringen för AFS tillhandahåller användaren det kundvänliga filresursnamnet som entitetsnamn och ett säkerhetskopieringsobjekt skapas. Säkerhetskopieringsobjektets namn är en unik identifierare som skapats av Azure Backup-tjänsten. Vanligtvis innebär identifieraren ett användarvänligt namn. Men för att hantera det viktiga scenariot med mjuk borttagning, där en filresurs kan tas bort och en annan filresurs kan skapas med samma namn, blir den unika identiteten för Azure-filresurs nu ett ID i stället för kundvänligt namn. För att känna till den unika identiteten/namnet ```Get-AzRecoveryServicesBackupItem``` på varje objekt kör du bara kommandot med relevanta filter för backupManagementType och WorkloadType för att hämta alla relevanta objekt och sedan observera namnfältet i det returnerade PS-objektet/svaret. Det rekommenderas alltid att lista objekt och sedan hämta deras unika namn från "namn" fält som svar. Använd det här värdet om du vill filtrera objekten med parametern "Namn". Annars använder du parametern FriendlyName för att hämta artikeln med det kundvänliga namn/identifierare.

> [!WARNING]
> Kontrollera att PS-versionen uppgraderas till den lägsta versionen för Az.RecoveryServices 2.6.0 för AFS-säkerhetskopior. Med den här versionen är filtret ```Get-AzRecoveryServicesBackupItem``` "friendlyName" tillgängligt för kommando. Skicka azure-filresursnamnet till parametern friendlyName. Om du skickar Azure File Share-namnet till parametern "Namn" genererar den här versionen en varning för att skicka det här egna namnet till parametern eget namn. Om du inte installerar den här minimiversionen kan det leda till att befintliga skript inte fungerar. Installera den lägsta versionen av PS med följande kommando.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Utlösa en säkerhetskopiering på begäran

Använd [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) för att köra en säkerhetskopiering på begäran för en skyddad Azure-filresurs.

1. Hämta lagringskontot från behållaren i valvet som innehåller dina säkerhetskopierade data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Om du vill starta ett säkerhetskopieringsjobb får du information om Azure-filresursen med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Kör en säkerhetskopiering på begäran med[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Kör säkerhetskopieringen på begäran enligt följande:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Kommandot returnerar ett jobb med ett ID som ska spåras, vilket visas i följande exempel.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Ögonblicksbilder av Azure-filresurs används medan säkerhetskopiorna tas, så vanligtvis slutförs jobbet när kommandot returnerar utdata.

### <a name="using-on-demand-backups-to-extend-retention"></a>Använda säkerhetskopiering på begäran för att utöka kvarhållningen

Säkerhetskopiering på begäran kan användas för att behålla dina ögonblicksbilder i 10 år. Schemaläggare kan användas för att köra PowerShell-skript på begäran med vald kvarhållning och därmed ta ögonblicksbilder med jämna mellanrum varje vecka, månad eller år. När du tar regelbundna ögonblicksbilder, se [begränsningarna för säkerhetskopiering på begäran](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) med Azure-säkerhetskopiering.

Om du letar efter exempelskript kan du referera till exempelskriptet på GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) med Hjälp av Azure Automation-runbook som gör att du kan schemalägga säkerhetskopior regelbundet och behålla dem även upp till 10 år.

> [!WARNING]
> Kontrollera att PS-versionen uppgraderas till den lägsta versionen för Az.RecoveryServices 2.6.0 för AFS-säkerhetskopior i dina automatiseringskörningsböcker. Du måste ersätta den gamla AzureRM-modulen med Az-modulen. Med den här versionen är filtret ```Get-AzRecoveryServicesBackupItem``` "friendlyName" tillgängligt för kommando. Skicka azure-filresursnamnet till parametern friendlyName. Om du skickar azure-filresursnamnet till parametern "Namn" genererar den här versionen en varning om att skicka det här egna namnet till parametern eget namn.

## <a name="next-steps"></a>Nästa steg

[Läs mer om](backup-afs.md) säkerhetskopiering av Azure-filer i Azure-portalen.
