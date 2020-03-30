---
title: Automatisk säkerhetskopiering v2 för virtuella SQL Server 2016/2017 Azure-datorer | Microsoft-dokument
description: I artikeln beskrivs funktionen Automatisk säkerhetskopiering för virtuella SQL Server 2016/2017-datorer som körs i Azure. Den här artikeln är specifik för virtuella datorer med hjälp av Resurshanteraren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 458012982531e228f7c4968f29e79e8b2e29aa48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651447"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatisk säkerhetskopiering v2 för virtuella Azure-datorer (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatisk säkerhetskopiering v2 konfigurerar automatiskt [Hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure VM som kör SQL Server 2016/2017 Standard-, Enterprise- eller Developer-utgåvor. På så sätt kan du konfigurera vanliga säkerhetskopieringar av databaser som använder varaktig Azure-bloblagring. Automatisk säkerhetskopiering v2 beror på [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Krav
Om du vill använda Automatisk säkerhetskopiering v2 läser du följande förutsättningar:

**Operativsystem:**

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server version/utgåva:**

- SQL Server 2016: Utvecklare, Standard eller Enterprise
- SQL Server 2017: Utvecklare, Standard eller Enterprise

> [!IMPORTANT]
> Automatisk säkerhetskopiering v2 fungerar med SQL Server 2016 eller senare. Om du använder SQL Server 2014 kan du använda Automatisk säkerhetskopiering v1 för att säkerhetskopiera databaserna. Mer information finns i [Automatisk säkerhetskopiering för virtuella SQL Server 2014-datorer i Azure](virtual-machines-windows-sql-automated-backup.md).

**Databaskonfiguration:**

