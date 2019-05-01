---
title: 'Azure Backup: Säkerhetskopiera och återställa SQL-databaser i Azure virtuella datorer med Azure Backup och PowerShell'
description: Säkerhetskopiera och återställa SQL-databaser i Azure virtuella datorer med Azure Backup och PowerShell.
services: backup
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL,
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 3a424335a1e7d7775f6be0980e7009669e354ea7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717899"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Säkerhetskopiera och återställa SQL-databaser i Azure virtuella datorer med PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att säkerhetskopiera och återställa en SQL-databas i en virtuell Azure-dator med hjälp av [Azure Backup](backup-overview.md) Recovery Services-valv.

I den här självstudien beskrivs hur du:

> [!div class="checklist"]
> * Konfigurera PowerShell och registrera Azure Recovery Services-providern.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhetskopiering för SQL-databas inom en Azure-dator.
> * Köra ett säkerhetskopieringsjobb.
> * Återställa en säkerhetskopierade SQL DB.
> * Övervaka säkerhetskopierings- och återställningsjobb.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* Läs mer om funktionerna för [säkerhetskopiering av SQL-databaser i virtuella Azure-datorer](backup-azure-sql-database.md#before-you-start).
* Granska PowerShell objekthierarkin för Recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services-objekthierarkin

Objekthierarkin summeras i följande diagram.

![Recovery Services-objekthierarkin](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska den **Az.RecoveryServices** [cmdlet-referens för](/powershell/module/az.recoveryservices) referens i Azure-biblioteket.

### <a name="set-up-and-install"></a>Konfigurera och installera

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurera PowerShell på följande sätt:

1. [Hämta den senaste versionen av Az PowerShell](/powershell/azure/install-az-ps). Den lägsta versionen är 1.5.0.

2. Hitta Azure Backup PowerShell-cmdlets med det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Granska alias och cmdlets för Azure Backup och Recovery Services-valvet. Här är ett exempel på vad som kan visas. Det är inte en fullständig lista över cmdlets.

    ![Lista över Recovery Services-cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Logga in på ditt Azure-konto med **Connect AzAccount**.
5. På webbsidan som visas, uppmanas du att ange autentiseringsuppgifterna för ditt konto.

    * Alternativt kan du kan inkludera autentiseringsuppgifterna för ditt konto som en parameter i den **Connect AzAccount** cmdlet med **-Credential**.
    * Om du är en CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras primära domännamn tenantID eller -klient. Ett exempel är **Connect AzAccount-klient** fabrikam.com.

6. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Om du använder Azure Backup för första gången kan du använda den **registrera AzResourceProvider** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Kontrollera att de har registrerats:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. I utdata från kommandot kontrollerar du att **RegistrationState** ändras till **registrerad**. Om det inte, kör den **registrera AzResourceProvider** cmdlet igen.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Följ dessa steg om du vill skapa ett Recovery Services-valv.

Recovery Services-valvet är en Resource Manager-resurs, så måste du placera det inom en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en resursgrupp med det **New AzResourceGroup** cmdlet. När du skapar en resursgrupp kan du ange namn och plats för resursgruppen.

1. Ett valv placeras i en resursgrupp. Om du inte har en befintlig resurs, skapa en ny med den [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). I det här exemplet skapar vi en ny resursgrupp i regionen USA, västra.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Använd den [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet för att skapa valvet. Ange samma plats för valvet som har använts för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Ange vilken typ av redundans för lagring för valvet.

    * Du kan använda [lokalt redundant lagring](../storage/common/storage-redundancy-lrs.md) eller [geo-redundant lagring](../storage/common/storage-redundancy-grs.md).
    * I följande exempel anges den **- BackupStorageRedundancy** för den[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd för **testvault** inställd  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Du kan visa alla valv i prenumerationen [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Utdata liknar följande. Tillhörande resursgrupp och plats har angetts.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ange valvets sammanhang

Store vault-objekt i en variabel och ange valvets sammanhang.

* Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata, så det är lämpligt att lagra valvobjekt i en variabel.
* Valvets sammanhang är typen av data som skyddas i valvet. Konfigurera den med [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). När du har angett kontexten gäller det alla efterkommande cmdletar.

I följande exempel anger valvkontexten för **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID

Planerar vi att avveckla valvkontexten inställningen i enlighet med riktlinjerna för Azure PowerShell. I stället kan du lagra eller hämta valv-ID och skicka dem till relevanta kommandon på följande sätt:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En princip för säkerhetskopiering anger schemat för säkerhetskopiering och hur länge säkerhetskopierade återställningspunkter ska finnas:

* En princip för säkerhetskopiering är associerad med minst en bevarandeprincip. En bevarandeprincip definierar hur länge en återställningspunkt sparas innan de tas bort.
* Visa standard säkerhetskopieringsprincip kvarhållning med [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visa standard säkerhetskopieringsprincip schema med [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Du använder den [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet för att skapa en ny säkerhetskopieringsprincip. Du kan ange principobjekt schema och kvarhållning.

I följande exempel lagrar schema-principen och bevarandeprincipen i variabler. Därefter använder de här variablerna som parametrar för en ny princip (**NewSQLPolicy**). **NewSQLPolicy** tar en daglig ”fullständig” säkerhetskopiering, bibehålls i 180 dagar och tar en säkerhetskopia varannan timme

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata liknar följande.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Aktivera säkerhetskopiering

### <a name="registering-the-sql-vm"></a>Registrera SQL VM

Backup-tjänsten kan ansluta till dessa Azure Resource Manager-resurser och hämta relevant information för Virtuella Azure-säkerhetskopieringar och Azure-filresurser. Eftersom SQL är ett program i en Azure-dator, måste Backup-tjänsten behörighet att komma åt programmet och hämta nödvändig information. För att göra det, måste du *”registrera'* Azure VM som innehåller SQL-program med ett Recovery services-valv. Du kan skydda SQL-databaser till den vault endast när du registrerar en SQL-VM med ett valv. Använd [registrera AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS-cmdleten för att registrera den virtuella datorn.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Kommandot returnerar en ”säkerhetskopiering behållare” för den här resursen och status 'registreras'

> [!NOTE]
> Om du inte är parametern force, uppmanas användaren att bekräfta med texten ”vill du inaktivera skyddet för den här behållaren”. Ignorera den här texten och säg ”Y” för att bekräfta. Det här är ett känt problem och vi arbetar för att ta bort texten och behovet av parametern force

### <a name="fetching-sql-dbs"></a>Hämtar SQL-databaser

När registreringen är klar, kommer Backup-tjänsten att visa en lista över alla tillgängliga SQL-komponenter i den virtuella datorn. Visa alla SQL-komponenter än som ska säkerhetskopieras till valvet användningen [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS-cmdlet

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

Utdata visar alla oskyddade SQL-komponenter för samtliga SQL virtuella datorer har registrerats i valvet med typen av konfigurationsobjekt och servernamn. Du kan filtrera till en viss SQL VM ytterligare genom att skicka den ”-behållaren” parametern eller Använd en kombination av ”namn” och ”servernamn, tillsammans med ItemType flagga för att komma fram till ett unikt SQL-objekt.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Konfigurera säkerhetskopiering

Nu när vi har nödvändiga SQL DB och principen med som den behöver säkerhetskopieras kan vi använda den [aktivera AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet för att konfigurera säkerhetskopiering för den här SQL-databasen.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Kommandot väntar tills konfigurera säkerhetskopieringen har slutförts och returnerar följande utdata.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Hämtar nya SQL-databaser

När datorn har registrerats, hämtar Backup-tjänsten information om databaser som är sedan tillgängliga. Om användaren lägger till SQL DBs/SQL-instanser till den registrerade datorn senare, som krävs för att utlösa manuellt tjänsten backup för att utföra en ny förfrågan om du att hämta alla oskyddade databaser (inklusive de nyligen tillagda) igen. Använd den [initiera AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS-cmdleten på SQL-VM att utföra en ny förfrågan. Kommandot väntar tills åtgärden har slutförts. Senare använda den [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS-cmdlet för att hämta listan över senaste oskyddade SQL-komponenter

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

När de relevanta objekt som ska skyddas hämtas, aktivera säkerhetskopiorna som finns beskrivet i de [ovan avsnittet](#configuring-backup).
Om något inte vill manuellt identifiera nya databaser, de kan välja autoprotection enligt beskrivningen [nedan](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Enable AutoProtection

En användare kan konfigurera säkerhetskopiering så att alla databaser som har lagts till i framtiden skyddas automatiskt med en viss princip. Om du vill aktivera autoprotection använder [aktivera AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS-cmdlet.

Eftersom instruktionen är att säkerhetskopiera alla framtida databaser och åtgärden utförs på en SQLInstance nivå.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

När autoprotection avsikten ges förfrågan in på datorn för att hämta nyligen har lagts till databaser sker som en schemalagd bakgrundsaktivitet var åttonde timme.

## <a name="restore-sql-dbs"></a>Återställa SQL-databaser

Azure Backup kan återställa SQL Server-databaser som körs på virtuella Azure-datorer på följande sätt:

1. Återställa till ett visst datum eller tid (till andra) med hjälp av säkerhetskopieringar av transaktionsloggen. Azure Backup anger automatiskt den lämpliga fullständig differentiella säkerhetskopieringen och kedja av säkerhetskopior som krävs för att återställa baserat på den valda tid.
2. Återställa en fullständig eller Differentiell säkerhetskopia att återställa till en specifik återställningspunkt.

Kontrollera kraven nämns [här](restore-sql-database-azure-vm.md#prerequisites) innan du återställer SQL-databaser.

Först hämta den relevanta säkerhetskopierade SQL-databas med den [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS-cmdlet.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Hämta relevanta återställningstiden

Som vi nämnt ovan kan användare kan återställa säkerhetskopierade SQL-databas till en fullständig/differentiell kopia **eller** till en logg i tidpunkt.

#### <a name="fetch-distinct-recovery-points"></a>Hämta distinkta återställningspunkter

Använd [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) att hämta återställningspunkter för olika (fullständig/differentiell) för en säkerhetskopierad SQL-databas.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = Get-Date.ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Utdata liknar följande exempel

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Använd ”RecoveryPointId”-filter eller en matris-filter för att hämta den relevanta återställningspunkten.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Hämta point-in-time-återställningspunkt

Om du vill återställa databasen till en viss point-in-time, använda [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS-cmdlet. Cmdleten returnerar en lista över datum som representerar start- och sluttider för en obruten, kontinuerlig loggkedjan för att säkerhetskopiera SQL-objekt. Den önskade punkt i tiden ska vara i intervallet.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Utdata ska vara detsamma som i följande exempel.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Ovanstående utdata innebär att användaren kan återställa till valfri punkt i tiden mellan visas starttid och sluttid. Tiderna är i UTC. Skapa alla point-in-time i PS som ligger inom det intervallet som anges ovan.

> [!NOTE]
> När en logg i tidpunkt valts för återställning, behöver användaren inte ange startpunkt d.v.s., fullständig säkerhetskopiering som databasen återställs från. Azure Backup-tjänsten tar hand om hela återställningsplanen, dvs. som fullständig säkerhetskopiering för att välja vad loggsäkerhetskopior för att tillämpa osv.

### <a name="determine-recovery-configuration"></a>Fastställa återställningskonfiguration

Vid återställning av SQL-databas stöds följande scenarion för återställning.

1. Åsidosätta säkerhetskopierade SQL-databas med data från en annan återställningspunkt - OriginalWorkloadRestore
2. Återställa SQL-databas som en ny databas i samma SQL-instans - AlternateWorkloadRestore
3. Återställa SQL-databas som en ny databas i en annan SQL-instans i en annan SQL VM - AlternateWorkloadRestore

När du hämtar den relevanta återställningspunkten (distinkta eller logga point-in-time), Använd [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS-cmdlet för att hämta recovery config objekt enligt önskad återställningsplanen.

#### <a name="original-workload-restore"></a>Den ursprungliga arbetsbelastning återställning

Om du vill åsidosätta den säkerhetskopierade databasen med data från återställningspunkten bara ange flaggan rätt och relevanta återställningspunkten som visas i följande exempel.

##### <a name="original-restore-with-distinct-recovery-point"></a>Ursprungliga återställning med distinkta återställningspunkt

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Ursprungliga återställning med log point-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternativa arbetsbelastning återställning

> [!IMPORTANT]
> En säkerhetskopierade SQL DB kan återställas som en ny databas till en annan SQLInstance endast i en Azure virtuell dator som har registrerats i valvet.

Som beskrivs ovan, om målet SQLInstance ligger inom en annan virtuell Azure-dator, se till att det [har registrerats i valvet](#registering-the-sql-vm) och relevanta SQLInstance visas som ett objekt som kan skyddas.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Sedan bara skicka relevant återställningspunkt, målinstansen av SQL med flaggan rätt som visas nedan.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternativa återställning med distinkta återställningspunkt

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternativa återställning med log point-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Sista recovery point configuration-objekt som hämtats från [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS-cmdlet har all relevant information för återställning och är enligt nedan.

````powershell
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
````

Du kan redigera de återställda DB namn, OverwriteWLIfpresent, NoRecoveryMode och targetPhysicalPath fält. Få mer information om målfilsökvägar enligt nedan.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Ange de relevanta PS-egenskaperna som strängvärden som visas nedan.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

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
````

> [!IMPORTANT]
> Se till att det slutgiltiga återställningsmålet config-objektet har alla nödvändiga och rätt värden eftersom återställningen kommer att baseras på config-objektet.

### <a name="restore-with-relevant-configuration"></a>Återställa med relevanta konfiguration

När konfigurationsobjekt relevanta återställning hämtas och verifierats, använda den [återställning AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS-cmdlet för att starta återställningen.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Återställningsåtgärden returnerar ett jobb som ska spåras.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Hantera SQL-säkerhetskopior

### <a name="on-demand-backup"></a>Säkerhetskopiering på begäran

När säkerhetskopieringen har aktiverats för en databas, användare kan även utlösa en säkerhetskopiering på begäran för DB med hjälp av [Backup AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS-cmdlet. I följande exempel utlöser en fullständig säkerhetskopiering för en SQL-databas med komprimering aktiverat och en fullständig säkerhetskopiering ska behållas i 60 dagar.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Ad hoc backup-kommandot returnerar ett jobb som ska spåras.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Om utdata är borttappad eller om du vill hämta den relevanta jobb-ID [hämta listan över jobb](#track-azure-backup-jobs) från Azure Backup-tjänsten och sedan spåra den och dess egenskaper.

### <a name="change-policy-for-backup-items"></a>Ändra principen för säkerhetskopieringsobjekt

Användaren kan ändra befintlig princip eller ändra principen för det säkerhetskopierade objektet från Princip1 till Princip2. Om du vill växla principer för ett säkerhetskopierade objekt bara hämta relevanta principen och säkerhetskopiera objekt och Använd den [aktivera AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) med säkerhetskopieringsobjekt som parameter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Kommandot väntar tills konfigurera säkerhetskopieringen har slutförts och returnerar följande utdata.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Registrera SQL-datorer

> [!WARNING]
> Se till att läsa följande [dokumentet](backup-sql-server-azure-troubleshoot.md#re-registration-failures) Fel Symptom och de orsaker innan du försöker omregistrering

Hämta den relevanta behållaren för säkerhetskopian och skickar den till cmdleten register för att utlösa omregistrering av SQL-VM.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Sluta skydda

#### <a name="retain-data"></a>Kvarhåll data

Om användare vill sluta skydda, använder de den [inaktivera AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS-cmdlet. Detta förhindrar att de schemalagda säkerhetskopieringarna men data som säkerhetskopieras tills nu behålls alltid.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>ta bort säkerhetskopierade data

För att ta bort säkerhetskopierade data lagrade i valvet, ska du lägga till ”-RemoveRecoveryPoints' flaggan/växlar du till den [inaktivera skydd kommandot](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Inaktivera automatiskt skydd

Om autoprotection har konfigurerats på en SQLInstance användaren kan inaktivera den med hjälp av den [inaktivera AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS-cmdlet.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Avregistrera SQL VM

Om alla databaser på en SQLServer [är inte längre skyddade och inte säkerhetskopierade data finns](#delete-backup-data), användare kan avregistrera SQL VM från det här valvet. Användare kan bara skydda databaser till ett annat valv. Använd [avregistrera AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS-cmdlet för att avregistrera SQL-VM.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Spåra Azure säkerhetskopieringsjobb

Det är viktigt att notera att Azure Backup endast spårar användaren utlöses jobb i SQL-säkerhetskopiering. Schemalagda säkerhetskopieringar (inklusive säkerhetskopior) visas inte i portal/powershell. Men om alla schemalagda jobb misslyckas, en [avisering om bandsäkerhetskopiering](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) genereras och visas i portalen. [Använda Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) att spåra alla schemalagda uppgifter och annan relevant information.

Användare kan spåra ad hoc/användare som utlöste åtgärder med jobb-ID som returneras i de [utdata](#on-demand-backup) asynkrona jobb som säkerhetskopiering. Använd [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetails?view=azps-1.5.0) PS-cmdlet för att spåra jobb och dess egenskaper.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Hämta listan över adhoc-jobb och deras status från Azure Backup-tjänsten med [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS-cmdlet. I följande exempel returneras alla pågående SQL-jobb.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Om du vill avbryta ett pågående jobb, Använd den [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS-cmdlet.

## <a name="managing-sql-always-on-availability-groups"></a>Hantera SQL alltid på Tillgänglighetsgrupper

Se till att för SQL Always On-Tillgänglighetsgrupper [registrera alla noder](#registering-the-sql-vm) för tillgänglighetsgruppen (AG). När registreringen är klar för alla noder som kan skapas logiskt ett gruppobjekt för SQL-tillgänglighet under objekt som ska skyddas. Databaserna under SQL AG visas som ”SQLDatabase”. Noderna visas som fristående instanser och standard SQL-databaser därunder listas som SQL-databaser.

Låt oss anta en SQL-Tillgänglighetsgrupp har två noder: ”sql-server-0” och ”sql-server-1' och 1 SQL AG DB. När båda dessa noder har registrerats, om användaren [visar en lista över objekt som ska skyddas](#fetching-sql-dbs), visas en lista med följande komponenter

1. En SQL AG-objekt – som kan skyddas objekttyp som SQLAvailabilityGroup
2. En AG SQL DB - skyddsobjekt typ som SQLDatabase
3. SQL-server-0 - skyddsobjekt skriver som SQLInstance
4. SQL-server-1 - skyddsobjekt skriver som SQLInstance
5. Alla standard SQL-databaser (master, model, msdb) under sql-server-0 - skyddsobjekt skriver som SQLDatabase
6. Alla standard SQL-databaser (master, model, msdb) under sql-server-1 - skyddsobjekt skriver som SQLDatabase

SQL-server-0, sql-server-1 också visas som ”AzureVMAppContainer” när [säkerhetskopiering behållare visas](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Hämta bara de relevanta SQL-databasen till [Aktivera säkerhetskopiering](#configuring-backup) och [ad hoc-säkerhetskopiering](#on-demand-backup) och [återställa PS-cmdletar](#restore-sql-dbs) är identiska.
