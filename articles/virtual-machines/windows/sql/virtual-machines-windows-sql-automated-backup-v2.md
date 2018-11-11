---
title: Automatisk säkerhetskopiering v2 för SQL Server 2016/2017 virtuella Azure-datorer | Microsoft Docs
description: Beskriver funktionen automatisk säkerhetskopiering för SQL Server 2016/2017 virtuella datorer som körs i Azure. Den här artikeln är specifik för virtuella datorer med Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 664a0036b8aa753de9636688d22afff0163f031f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246828"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatisk säkerhetskopiering v2 för virtuella Azure-datorer (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatiserad säkerhetskopiering v2 konfigurerar automatiskt [hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure-dator som kör SQL Server 2016/2017 Standard, Enterprise eller Developer-versioner. På så sätt kan du konfigurera regelbundna databassäkerhetskopieringar som använder varaktiga Azure blob-lagring. Automatiserad säkerhetskopiering v2 beror på den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Förutsättningar
För att använda automatisk säkerhetskopiering v2, gå igenom följande krav:

**Operativsystemet**:

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server version/utgåva**:

- SQLServer 2016: Developer, Standard eller Enterprise
- SQLServer 2017: Developer, Standard eller Enterprise

> [!IMPORTANT]
> Automatiserad säkerhetskopiering v2 fungerar med SQL Server 2016 eller senare. Om du använder SQL Server 2014 kan använda du automatisk säkerhetskopiering v1 för att säkerhetskopiera dina databaser. Mer information finns i [automatisk säkerhetskopiering för SQL Server 2014 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

**Databaskonfiguration**:

- Måldatabaserna måste använda den fullständiga återställningsmodellen. Mer information om effekten av den fullständiga återställningsmodellen säkerhetskopior finns i [säkerhetskopiering Under den fullständiga återställningsmodellen](https://technet.microsoft.com/library/ms190217.aspx).
- Systemdatabaser behöver inte använda fullständiga återställningsmodellen. Om du behöver loggsäkerhetskopior vidtas för modellen eller MSDB, måste du använda fullständiga återställningsmodellen.
- Måldatabaserna måste vara på standardinstansen för SQL Server. SQL Server IaaS-tillägget har inte stöd för namngivna instanser.

> [!NOTE]
> Automatisk säkerhetskopiering är beroende av den **SQL Server IaaS Agent-tillägget**. Aktuell SQL VM-galleriavbildningar lägga till det här tillägget som standard. Mer information finns i [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering v2. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandon.

### <a name="basic-settings"></a>Grundinställningar

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** | Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure virtuell dator som kör SQL Server 2016/2017 Developer, Standard eller Enterprise. |
| **Kvarhållningsperiod** | 1 – 30 dagar (30 dagar) | Antal dagar att behålla säkerhetskopior. |
| **Lagringskonto** | Azure Storage-konto | Ett Azure storage-konto som ska användas för att lagra filer för automatisk säkerhetskopiering i blob storage. En behållare skapas på den här platsen för att lagra säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och databas-GUID. |
| **Kryptering** |Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar kryptering. När kryptering har aktiverats, finns de certifikat som används för att återställa säkerhetskopian i det angivna lagringskontot. Den använder samma **automaticbackup** behållare med samma namngivningskonvention. Om lösenordet ändras, skapas ett nytt certifikat med lösenordet, men det gamla certifikatet förblir för att återställa tidigare säkerhetskopior. |
| **Lösenord** |Lösenordet | Ett lösenord för krypteringsnycklar. Det här lösenordet används bara krävs om kryptering är aktiverat. Om du vill återställa en krypterad säkerhetskopiering måste du ha rätt lösenord och relaterade certifikatet som användes när säkerhetskopian skapades. |

### <a name="advanced-settings"></a>Avancerade inställningar

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **System-databassäkerhetskopieringar** | Aktivera/inaktivera (inaktiverad) | När aktiverat den här funktionen också säkerhetskopierar systemdatabaserna: Master, MSDB och modellen. Kontrollera att de är i fullständigt återställningsläge om du vill att säkerhetskopior som ska vidtas för databaserna MSDB och modell. Loggsäkerhetskopior tas aldrig för huvudserver. Och inga säkerhetskopior tas för TempDB. |
| **Schema för säkerhetskopiering** | Manuell/automatisk (automatisk) | Som standard fastställs automatiskt schemat för säkerhetskopiering baserat på logg-tillväxt. Manuell Säkerhetskopieringsschemat gör att användaren kan ange tidsperioden för säkerhetskopior. I det här fallet ske säkerhetskopieringar endast vid den angivna frekvensen och under den angivna tidsfönstret på en viss dag. |
| **Frekvens för fullständig säkerhetskopiering** | Varje dag/vecka | Frekvensen av fullständiga säkerhetskopieringar. I båda fallen börjar fullständiga säkerhetskopieringar under nästa schemalagda tidsperioden. När vecka har valts kan säkerhetskopior löper under flera dagar tills alla databaser har säkerhetskopierat. |
| **Starttid för fullständig säkerhetskopiering** | 00:00 – 23:00 (01:00) | Starttid för en viss dag under vilken fullständiga säkerhetskopieringar kan göras. |
| **Tidsperiod för fullständig säkerhetskopiering** | 1 – 23 timmar (1 timme) | Varaktigheten för tidsperioden för en viss dag under vilken fullständiga säkerhetskopieringar kan göras. |
| **Frekvens för loggsäkerhetskopiering** | 5 – 60 minuter (60 minuter) | Frekvensen för säkerhetskopior. |

## <a name="understanding-full-backup-frequency"></a>Förstå frekvens för fullständig säkerhetskopiering
Det är viktigt att förstå skillnaden mellan dagliga och veckovisa fullständiga säkerhetskopieringar. Tänk på följande två exempelscenarier.

### <a name="scenario-1-weekly-backups"></a>Scenario 1: Veckovisa säkerhetskopior
Du har en SQL Server VM som innehåller ett antal stora databaser.

På måndag aktiverar du automatisk säkerhetskopiering v2 med följande inställningar:

- Schema för säkerhetskopiering: **manuell**
- Fullständig säkerhetskopieringsfrekvens: **varje vecka**
- Fullständig säkerhetskopiering: **01:00**
- Tidsperiod för fullständig säkerhetskopiering: **1 timme**

Det innebär att nästa tillgängliga fönster för säkerhetskopiering är tisdag kl 1 timme. Vid den tidpunkten börjar automatisk säkerhetskopiering säkerhetskopiera dina databaser en i taget. I det här scenariot är dina databaser tillräckligt stor för att det Slutför fullständiga säkerhetskopieringar för de första par databaserna. Men efter en timme har inte alla databaserna som säkerhetskopierats.

När detta sker börjar automatisk säkerhetskopiering säkerhetskopiera återstående databaser nästa dag, onsdag kl 1 under en timme. Om inte alla databaser har säkerhetskopierats vid den tiden, försök igen nästa dag på samma gång. Detta fortsätter tills alla databaser har säkerhetskopierats.

När den når tisdag igen börjar automatisk säkerhetskopiering säkerhetskopierar alla databaser igen.

Det här scenariot visar att automatisk säkerhetskopiering fungerar bara inom det angivna tidsfönstret och varje databas säkerhetskopieras på en gång per vecka. Detta visar att det är möjligt för säkerhetskopiering till löper under flera dagar i de fall där det inte går att slutföra alla säkerhetskopior i en dag.

### <a name="scenario-2-daily-backups"></a>Scenario 2: Daglig säkerhetskopiering
Du har en SQL Server VM som innehåller ett antal stora databaser.

På måndag aktiverar du automatisk säkerhetskopiering v2 med följande inställningar:

- Schema för säkerhetskopiering: manuell
- Fullständig säkerhetskopieringsfrekvens: varje dag
- Fullständig säkerhetskopiering: 22:00
- Tidsperiod för fullständig säkerhetskopiering: 6 timmar

Det innebär att nästa tillgängliga fönster för säkerhetskopiering är måndag kl 10 i 6 timmar. Vid den tidpunkten börjar automatisk säkerhetskopiering säkerhetskopiera dina databaser en i taget.

Tisdagen vid 10 i 6 timmar och fullständiga säkerhetskopieringar av alla databaser starta sedan igen.

> [!IMPORTANT]
> När du schemalägger dagliga säkerhetskopior, rekommenderar vi att du schemalägger ett brett tidsfönster att se till att alla databaser kan säkerhetskopieras i den här gången. Detta är särskilt viktigt i fall där du har en stor mängd data du säkerhetskopierar.

## <a name="configure-in-the-portal"></a>Konfigurera i portalen

Du kan använda Azure-portalen för att konfigurera automatisk säkerhetskopiering v2 under etableringen eller för befintliga virtuella SQL Server 2016/2017-datorer.

## <a name="configure-for-new-vms"></a>Konfigurera för nya virtuella datorer

Använd Azure-portalen för att konfigurera automatisk säkerhetskopiering v2 när du skapar en ny SQL Server 2016 eller 2017-dator i distributionsmodellen för Resource Manager.

I den **SQL Server-inställningar** väljer **automatisk säkerhetskopiering**. I följande Skärmbild av Azure portal visas den **SQL automatisk säkerhetskopiering** inställningar.

![Automatisk säkerhetskopiering för SQL-konfiguration i Azure-portalen](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automatiserad säkerhetskopiering v2 är inaktiverad som standard.

## <a name="configure-existing-vms"></a>Konfigurera befintliga virtuella datorer

För befintliga SQL Server-datorer, väljer du din SQL Server-dator. Välj sedan den **konfiguration av SQL Server** delen av den virtuella datorn **inställningar**.

![SQL automatisk säkerhetskopiering för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

I den **konfiguration av SQL Server** klickar du på den **redigera** i avsnittet automatisk säkerhetskopiering.

![Konfigurera automatisk säkerhetskopiering för SQL för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

När du är klar klickar du på den **OK** knappen längst ned på den **konfiguration av SQL Server** inställningar för att spara dina ändringar.

Om du aktiverar automatisk säkerhetskopiering för första gången, konfigurerar SQL Server IaaS Agent i bakgrunden i Azure. Under denna tid kanske Azure-portalen inte visar att automatisk säkerhetskopiering är konfigurerad. Vänta några minuter för att agenten ska installeras, konfigureras. Efter det visar Azure-portalen de nya inställningarna.

## <a name="configure-with-powershell"></a>Konfigurera med PowerShell

Du kan använda PowerShell för att konfigurera automatisk säkerhetskopiering v2. Innan du börjar måste du:

- [Ladda ned och installera den senaste Azure PowerShell](https://aka.ms/webpi-azps).
- Öppna Windows PowerShell och koppla den till ditt konto med den **Connect-AzureRmAccount** kommando.

### <a name="install-the-sql-iaas-extension"></a>Installera SQL IaaS-tillägget
Om du har etablerat en SQL Server-dator från Azure portal bör SQL Server IaaS-tillägget vara installerad. Du kan fastställa om det har installerats för den virtuella datorn genom att anropa **Get-AzureRmVM** kommandot och undersöka den **tillägg** egenskapen.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Om SQL Server IaaS Agent-tillägget har installerats, bör du se att det visas som ”SqlIaaSAgent” eller ”SQLIaaSExtension”. **ProvisioningState** för tillägget bör också visa ”Succeeded”. 

Om den inte är installerad eller kunde inte etableras, kan du installera det med följande kommando. Utöver VM namn och resursgrupp gruppen, måste du även ange regionen (**$region**) som den virtuella datorn finns i.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> Kontrollera inställningarna för aktuella
Om du har aktiverat automatisk säkerhetskopiering under etableringen kan du använda PowerShell för att kontrollera din nuvarande konfiguration. Kör den **Get-AzureRmVMSqlServerExtension** kommandot och granska de **AutoBackupSettings** egenskapen:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Du bör få utdata liknar följande:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Om dina utdata visar att **aktivera** är inställd på **FALSKT**, och sedan att aktivera automatisk säkerhetskopiering. Den goda nyheten är att aktivera och konfigurera automatisk säkerhetskopiering på samma sätt. Se nästa avsnitt för den här informationen.

> [!NOTE] 
> Om du markerar inställningarna innan du gör en ändring är det möjligt att du kommer tillbaka gamla konfigurationsvärden. Vänta några minuter och kontrollera inställningarna igen för att se till att dina ändringar har tillämpats.

### <a name="configure-automated-backup-v2"></a>Konfigurera automatisk säkerhetskopiering v2
Du kan använda PowerShell för att aktivera automatisk säkerhetskopiering samt för att ändra dess konfiguration och beteende när som helst. 

Först, Välj eller skapa ett lagringskonto för de säkerhetskopiera filerna. Följande skript väljer ett lagringskonto eller skapar den om den inte finns.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatisk säkerhetskopiering stöder inte lagra säkerhetskopior i premium-lagring, men det kan ta säkerhetskopior från VM-diskar som använder Premium Storage.

Använd sedan den **New AzureRmVMSqlServerAutoBackupConfig** kommando för att aktivera och konfigurera inställningarna för automatisk säkerhetskopiering v2 för att lagra säkerhetskopior i Azure storage-kontot. I det här exemplet anges säkerhetskopiorna ska behållas i 10 dagar. Säkerhetskopior av databasen system är aktiverade. Fullständiga säkerhetskopieringar är schemalagda för varje vecka med ett tidsfönster med början vid 20:00 för två timmar. Säkerhetskopieringar är schemalagda för var 30: e minut. Det andra kommandot **Set-AzureRmVMSqlServerExtension**, uppdaterar den angivna virtuella Azure-datorer med de här inställningarna.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS Agent. 

Om du vill aktivera kryptering, ändrar du föregående skript för att skicka den **EnableEncryption** parametern tillsammans med ett lösenord (säker sträng) för den **CertificatePassword** parametern. Följande skript gör att inställningarna för automatisk säkerhetskopiering i exemplet ovan och lägger till kryptering.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Bekräfta dina inställningar tillämpas [verifiera konfigurationen för automatisk säkerhetskopiering](#verifysettings).

### <a name="disable-automated-backup"></a>Inaktivera automatisk säkerhetskopiering
Om du vill inaktivera automatisk säkerhetskopiering, kör samma skript utan den **-aktivera** parametern till den **New AzureRmVMSqlServerAutoBackupConfig** kommandot. Om den **-aktivera** parametern signalerar kommandot för att inaktivera funktionen. Precis som med installationen, kan det ta flera minuter att inaktivera automatisk säkerhetskopiering.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Exempelskript
Följande skript innehåller ett antal variabler som du kan anpassa om du vill aktivera och konfigurera automatisk säkerhetskopiering för den virtuella datorn. I ditt fall kan du behöva anpassa skriptet efter dina behov. Du skulle behöva göra ändringar om du vill inaktivera säkerhetskopiering för systemdatabaser eller aktivera kryptering.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Övervakning

Om du vill övervaka automatisk säkerhetskopiering på SQL Server 2016/2017 har du två huvudsakliga alternativ. Eftersom automatisk säkerhetskopiering använder funktionen SQL Server-hanterad säkerhetskopiering kan tillämpas på samma sätt som övervakning för både.

Du kan börja söka status genom att anropa [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Eller fråga den [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) -tabellvärdesfunktion.

Ett annat alternativ är att dra nytta av den inbyggda funktionen för Database Mail för meddelanden.

1. Anropa den [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) lagrade proceduren för att tilldela en e-postadress till den **SSMBackup2WANotificationEmailIds** parametern. 
1. Aktivera [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) att skicka e-postmeddelanden från den virtuella Azure-datorn.
1. Använd SMTP-server och användar-namn för att konfigurera Database Mail. Du kan konfigurera Database Mail i SQL Server Management Studio eller med Transact-SQL-kommandon. Mer information finns i [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurera SQL Server Agent om du vill använda Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Kontrollera att SMTP-porten tillåts både via den lokala Virtuella brandväggen och nätverkssäkerhetsgruppen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
Automatiserad säkerhetskopiering v2 konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Så det är viktigt att [Läs dokumentationen för hanterad säkerhetskopiering](https://msdn.microsoft.com/library/dn449496.aspx) att förstå beteende och konsekvenser.

Du kan hitta ytterligare säkerhetskopia och återställa hos SQL Server på virtuella Azure-datorer i följande artikel: [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Information om andra tillgängliga automation-aktiviteter finns i [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

