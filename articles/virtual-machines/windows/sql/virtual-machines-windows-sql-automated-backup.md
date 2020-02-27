---
title: Automatisk säkerhets kopiering för SQL Server 2014 Azure Virtual Machines
description: Förklarar den automatiska säkerhets kopierings funktionen för SQL Server 2014-datorer som körs i Azure. Den här artikeln är speciell för virtuella datorer som använder Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c7dea85d8de17a0f65e6e73b5b5fbe619d464d3d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650356"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatisk säkerhets kopiering för SQL Server 2014 Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatisk säkerhets kopiering konfigurerar automatiskt [hanterad säkerhets kopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en virtuell Azure-dator som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera regelbundna säkerhets kopieringar av databaser som använder varaktiga Azure Blob Storage. Automatiserad säkerhets kopiering beror på [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda automatisk säkerhets kopiering bör du tänka på följande:

**Operativ system**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server version/utgåva**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatisk säkerhets kopiering fungerar med SQL Server 2014. Om du använder SQL Server 2016/2017 kan du använda automatisk säkerhets kopiering v2 för att säkerhetskopiera dina databaser. Mer information finns i [Automatisk säkerhets kopiering v2 för SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Databas konfiguration**:

- Mål databaserna måste använda den fullständiga återställnings modellen. Mer information om effekten av den fullständiga återställnings modellen för säkerhets kopieringar finns i [säkerhets kopiering under den fullständiga återställnings modellen](https://technet.microsoft.com/library/ms190217.aspx).
- Mål databaserna måste finnas på standard SQL Servers instansen. SQL Server IaaS-tillägget har inte stöd för namngivna instanser.

> [!NOTE]
> Automatisk säkerhets kopiering förlitar sig på SQL Server IaaS agent-tillägg. Aktuella Galleri avbildningar för virtuella SQL-datorer Lägg till tillägget som standard. Mer information finns i [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar

I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhets kopiering. De faktiska konfigurations stegen varierar beroende på om du använder Azure Portal-eller Azure Windows PowerShell-kommandon.

| Inställning | Intervall (standard) | Description |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** | Aktivera/inaktivera (inaktive rad) | Aktiverar eller inaktiverar automatisk säkerhets kopiering för en virtuell Azure-dator som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperiod** | 1-30 dagar (30 dagar) | Antalet dagar som säkerhets kopian ska sparas. |
| **Lagringskonto** | Azure Storage-konto | Ett Azure Storage-konto som ska användas för att lagra automatiska säkerhetskopieringsfiler i Blob Storage. En behållare skapas på den här platsen för att lagra alla säkerhetskopieringsfiler. Fil namns konventionen för säkerhets kopiering innehåller datum, tid och dator namn. |
| **Kryptering** | Aktivera/inaktivera (inaktive rad) | Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat finns de certifikat som används för att återställa säkerhets kopian i det angivna lagrings kontot i samma `automaticbackup` behållare med samma namngivnings konvention. Om lösen ordet ändras genereras ett nytt certifikat med det lösen ordet, men det gamla certifikatet kvarstår för att återställa tidigare säkerhets kopior. |
| **Lösenord** | Lösen ords text | Ett lösen ord för krypterings nycklar. Detta krävs endast om kryptering har Aktiver ATS. För att kunna återställa en krypterad säkerhets kopia måste du ha rätt lösen ord och relaterat certifikat som användes när säkerhets kopieringen gjordes. |


## <a name="configure-new-vms"></a>Konfigurera nya virtuella datorer

Använd Azure Portal för att konfigurera automatisk säkerhets kopiering när du skapar en ny SQL Server 2014-virtuell dator i distributions modellen för Resource Manager.

Rulla ned till **Automatisk säkerhets kopiering** på fliken **SQL Server inställningar** och välj **Aktivera**. Följande Azure Portal skärm bild visar inställningarna för **Automatisk säkerhets kopiering i SQL** .

![Konfiguration av automatiserad SQL-säkerhetskopiering i Azure Portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurera befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga SQL Server virtuella datorer kan du aktivera och inaktivera automatiserade säkerhets kopieringar, ändra kvarhållningsperioden, ange lagrings kontot och aktivera kryptering från Azure Portal. 

Gå till [resursen för virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) för din SQL Server 2014-dator och välj sedan **säkerhets kopieringar**. 

![Automatisk SQL-säkerhetskopiering för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

När du är färdig väljer du knappen **tillämpa** längst ned på sidan **säkerhets kopior** för att spara ändringarna.

Om du aktiverar automatisk säkerhets kopiering för första gången konfigurerar Azure SQL Server IaaS-agenten i bakgrunden. Under den här tiden kanske Azure Portal inte visar att automatisk säkerhets kopiering har kon figurer ATS. Vänta några minuter tills agenten har installerats och kon figurer ATS. Efter det kommer Azure Portal att återspegla de nya inställningarna.

> [!NOTE]
> Du kan också konfigurera automatisk säkerhets kopiering med en mall. Mer information finns i [Azure snabb starts mal len för automatisk säkerhets kopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurera med PowerShell

Du kan använda PowerShell för att konfigurera automatisk säkerhets kopiering. Innan du börjar måste du:

- [Hämta och installera den senaste Azure PowerShell](https://aka.ms/webpi-azps).
- Öppna Windows PowerShell och associera det med ditt konto med kommandot **Connect-AzAccount** . 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Installera SQL IaaS-tillägget
Om du etablerade en SQL Server virtuell dator från Azure Portal bör SQL Server IaaS-tillägget redan vara installerat. Du kan kontrol lera om den är installerad för den virtuella datorn genom att anropa **Get-AzVM-** kommandot och undersöka **tillägg** -egenskapen.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Om tillägget SQL Server IaaS agent är installerat bör det visas som "Sqliaasagent är" eller "SQLIaaSExtension". **ProvisioningState** för tillägget bör också Visa "lyckades".

Om den inte är installerad eller inte gick att tillhandahålla, kan du installera den med följande kommando. Förutom namnet på den virtuella datorn och resurs gruppen måste du också ange den region ( **$region**) som den virtuella datorn finns i. Ange licens typ för din SQL Server VM, välja mellan antingen betala per användning eller hämta egen licens via [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). Mer information om licensiering finns i [licensierings modell](virtual-machines-windows-sql-ahb.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Om tillägget inte redan är installerat startas SQL Server-tjänsten om du installerar tillägget.

### <a id="verifysettings"></a>Verifiera aktuella inställningar

Om du har aktiverat automatisk säkerhets kopiering under etableringen kan du använda PowerShell för att kontrol lera den aktuella konfigurationen. Kör kommandot **Get-AzVMSqlServerExtension** och granska egenskapen **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Du bör få utdata som liknar följande:

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

Om resultatet visar att **Aktivera** är inställt på **falskt**måste du aktivera automatisk säkerhets kopiering. Den goda nyheten är att du aktiverar och konfigurerar automatisk säkerhets kopiering på samma sätt. Mer information finns i nästa avsnitt.

> [!NOTE] 
> Om du kontrollerar inställningarna direkt efter att du har gjort en ändring är det möjligt att du får tillbaka de gamla konfigurations värdena. Vänta några minuter och kontrol lera inställningarna igen för att se till att ändringarna har tillämpats.

### <a name="configure-automated-backup"></a>Konfigurera automatisk säkerhets kopiering
Du kan använda PowerShell för att aktivera automatisk säkerhets kopiering samt ändra konfiguration och beteende när som helst.

Börja med att välja eller skapa ett lagrings konto för de säkerhetskopierade filerna. Följande skript väljer ett lagrings konto eller skapar det om det inte redan finns.

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
> Automatisk säkerhets kopiering stöder inte lagring av säkerhets kopior i Premium Storage, men det kan ta säkerhets kopior från virtuella dator diskar som använder Premium Storage.

Använd sedan kommandot **New-AzVMSqlServerAutoBackupConfig** för att aktivera och konfigurera inställningarna för automatisk säkerhets kopiering för att lagra säkerhets kopior i Azure Storage-kontot. I det här exemplet behålls säkerhets kopiorna i 10 dagar. Det andra kommandot, **set-AzVMSqlServerExtension**, uppdaterar den angivna virtuella Azure-datorn med de här inställningarna.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

> [!NOTE]
> Det finns andra inställningar för **New-AzVMSqlServerAutoBackupConfig** som endast gäller för SQL Server 2016 och automatisk säkerhets kopiering v2. SQL Server 2014 har inte stöd för följande inställningar: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**och **LogBackupFrequencyInMinutes**. Om du försöker konfigurera de här inställningarna på en virtuell SQL Server 2014-dator finns det inget fel, men inställningarna tillämpas inte. Om du vill använda de här inställningarna på en SQL Server 2016 virtuell dator, se [Automatisk säkerhets kopiering v2 för SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

Om du vill aktivera kryptering ändrar du det tidigare skriptet för att skicka parametern **EnableEncryption** tillsammans med ett lösen ord (säker sträng) för parametern **CertificatePassword** . Följande skript aktiverar de automatiska säkerhets kopierings inställningarna i föregående exempel och lägger till kryptering.

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

Bekräfta [konfigurationen för automatisk säkerhets kopiering](#verifysettings)för att kontrol lera att dina inställningar tillämpas.

### <a name="disable-automated-backup"></a>Inaktivera automatisk säkerhets kopiering

Om du vill inaktivera automatisk säkerhets kopiering kör du samma skript utan parametern **-Enable** till kommandot **New-AzVMSqlServerAutoBackupConfig** . Avsaknad av parametern **-Enable** signalerar kommandot för att inaktivera funktionen. Precis som vid installationen kan det ta flera minuter att inaktivera automatisk säkerhets kopiering.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Exempelskript

Följande skript innehåller en uppsättning variabler som du kan anpassa för att aktivera och konfigurera automatisk säkerhets kopiering för den virtuella datorn. I så fall kan du behöva anpassa skriptet baserat på dina krav. Du skulle till exempel behöva göra ändringar om du vill inaktivera säkerhets kopieringen av system databaser eller aktivera kryptering.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Övervakning

Om du vill övervaka automatisk säkerhets kopiering på SQL Server 2014 har du två huvud alternativ. Eftersom den automatiska säkerhets kopieringen använder funktionen SQL Server hanterad säkerhets kopiering, gäller samma övervaknings tekniker för båda.

Först kan du söka efter statusen genom att anropa [msdb. smart_admin. sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Eller så frågar du tabell värdes funktionen [msdb. smart_admin. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

> [!NOTE]
> Schemat för hanterad säkerhets kopiering i SQL Server 2014 är **msdb. smart_admin**. I SQL Server 2016 detta ändrades till **msdb. managed_backup**, och referens avsnitten använder detta nyare schema. Men för SQL Server 2014 måste du fortsätta att använda **smart_admin** -schemat för alla hanterade säkerhets kopierings objekt.

Ett annat alternativ är att dra nytta av den inbyggda Database Mail funktionen för meddelanden.

1. Anropa den lagrade proceduren [msdb. smart_admin. sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) för att tilldela en e-postadress till **SSMBackup2WANotificationEmailIds** -parametern. 
1. Aktivera [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) för att skicka e-postmeddelanden från den virtuella Azure-datorn.
1. Använd SMTP-servern och användar namnet för att konfigurera Database Mail. Du kan konfigurera Database Mail i SQL Server Management Studio eller med Transact-SQL-kommandon. Mer information finns i [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurera SQL Server Agent att använda Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Kontrol lera att SMTP-porten tillåts både via den lokala VM-brandväggen och nätverks säkerhets gruppen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Automatisk säkerhets kopiering konfigurerar hanterad säkerhets kopiering på virtuella Azure-datorer. Det är därför viktigt att [granska dokumentationen för hanterad säkerhets kopiering på SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Du hittar ytterligare vägledning för säkerhets kopiering och återställning av SQL Server på virtuella Azure-datorer i följande artikel: [säkerhets kopiering och återställning för SQL Server i Azure-Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Information om andra tillgängliga Automation-uppgifter finns [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns [SQL Server på azure Virtual Machines-översikt](virtual-machines-windows-sql-server-iaas-overview.md).