- Måldatabaser måste använda den fullständiga återställningsmodellen. Mer information om hur den fullständiga återställningsmodellen påverkar säkerhetskopieringar finns i [Säkerhetskopiering under modellen för fullständig återställning](https://technet.microsoft.com/library/ms190217.aspx).
- Systemdatabaser behöver inte använda fullständig återställningsmodell. Om du behöver loggsäkerhetskopior för modell eller MSDB måste du dock använda fullständig återställningsmodell.
- Måldatabaser måste finnas på antingen sql server-standardinstansen eller en [korrekt installerad](virtual-machines-windows-sql-server-iaas-faq.md#administration) namngiven instans. 

> [!NOTE]
> Automatisk säkerhetskopiering förlitar sig på **SQL Server IaaS Agent Extension**. Aktuella SQL-avbildningar för virtuella datorer lägger till det här tillägget som standard. Mer information finns i [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering v2. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandona.

### <a name="basic-settings"></a>Grundläggande inställningar

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** | Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure VM som kör SQL Server 2016/2017 Developer, Standard eller Enterprise. |
| **Kvarhållningsperiod** | 1-30 dagar (30 dagar) | Antalet dagar för att behålla säkerhetskopior. |
| **Lagringskonto** | Azure Storage-konto | Ett Azure-lagringskonto som ska användas för lagring av automatisk säkerhetskopieringsfiler i blob-lagring. En behållare skapas på den här platsen för att lagra alla säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och databas-GUID. |
| **Kryptering** |Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat finns de certifikat som används för att återställa säkerhetskopian i det angivna lagringskontot. Den använder samma **automatiska reservbehållare** med samma namngivningskonvention. Om lösenordet ändras genereras ett nytt certifikat med det lösenordet, men det gamla certifikatet återstår att återställa tidigare säkerhetskopior. |
| **Lösenord** |Lösenordstext | Ett lösenord för krypteringsnycklar. Det här lösenordet krävs bara om kryptering är aktiverat. För att återställa en krypterad säkerhetskopia måste du ha rätt lösenord och relaterat certifikat som användes när säkerhetskopieringen gjordes. |

### <a name="advanced-settings"></a>Avancerade inställningar

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Säkerhetskopiering av systemdatabaser** | Aktivera/inaktivera (inaktiverad) | När den här funktionen är aktiverad säkerhetskopierar den också systemdatabaserna: Master, MSDB och Model. Kontrollera att de är i fullständigt återställningsläge för DATABASERNA MSDB och Modell. Log säkerhetskopior tas aldrig för Master. Och inga säkerhetskopior tas för TempDB. |
| **Schema för säkerhetskopiering** | Manuell/automatiserad (automatiserad) | Som standard bestäms säkerhetskopieringsschemat automatiskt baserat på loggtillväxten. Med schema för manuell säkerhetskopiering kan användaren ange tidsfönstret för säkerhetskopior. I det här fallet sker säkerhetskopieringar endast med den angivna frekvensen och under det angivna tidsfönstret för en viss dag. |
| **Fullständig säkerhetskopieringsfrekvens** | Dagligen/veckovis | Frekvens av fullständiga säkerhetskopior. I båda fallen börjar fullständiga säkerhetskopior under nästa schemalagda tidsfönster. När varje vecka är markerat kan säkerhetskopior sträcka sig flera dagar tills alla databaser har säkerhetskopierats. |
| **Starttid för full säkerhetskopiering** | 00:00 – 23:00 (01:00) | Starttid för en viss dag under vilken fullständiga säkerhetskopior kan äga rum. |
| **Fönstret Full säkerhetskopieringstid** | 1 – 23 timmar (1 timme) | Varaktigheten av tidsfönstret för en viss dag under vilken fullständiga säkerhetskopior kan äga rum. |
| **Logga säkerhetskopieringsfrekvens** | 5 – 60 minuter (60 minuter) | Frekvensen för loggsäkerhetskopior. |

## <a name="understanding-full-backup-frequency"></a>Förstå fullständig säkerhetskopieringsfrekvens
Det är viktigt att förstå skillnaden mellan dagliga och veckovisa fullständiga säkerhetskopior. Tänk på följande två exempelscenarier.

### <a name="scenario-1-weekly-backups"></a>Scenario 1: Veckovisa säkerhetskopior
Du har en virtuell SQL Server som innehåller ett antal stora databaser.

På måndag aktiverar du Automatisk säkerhetskopiering v2 med följande inställningar:

- Schema för **säkerhetskopiering: Manuell**
- Fullständig säkerhetskopieringsfrekvens: **Varje vecka**
- Full säkerhetskopiering starttid: **01:00**
- Fönstret full säkerhetskopieringstid: **1 timme**

Detta innebär att nästa tillgängliga backup fönster är tisdag kl 01:00 för 1 timme. Vid den tidpunkten börjar automatisk säkerhetskopiering säkerhetskopiera dina databaser en i taget. I det här fallet är dina databaser tillräckligt stora för att fullständiga säkerhetskopior slutförs för de första pardatabaserna. Men efter en timme har inte alla databaser säkerhetskopierats.

När detta händer börjar automatisk säkerhetskopiering säkerhetskopiera de återstående databaserna nästa dag, onsdag klockan 01.00 i en timme. Om inte alla databaser har säkerhetskopierats under den tiden, försöker den igen nästa dag samtidigt. Detta fortsätter tills alla databaser har säkerhetskopierats.

När den når tisdag igen, börjar automatisk säkerhetskopiering säkerhetskopiera alla databaser igen.

Det här scenariot visar att automatisk säkerhetskopiering endast fungerar inom det angivna tidsfönstret och att varje databas säkerhetskopieras en gång per vecka. Detta visar också att det är möjligt för säkerhetskopior att sträcka sig över flera dagar i det fall där det inte är möjligt att slutföra alla säkerhetskopior på en enda dag.

### <a name="scenario-2-daily-backups"></a>Scenario 2: Dagliga säkerhetskopior
Du har en virtuell SQL Server som innehåller ett antal stora databaser.

På måndag aktiverar du Automatisk säkerhetskopiering v2 med följande inställningar:

- Schema för säkerhetskopiering: Manuell
- Fullständig säkerhetskopieringsfrekvens: Dagligen
- Full säkerhetskopiering starttid: 22:00
- Fönstret full säkerhetskopieringstid: 6 timmar

Detta innebär att nästa tillgängliga säkerhetskopieringsfönster är måndag kl 22:00 i 6 timmar. Vid den tidpunkten börjar automatisk säkerhetskopiering säkerhetskopiera dina databaser en i taget.

Sedan, på tisdag vid 10 i 6 timmar, full säkerhetskopior av alla databaser börjar igen.

> [!IMPORTANT]
> När du schemalägger dagliga säkerhetskopior rekommenderar vi att du schemalägger ett brett tidsfönster för att säkerställa att alla databaser kan säkerhetskopieras inom denna tid. Detta är särskilt viktigt i de fall där du har en stor mängd data för att säkerhetskopiera.

## <a name="configure-new-vms"></a>Konfigurera nya virtuella datorer

Använd Azure-portalen för att konfigurera automatisk säkerhetskopiering v2 när du skapar en ny virtuell SQL Server 2016- eller 2017-virtuell dator i resurshanterarens distributionsmodell.

På fliken **Inställningar för SQL Server** väljer du **Aktivera** under **Automatisk säkerhetskopiering**. Följande Azure-portal skärmdump visar **SQL Automated Backup** inställningar.

![KONFIGURATION FÖR AUTOMATISK SQL-säkerhetskopiering i Azure-portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automatisk säkerhetskopiering v2 är inaktiverad som standard.

## <a name="configure-existing-vms"></a>Konfigurera befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga virtuella SQL Server-datorer navigerar du till [resursen virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) och väljer sedan **Säkerhetskopior** för att konfigurera dina automatiska säkerhetskopior.

![SQL Automated Backup för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


När du är klar klickar du på knappen **Använd** längst ned på **inställningssidan för säkerhetskopior** för att spara ändringarna.

Om du aktiverar automatisk säkerhetskopiering för första gången konfigurerar Azure SQL Server IaaS-agenten i bakgrunden. Under den här tiden kanske Azure-portalen inte visar att automatisk säkerhetskopiering är konfigurerad. Vänta flera minuter tills agenten har installerats, konfigurerat. Därefter återspeglar Azure-portalen de nya inställningarna.

## <a name="configure-with-powershell"></a>Konfigurera med PowerShell

Du kan använda PowerShell för att konfigurera automatisk säkerhetskopiering v2. Innan du börjar måste du:

- [Ladda ned och installera den senaste Azure PowerShell](https://aka.ms/webpi-azps).
- Öppna Windows PowerShell och associera det med ditt konto med kommandot **Connect-AzAccount.**

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Installera SQL IaaS-tillägget
Om du har etablerat en virtuell SQL Server-dator från Azure-portalen bör SQL Server IaaS-tillägget redan vara installerat. Du kan avgöra om det är installerat för din virtuella dator genom att anropa **Get-AzVM-kommandot** och undersöka egenskapen **Extensions.**

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Om tillägget SQL Server IaaS Agent är installerat bör du se det listat som "SqlIaaSAgent" eller "SQLIaaSExtension". **EtableringStaten** för tillägget ska också visa "Lyckades". 

Om den inte är installerad eller inte har etablerats kan du installera den med följande kommando. Förutom vm-namn och resursgrupp måste du också ange den region (**$region**) som den virtuella datorn finns i.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Verifiera aktuella inställningar
Om du har aktiverat automatisk säkerhetskopiering under etableringen kan du använda PowerShell för att kontrollera din aktuella konfiguration. Kör kommandot **Get-AzVMSqlServerExtension** och undersök egenskapen **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Du bör få utdata som liknar följande:

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

Om utdata visar att **Aktivera** är inställt på **Falskt**måste du aktivera automatisk säkerhetskopiering. Den goda nyheten är att du aktiverar och konfigurerar automatisk säkerhetskopiering på samma sätt. Se nästa avsnitt för den här informationen.

> [!NOTE] 
> Om du kontrollerar inställningarna direkt efter att du har gjort en ändring är det möjligt att du får tillbaka de gamla konfigurationsvärdena. Vänta några minuter och kontrollera inställningarna igen för att se till att ändringarna har tillämpats.

### <a name="configure-automated-backup-v2"></a>Konfigurera automatisk säkerhetskopiering v2
Du kan när som helst använda PowerShell för att aktivera automatisk säkerhetskopiering samt för att ändra dess konfiguration och beteende. 

Välj eller skapa först ett lagringskonto för säkerhetskopiorna. Följande skript väljer ett lagringskonto eller skapar det om det inte finns.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatisk säkerhetskopiering stöder inte lagring av säkerhetskopior i premiumlagring, men det kan ta säkerhetskopior från VM-diskar som använder Premium Storage.

Använd sedan kommandot **New-AzVMSqlServerAutoBackupConfig** för att aktivera och konfigurera inställningarna för automatisk säkerhetskopiering v2 för att lagra säkerhetskopior i Azure-lagringskontot. I det här exemplet är säkerhetskopiorna inställda på att behållas i 10 dagar. Säkerhetskopiering av systemdatabaser är aktiverade. Fullständiga säkerhetskopior är schemalagda varje vecka med ett tidsfönster som börjar klockan 20:00 i två timmar. Log-säkerhetskopior är schemalagda för var 30:e minut. Det andra kommandot, **Set-AzVMSqlServerExtension**, uppdaterar den angivna Azure VM med dessa inställningar.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten. 

Om du vill aktivera kryptering ändrar du det föregående skriptet för att skicka parametern **EnableEncryption** tillsammans med ett lösenord (säker sträng) för **parametern CertificatePassword.** Följande skript aktiverar inställningarna för automatisk säkerhetskopiering i föregående exempel och lägger till kryptering.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Kontrollera [konfigurationen för automatisk säkerhetskopiering](#verifysettings)för att bekräfta att dina inställningar tillämpas .

### <a name="disable-automated-backup"></a>Inaktivera automatisk säkerhetskopiering
Om du vill inaktivera automatisk säkerhetskopiering kör du samma skript utan parametern **-Enable** till kommandot **New-AzVMSqlServerAutoBackupConfig.** Frånvaron av parametern **-Enable** signalerar kommandot för att inaktivera funktionen. Som med installationen kan det ta flera minuter att inaktivera automatisk säkerhetskopiering.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Exempelskript
Följande skript innehåller en uppsättning variabler som du kan anpassa för att aktivera och konfigurera automatisk säkerhetskopiering för den virtuella datorn. I ditt fall kan du behöva anpassa skriptet baserat på dina krav. Du måste till exempel göra ändringar om du vill inaktivera säkerhetskopiering av systemdatabaser eller aktivera kryptering.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Övervakning

Om du vill övervaka automatisk säkerhetskopiering på SQL Server 2016/2017 har du två huvudalternativ. Eftersom automatisk säkerhetskopiering använder funktionen HANTERAd SQL Server-säkerhetskopiering gäller samma övervakningstekniker för båda.

Först kan du avsöka statusen genom att anropa [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Eller fråga funktionen [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabellvärderad.

Ett annat alternativ är att dra nytta av den inbyggda funktionen Databaspost för meddelanden.

1. Anropa [förfarandet msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) lagrad för att tilldela en e-postadress till parametern **SSMBackup2WANotificationEmailIds.** 
1. Aktivera [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) för att skicka e-postmeddelanden från Azure VM.
1. Använd SMTP-servern och användarnamnet för att konfigurera Databaspost. Du kan konfigurera Databasutskick i SQL Server Management Studio eller med Transact-SQL-kommandon. Mer information finns i [Databaspost](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurera SQL Server Agent för att använda Databaspost](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Kontrollera att SMTP-porten tillåts både via den lokala VM-brandväggen och nätverkssäkerhetsgruppen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
Automatisk säkerhetskopiering v2 konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Därför är det viktigt att [granska dokumentationen för Managed Backup för](https://msdn.microsoft.com/library/dn449496.aspx) att förstå beteendet och konsekvenserna.

Du hittar ytterligare vägledning för säkerhetskopiering och återställning för SQL Server på virtuella Azure-datorer i följande artikel: [Säkerhetskopiering och återställning för SQL Server i Virtuella Azure-datorer](virtual-machines-windows-sql-backup-recovery.md).

Information om andra tillgängliga automatiseringsuppgifter finns i [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).

