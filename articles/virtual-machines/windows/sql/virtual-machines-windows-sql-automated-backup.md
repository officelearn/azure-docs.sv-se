---
title: Automatisk säkerhetskopiering för SQL Server 2014 Azure Virtual Machines | Microsoft Docs
description: Beskriver funktionen automatisk säkerhetskopiering för SQL Server 2014-datorer som körs i Azure. Den här artikeln är specifik för virtuella datorer med Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 99439c2b6bd4fdd271dda7a49850c5b6f44330b3
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984723"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatiserad säkerhetskopiering för SQL Server 2014-datorer (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatisk säkerhetskopiering konfigureras automatiskt [hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure-dator som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera regelbundna databassäkerhetskopieringar som använder varaktiga Azure blob-lagring. Automatisk säkerhetskopiering beror på den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Förutsättningar
Överväg följande krav för att använda automatisk säkerhetskopiering:

**Operativsystemet**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server version/utgåva**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatiserad säkerhetskopiering fungerar med SQL Server 2014. Om du använder SQL Server 2016/2017 kan använda du automatisk säkerhetskopiering v2 för att säkerhetskopiera dina databaser. Mer information finns i [v2 för automatisk säkerhetskopiering för SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Databaskonfiguration**:

- Måldatabaserna måste använda den fullständiga återställningsmodellen. Mer information om effekten av den fullständiga återställningsmodellen säkerhetskopior finns i [säkerhetskopiering Under den fullständiga återställningsmodellen](https://technet.microsoft.com/library/ms190217.aspx).
- Måldatabaserna måste vara på standardinstansen för SQL Server. SQL Server IaaS-tillägget har inte stöd för namngivna instanser.

> [!NOTE]
> Automatisk säkerhetskopiering är beroende av SQL Server IaaS Agent-tillägget. Aktuell SQL VM-galleriavbildningar lägga till det här tillägget som standard. Mer information finns i [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar

I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandon.

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** | Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure virtuell dator som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperiod** | 1 – 30 dagar (30 dagar) | Antal dagar att behålla en säkerhetskopia. |
| **Lagringskonto** | Azure Storage-konto | Ett Azure storage-konto som ska användas för att lagra filer för automatisk säkerhetskopiering i blob storage. En behållare skapas på den här platsen för att lagra säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och namnet på datorn. |
| **Kryptering** | Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar kryptering. När kryptering har aktiverats, de certifikat som används för att återställa säkerhetskopian finns i det angivna lagringskontot i samma `automaticbackup` behållare med samma namngivningskonvention. Om lösenordet ändras, skapas ett nytt certifikat med lösenordet, men det gamla certifikatet förblir för att återställa tidigare säkerhetskopior. |
| **Lösenord** | Lösenordet | Ett lösenord för krypteringsnycklar. Detta är endast krävs om kryptering är aktiverat. Om du vill återställa en krypterad säkerhetskopiering måste du ha rätt lösenord och relaterade certifikatet som användes när säkerhetskopian skapades. |

## <a name="configure-in-the-portal"></a>Konfigurera i portalen

Du kan använda Azure-portalen för att konfigurera automatisk säkerhetskopiering under etableringen eller för befintliga SQL Server 2014-datorer.

## <a name="configure-new-vms"></a>Konfigurera nya virtuella datorer

Använd Azure-portalen för att konfigurera automatisk säkerhetskopiering när du skapar en ny SQL Server 2014-dator i distributionsmodellen för Resource Manager.

I den **SQL Server-inställningar** väljer **automatisk säkerhetskopiering**. I följande Skärmbild av Azure portal visas den **SQL automatisk säkerhetskopiering** inställningar.

![Automatisk säkerhetskopiering för SQL-konfiguration i Azure-portalen](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurera befintliga virtuella datorer

För befintliga SQL Server-datorer, väljer du din SQL Server-dator. Välj sedan den **konfiguration av SQL Server** delen av den virtuella datorn **inställningar**.

![SQL automatisk säkerhetskopiering för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

I den **konfiguration av SQL Server** fönstret klickar du på den **redigera** i avsnittet automatisk säkerhetskopiering.

![Konfigurera automatisk säkerhetskopiering för SQL för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

När du är klar klickar du på den **OK** knappen längst ned på den **konfiguration av SQL Server** inställningar för att spara dina ändringar.

Om du aktiverar automatisk säkerhetskopiering för första gången, konfigurerar SQL Server IaaS Agent i bakgrunden i Azure. Under denna tid kanske Azure-portalen inte visar att automatisk säkerhetskopiering är konfigurerad. Vänta några minuter för att agenten ska installeras, konfigureras. Efter det Azure-portalen visar de nya inställningarna.

> [!NOTE]
> Du kan också konfigurera automatisk säkerhetskopiering med en mall. Mer information finns i [Azure-snabbstartsmall för automatisk säkerhetskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurera med PowerShell

Du kan använda PowerShell för att konfigurera automatisk säkerhetskopiering. Innan du börjar måste du:

- [Ladda ned och installera den senaste Azure PowerShell](https://aka.ms/webpi-azps).
- Öppna Windows PowerShell och koppla den till ditt konto med den **Connect AzAccount** kommando.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Installera SQL IaaS-tillägget
Om du har etablerat en SQL Server-dator från Azure portal bör SQL Server IaaS-tillägget vara installerad. Du kan fastställa om det har installerats för den virtuella datorn genom att anropa **Get-AzVM** kommandot och undersöka den **tillägg** egenskapen.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Om SQL Server IaaS Agent-tillägget har installerats, bör du se att det visas som ”SqlIaaSAgent” eller ”SQLIaaSExtension”. **ProvisioningState** för tillägget bör också visa ”Succeeded”.

Om den inte är installerad eller kunde inte etableras, kan du installera det med följande kommando. Utöver VM namn och resursgrupp gruppen, måste du även ange regionen (**$region**) som den virtuella datorn finns i.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Om tillägget inte redan är installerat installerar tillägget startar om SQL Server-tjänsten.

### <a id="verifysettings"></a> Kontrollera inställningarna för aktuella

Om du har aktiverat automatisk säkerhetskopiering under etableringen kan du använda PowerShell för att kontrollera din nuvarande konfiguration. Kör den **Get-AzVMSqlServerExtension** kommandot och granska de **AutoBackupSettings** egenskapen:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Du bör få utdata liknar följande:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Om dina utdata visar att **aktivera** är inställd på **FALSKT**, och sedan att aktivera automatisk säkerhetskopiering. Den goda nyheten är att aktivera och konfigurera automatisk säkerhetskopiering på samma sätt. Se nästa avsnitt för den här informationen.

> [!NOTE] 
> Om du markerar inställningarna innan du gör en ändring är det möjligt att du kommer tillbaka gamla konfigurationsvärden. Vänta några minuter och kontrollera inställningarna igen för att se till att dina ändringar har tillämpats.

### <a name="configure-automated-backup"></a>Konfigurera automatisk säkerhetskopiering
Du kan använda PowerShell för att aktivera automatisk säkerhetskopiering samt för att ändra dess konfiguration och beteende när som helst.

Först, Välj eller skapa ett lagringskonto för de säkerhetskopiera filerna. Följande skript väljer ett lagringskonto eller skapar den om den inte finns.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Automatisk säkerhetskopiering stöder inte lagra säkerhetskopior i premium-lagring, men det kan ta säkerhetskopior från VM-diskar som använder Premium Storage.

Använd sedan den **New AzVMSqlServerAutoBackupConfig** kommando för att aktivera och konfigurera inställningarna för automatisk säkerhetskopiering för att lagra säkerhetskopior i Azure storage-kontot. I det här exemplet bevaras säkerhetskopior i 10 dagar. Det andra kommandot **Set-AzVMSqlServerExtension**, uppdaterar den angivna virtuella Azure-datorer med de här inställningarna.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS Agent.

> [!NOTE]
> Det finns andra inställningar för **New AzVMSqlServerAutoBackupConfig** som gäller endast för SQL Server 2016 och automatisk säkerhetskopiering v2. SQL Server 2014 stöder inte följande inställningar: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**, och **LogBackupFrequencyInMinutes**. Om du försöker konfigurera dessa inställningar på en virtuell dator för SQL Server 2014, det finns inget fel, men inställningarna gäller inte. Om du vill använda de här inställningarna på en virtuell dator för SQL Server 2016 finns i [v2 för automatisk säkerhetskopiering för SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

Om du vill aktivera kryptering, ändrar du föregående skript för att skicka den **EnableEncryption** parametern tillsammans med ett lösenord (säker sträng) för den **CertificatePassword** parametern. Följande skript gör att inställningarna för automatisk säkerhetskopiering i exemplet ovan och lägger till kryptering.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Bekräfta dina inställningar tillämpas [verifiera konfigurationen för automatisk säkerhetskopiering](#verifysettings).

### <a name="disable-automated-backup"></a>Inaktivera automatisk säkerhetskopiering

Om du vill inaktivera automatisk säkerhetskopiering, kör samma skript utan den **-aktivera** parametern till den **New AzVMSqlServerAutoBackupConfig** kommandot. Om den **-aktivera** parametern signalerar kommandot för att inaktivera funktionen. Precis som med installationen, kan det ta flera minuter att inaktivera automatisk säkerhetskopiering.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
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

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Övervakning

För att övervaka automatisk säkerhetskopiering på SQL Server 2014, har du två huvudsakliga alternativ. Eftersom automatisk säkerhetskopiering använder funktionen SQL Server-hanterad säkerhetskopiering kan tillämpas på samma sätt som övervakning för både.

Du kan börja söka status genom att anropa [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Eller fråga den [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) -tabellvärdesfunktion.

> [!NOTE]
> Schemat för hanterad säkerhetskopiering i SQL Server 2014 är **msdb.smart_admin**. I SQL Server 2016 detta ändras till **msdb.managed_backup**, och referensämnena använder den här nyare schema. Men för SQL Server 2014 måste du fortsätta att använda den **smart_admin** schemat för alla objekt som hanterad säkerhetskopiering.

Ett annat alternativ är att dra nytta av den inbyggda funktionen för Database Mail för meddelanden.

1. Anropa den [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) lagrade proceduren för att tilldela en e-postadress till den **SSMBackup2WANotificationEmailIds** parametern. 
1. Aktivera [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) att skicka e-postmeddelanden från den virtuella Azure-datorn.
1. Använd SMTP-server och användar-namn för att konfigurera Database Mail. Du kan konfigurera Database Mail i SQL Server Management Studio eller med Transact-SQL-kommandon. Mer information finns i [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurera SQL Server Agent om du vill använda Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Kontrollera att SMTP-porten tillåts både via den lokala Virtuella brandväggen och nätverkssäkerhetsgruppen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Automatisk säkerhetskopiering konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Så det är viktigt att [Läs dokumentationen för hanterad säkerhetskopiering på SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Du kan hitta ytterligare säkerhetskopia och återställa hos SQL Server på virtuella Azure-datorer i följande artikel: [Säkerhetskopiering och återställning för SQLServer i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Information om andra tillgängliga automation-aktiviteter finns i [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).
