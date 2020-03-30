---
title: SQL DB i Azure VM-säkerhetskopiering & återställning via PowerShell
description: Säkerhetskopiera och återställa SQL-databaser i virtuella Azure-datorer med Azure Backup och PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131820"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Säkerhetskopiera och återställa SQL-databaser i virtuella Azure-datorer med PowerShell

I den hÃ¤r artikeln beskrivs hur du anvÃ¤nder Azure PowerShell för att säkerhetskopiera och återställa en SQL DB i en Azure VM med [Azure Backup](backup-overview.md) Recovery Services vault.

I den här artikeln beskrivs hur du:

> [!div class="checklist"]
>
> * Konfigurera PowerShell och registrera Azure Recovery Services Provider.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhetskopiering för SQL DB i en Virtuell Azure.Configure backup for SQL DB within an Azure VM.
> * Kör ett säkerhetskopieringsjobb.
> * Återställa en säkerhetskopierad SQL DB.
> * Övervaka säkerhetskopiering och återställning av jobb.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* Läs om funktionsfunktionerna för [säkerhetskopiering av SQL DBs i virtuella Azure-datorer](backup-azure-sql-database.md#before-you-start).
* Granska PowerShell-objekthierarkin för Återställningstjänster.

### <a name="recovery-services-object-hierarchy"></a>Objekthierarki för Återställningstjänster

Objekthierarkin sammanfattas i följande diagram.

![Objekthierarki för Återställningstjänster](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska [cmdletreferensen](/powershell/module/az.recoveryservices) **Az.RecoveryServices** i Azure-biblioteket.

### <a name="set-up-and-install"></a>Konfigurera och installera

Konfigurera PowerShell enligt följande:

1. [Ladda ner den senaste versionen av Az PowerShell](/powershell/azure/install-az-ps). Den minsta version som krävs är 1.5.0.

2. Hitta Azure Backup PowerShell-cmdletar med det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Granska alias och cmdlets för Azure Backup och Recovery Services-valvet. Här är ett exempel på vad du kan se. Det är inte en komplett lista över cmdlets.

    ![Lista över cmdlets för återställningstjänster](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Logga in på ditt **Azure-konto med Connect-AzAccount**.
5. På webbsidan som visas uppmanas du att mata in dina kontouppgifter.

    * Alternativt kan du inkludera dina kontouppgifter som en parameter i cmdleten **Connect-AzAccount** med **-Autentiseringsuppgifter**.
    * Om du är en CSP-partner som arbetar för en klient anger du kunden som klient med hjälp av deras klientid eller klient primära domännamn. Ett exempel är **Connect-AzAccount -Tenant** fabrikam.com.

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

Följ dessa steg för att skapa ett Recovery Services-valv.

Valvet för återställningstjänster är en Resource Manager-resurs, så du måste placera det i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med cmdleten **New-AzResourceGroup.** När du skapar en resursgrupp anger du namn och plats för resursgruppen.

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
    * I följande exempel anges alternativet **-BackupStorageRedundancy** för[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd för **testvault** inställt på **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valven i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Utdata liknar följande. Den associerade resursgruppen och platsen tillhandahålls.

```output
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

Vi planerar att ta bort kontextinställningen för valvet i enlighet med Azure PowerShell-riktlinjer. I stället kan du lagra eller hämta valv-ID:t och skicka det till relevanta kommandon enligt följande:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En princip för säkerhetskopiering anger schemat för säkerhetskopieringar och hur länge återställningspunkter för säkerhetskopiering ska behållas:

* En princip för säkerhetskopiering är associerad med minst en bevarandeprincip. En bevarandeprincip definierar hur länge en återställningspunkt behålls innan den tas bort.
* Visa standardbehållningen av principer för säkerhetskopiering med [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visa standardschemat för säkerhetskopieringsprincipen med [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Du kan använda cmdleten [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) för att skapa en ny princip för säkerhetskopiering. Du matar in schema- och bevarandeprincipobjekten.

Som standard definieras en starttid i schemaprincipobjektet. Använd följande exempel för att ändra starttiden till önskad starttid. Den önskade starttiden bör också vara i UTC. I exemplet nedan förutsätter att önskad starttid är 01:00 UTC för dagliga säkerhetskopior.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Du behöver bara ange starttiden på 30 minuter. I exemplet ovan kan det bara vara "01:00:00" eller "02:30:00". Starttiden kan inte vara "01:15:00"

I följande exempel lagras schemaprincipen och bevarandeprincipen i variabler. Sedan används dessa variabler som parametrar för en ny princip (**NewSQLPolicy**). **NewSQLPolicy** tar en daglig "Full" backup, behåller den i 180 dagar och tar en logg backup varannan timme

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata liknar följande.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Aktivera säkerhetskopiering

### <a name="registering-the-sql-vm"></a>Registrera DEN VIRTUELLA DATORN FÖR SQL

För Azure VM-säkerhetskopior och Azure File-resurser kan säkerhetskopieringstjänsten ansluta till dessa Azure Resource Manager-resurser och hämta relevant information. Eftersom SQL är ett program i en Azure VM, behöver säkerhetskopieringstjänsten behörighet för att komma åt programmet och hämta nödvändig information. För att göra det måste du *"registrera"* den virtuella Azure-datorn som innehåller SQL-programmet med ett recovery services-valv. När du har registrerat en VIRTUELL SQL-dator med ett valv kan du bara skydda SQL DBs till det valvet. Använd [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet för att registrera den virtuella datorn.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Kommandot returnerar en "reservbehållare" för den här resursen och statusen kommer att "registreras"

> [!NOTE]
> Om kraftparametern inte anges uppmanas användaren att bekräfta med texten "Vill du inaktivera skyddet för den här behållaren". Ignorera den här texten och säg "Y" för att bekräfta. Detta är ett känt problem och vi arbetar för att ta bort texten och kravet på kraftparametern.

### <a name="fetching-sql-dbs"></a>Hämtar SQL DBs

När registreringen är klar kan säkerhetskopieringstjänsten lista alla tillgängliga SQL-komponenter i den virtuella datorn. Om du vill visa alla SQL-komponenter som ännu inte har säkerhetskopierats till det här valvet använder du [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

Utdata visar alla oskyddade SQL-komponenter för alla SQL-virtuella datorer som är registrerade i det här valvet med objekttyp och ServerName. Du kan ytterligare filtrera till en viss SQL-virtuell dator genom att skicka parametern "-Container" eller använda kombinationen av "Name" och "ServerName" tillsammans med ItemType-flaggan för att komma fram till ett unikt SQL-objekt.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Konfigurera säkerhetskopiering

Nu när vi har den nödvändiga SQL DB och den princip som den behöver säkerhetskopieras, kan vi använda [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet för att konfigurera säkerhetskopiering för denna SQL DB.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Kommandot väntar tills konfigurera säkerhetskopian är klar och returnerar följande utdata.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Hämtar nya SQL DBs

När datorn är registrerad hämtar säkerhetskopieringstjänsten information om de DBs som är tillgängliga då. Om användaren lägger till SQL DBs /SQL-instanser till den registrerade datorn senare, måste man manuellt utlösa säkerhetskopieringstjänsten för att utföra en ny "förfrågan" för att få alla oskyddade DBs (inklusive de nyligen tillagda) igen. Använd [CMDLET Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS på SQL-virtuella datorn för att utföra en ny förfrågan. Kommandot väntar tills åtgärden är klar. Senare använda [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet för att få en lista över de senaste oskyddade SQL-komponenter

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

När de relevanta skyddsobjekten har hämtats aktiverar du säkerhetskopiorna enligt anvisningarna i [avsnittet ovan](#configuring-backup).
Om man inte vill manuellt upptäcka nya DBs, kan de välja autoprotection som förklaras [nedan](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Aktivera automatisk skydd

En användare kan konfigurera säkerhetskopiering så att alla DBs som läggs till i framtiden automatiskt skyddas med en viss princip. Om du vill aktivera autoskydd använder du [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

Eftersom instruktionen är att säkerhetskopiera alla framtida DBs, utförs åtgärden på en SQLInstance-nivå.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

När autoskydd avsikten ges, förfrågan om maskinen för att hämta nyligen tillagda DBs sker som en schemalagd bakgrundsaktivitet var 8 timmar.

## <a name="restore-sql-dbs"></a>Återställa SQL DBs

Azure Backup kan återställa SQL Server-databaser som körs på virtuella Azure-datorer enligt följande:

* Återställ till ett visst datum eller en viss tid (till det andra) med hjälp av säkerhetskopior av transaktionsloggen. Azure Backup bestämmer automatiskt lämplig fullständig differentiell säkerhetskopiering och kedjan av loggsäkerhetskopior som krävs för att återställa baserat på den valda tiden.
* Återställ en specifik fullständig eller differentiell säkerhetskopiering för att återställa till en viss återställningspunkt.

Kontrollera de förutsättningar som nämns [här](restore-sql-database-azure-vm.md#prerequisites) innan du återställer SQL DBs.

Hämta först relevant säkerhetskopierad SQL DB med [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Hämta relevant återställningstid

Som beskrivits ovan kan användaren återställa den säkerhetskopierade SQL DB till en fullständig/differentiell kopia **eller** till en loggpunkt i tid.

#### <a name="fetch-distinct-recovery-points"></a>Hämta distinkta återställningspunkter

Använd [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) för att hämta distinkta (fullständig/differentiell) återställningspunkter för en säkerhetskopierad SQL DB.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

Utdata liknar följande exempel

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Använd filtret "RecoveryPointId" eller ett matrisfilter för att hämta den relevanta återställningspunkten.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Hämta återställningspunkt för tidpunkt

Om användaren vill återställa DB till en viss point-in-time använder du [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet. Cmdlet returnerar en lista över datum som representerar start- och sluttider för en obruten, kontinuerlig loggkedja för det SQL-säkerhetskopieringsobjektet. Den önskade point-in-time bör vara inom detta intervall.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Utdata liknar följande exempel.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

Ovanstående utdata innebär att användaren kan återställa till valfri punkt i tid mellan den visade starttiden och sluttiden. Tiderna är i UTC. Konstruera alla point-in-time i PS som ligger inom det intervall som visas ovan.

> [!NOTE]
> När en loggpunkt-i-tid har valts för återställning behöver användaren inte ange startpunkten, dvs fullständig säkerhetskopiering som DB återställs från. Azure Backup-tjänsten tar hand om hela återställningsplanen, dvs som fullständig säkerhetskopiering att välja, vilka loggsäkerhetskopior som ska gälla etc.

### <a name="determine-recovery-configuration"></a>Ta reda på återställningskonfiguration

Vid SQL DB-återställning stöds följande återställningsscenarier.

* Åsidosätta säkerhetskopierade SQL DB med data från en annan återställningspunkt - OriginalWorkloadRestore
* Återställa SQL DB som en ny DB i samma SQL-instans - AlternateWorkloadRestore
* Återställa SQL DB som en ny DB i en annan SQL-instans i en annan SQL VM - AlternateWorkloadRestore
* Återställa SQL DB som .bak-filer -RestoreAsFiles

När du har hämtat den relevanta återställningspunkten (distinkt eller logga in-time) använder du [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet för att hämta återställningskonfigurationsobjektet enligt den önskade återställningsplanen.

#### <a name="original-workload-restore"></a>Återställning av den ursprungliga arbetsbelastningen

Om du vill åsidosätta den säkerhetskopierade DB-databasen med data från återställningspunkten anger du bara rätt flagga och den relevanta återställningspunkten som visas i följande exempel.

##### <a name="original-restore-with-distinct-recovery-point"></a>Originalåterställning med distinkt återställningspunkt

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Originalåterställning med loggpunkt i tid

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternativ återställning av arbetsbelastning

> [!IMPORTANT]
> En säkerhetskopierad SQL DB kan återställas som en ny DB till en annan SQLInstance bara, i en Azure VM "registrerad" till det här valvet.

Som beskrivits ovan, om målet SQLInstance ligger inom en annan Azure VM, se till att det är [registrerat i det här valvet](#registering-the-sql-vm) och den relevanta SQLInstance visas som ett skyddat objekt.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Sedan är det bara att passera den relevanta återställningspunkten, mål SQL-instans med rätt flagga som visas nedan.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternativ återställning med distinkt återställningspunkt

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternativ återställning med loggpunkt i tid

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Återställa som filer

Om du vill återställa säkerhetskopierade data som .bak-filer i stället för en databas väljer du alternativet **Återställ som filer.** Säkerhetskopierade SQL DB kan återställas till alla mål-virtuella datorer som är registrerade i det här valvet.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Återställa som filer med olika återställningspunkt

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Återställ som filer med loggpunkt i tid från den senaste fullständiga

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Återställa som filer med loggpunkt i tid från en angiven fullständig

Om du vill ge en specifik fullständig som ska användas för återställning använder du följande kommando:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Konfigurationsobjektet för den slutliga återställningspunkten som hämtats från [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet har all relevant information för återställning och är som visas nedan.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

Du kan redigera de återställda DB-namnet, Överskrivnings-, NoRecoveryMode- och targetPhysicalPath-fälten. Få mer information om målfilens sökvägar som visas nedan.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Ange relevanta PS-egenskaper som strängvärden enligt nedan.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Kontrollera att det slutliga konfigurationsobjektet för återställning har alla nödvändiga och korrekta värden eftersom återställningen baseras på config-objektet.

### <a name="restore-with-relevant-configuration"></a>Återställ med relevant konfiguration

När det relevanta config-objektet för återställning har hämtats och verifierats använder du cmdleten [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS för att starta återställningsprocessen.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

Återställningsåtgärden returnerar ett jobb som ska spåras.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Hantera SQL-säkerhetskopior

### <a name="on-demand-backup"></a>Säkerhetskopiering på begäran

När säkerhetskopiering har aktiverats för en DB kan användaren också utlösa en säkerhetskopiering på begäran för DB med [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet. Följande exempel utlöser en fullständig säkerhetskopia på en SQL DB med komprimering aktiverad och den fullständiga säkerhetskopian bör behållas i 60 dagar.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Kommandot säkerhetskopiering på begäran returnerar ett jobb som ska spåras.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Om utdata går förlorad eller om du vill hämta relevant jobb-ID [hämtar du listan över jobb](#track-azure-backup-jobs) från Azure Backup-tjänsten och spårar den och dess information.

### <a name="change-policy-for-backup-items"></a>Ändra princip för säkerhetskopieringsobjekt

Användaren kan antingen ändra befintlig princip eller ändra principen för det säkerhetskopierade objektet från Princip1 till Princip2. Om du vill växla principer för ett säkerhetskopierat objekt hämtar du relevant princip och säkerhetskopierar objektet och använder kommandot [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) med säkerhetskopieringsobjekt som parameter.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Kommandot väntar tills konfigurera säkerhetskopian är klar och returnerar följande utdata.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Registrera om virtuella SQL-datorer

> [!WARNING]
> Se till att läsa det här [dokumentet](backup-sql-server-azure-troubleshoot.md#re-registration-failures) för att förstå felsymptom och orsaker innan du försöker omregistrering

Om du vill utlösa omregistrering av SQL-datorn hämtar du relevant reservbehållare och skickar den till register-cmdleten.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Sluta skydda

#### <a name="retain-data"></a>Behålla data

Om användaren vill stoppa skyddet kan de använda cmdleten [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Detta stoppar schemalagda säkerhetskopior men de data som säkerhetskopierats hittills behålls för alltid.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

För att helt ta bort lagrade säkerhetskopieringsdata i valvet, bara lägga till "-RemoveRecoveryPoints" flagga / växla till ["inaktivera" skydd kommandot](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Inaktivera automatiskt skydd

Om autoskydd har konfigurerats på en SQLInstance kan användaren inaktivera det med hjälp av [filen Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Avregistrera SQL VM

Om alla DBs på en SQL-server [inte längre är skyddade och det inte finns några säkerhetskopieringsdata](#delete-backup-data)kan användaren avregistrera SQL-virtuella datorn från det här valvet. Först då användaren kan skydda DBs till ett annat valv. Använd [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet för att avregistrera SQL VM.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Spåra Azure Backup-jobb

Det är viktigt att notera att Azure Backup bara spårar användarutlösta jobb i SQL-säkerhetskopiering. Schemalagda säkerhetskopior (inklusive loggsäkerhetskopior) visas inte i portal/powershell. Men om några schemalagda jobb misslyckas genereras en [avisering om säkerhetskopiering](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) och visas i portalen. [Använd Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) för att spåra alla schemalagda jobb och annan relevant information.

Användare kan spåra åtgärder på begäran/användarutlösta åtgärder med JobID som returneras i [utdata från](#on-demand-backup) asynkrona jobb, till exempel säkerhetskopiering. Använd [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet för att spåra jobb och dess detaljer.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Om du vill hämta listan över jobb på begäran och deras status från Azure Backup-tjänsten använder du [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet. I följande exempel returneras alla pågående SQL-jobb.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Om du vill avbryta ett pågående jobb använder du cmdleten [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Hantera SQL Always On Availability groups

För SQL Always On Availability Groups kontrollerar du att [alla noder](#registering-the-sql-vm) i gruppen Tillgänglighet (AG) registreras. När registreringen är klar för alla noder skapas ett SQL-tillgänglighetsgruppobjekt logiskt under skyddsbara objekt. Databaserna under SQL AG kommer att listas som "SQLDatabase". Noderna visas som fristående instanser och standard-SQL-databaserna under dem visas även som SQL-databaser.

Anta till exempel att en SQL AG har två noder: "sql-server-0" och "sql-server-1" och 1 SQL AG DB. När båda dessa noder har registrerats, om användaren [listar de skyddade objekten,](#fetching-sql-dbs)visas följande komponenter

* Ett SQL AG-objekt - skyddad objekttyp som SQLAvailabilityGroup
* En SQL AG DB - skyddad objekttyp som SQLDatabase
* sql-server-0 - skyddad objekttyp som SQLInstance
* sql-server-1 - skyddad objekttyp som SQLInstance
* Alla standard-SQL DBs (master, modell, msdb) under sql-server-0 - skyddad objekttyp som SQLDatabase
* Alla standard-SQL DBs (master, modell, msdb) under sql-server-1 - skyddad objekttyp som SQLDatabase

sql-server-0, sql-server-1 kommer också att listas som "AzureVMAppContainer" när [säkerhetskopior listas](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Bara hämta den relevanta SQL-databasen för att [möjliggöra säkerhetskopiering](#configuring-backup) och [on-demand backup](#on-demand-backup) och [återställa PS cmdlets](#restore-sql-dbs) är identiska.
