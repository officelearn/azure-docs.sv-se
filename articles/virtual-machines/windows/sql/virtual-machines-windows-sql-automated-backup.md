---
title: Automatisk säkerhetskopiering för virtuella SQL Server 2014-azure-datorer
description: I artikeln beskrivs funktionen Automatisk säkerhetskopiering för virtuella SQL Server 2014-datorer som körs i Azure. Den här artikeln är specifik för virtuella datorer med hjälp av Resurshanteraren.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650356"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatisk säkerhetskopiering för virtuella SQL Server 2014-datorer (Resurshanteraren)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatisk säkerhetskopiering konfigurerar automatiskt [hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure VM som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera vanliga säkerhetskopieringar av databaser som använder varaktig Azure-bloblagring. Automatisk säkerhetskopiering beror på [TILLÄGGET FÖR SQL Server IaaS-agent .](virtual-machines-windows-sql-server-agent-extension.md)

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Krav
Om du vill använda automatisk säkerhetskopiering bör du tänka på följande förutsättningar:

**Operativsystem:**

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server version/utgåva:**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatisk säkerhetskopiering fungerar med SQL Server 2014. Om du använder SQL Server 2016/2017 kan du använda Automatisk säkerhetskopiering v2 för att säkerhetskopiera databaserna. Mer information finns i [Automatisk säkerhetskopiering v2 för virtuella SQL Server 2016-azure-datorer](virtual-machines-windows-sql-automated-backup-v2.md).

**Databaskonfiguration:**

- Måldatabaser måste använda den fullständiga återställningsmodellen. Mer information om hur den fullständiga återställningsmodellen påverkar säkerhetskopieringar finns i [Säkerhetskopiering under modellen för fullständig återställning](https://technet.microsoft.com/library/ms190217.aspx).
- Måldatabaser måste finnas på standard-SQL Server-instansen. SQL Server IaaS-tillägget stöder inte namngivna instanser.

> [!NOTE]
> Automatisk säkerhetskopiering är beroende av SQL Server IaaS Agent Extension. Aktuella SQL-avbildningar för virtuella datorer lägger till det här tillägget som standard. Mer information finns i [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar

I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandona.

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** | Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure VM som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperiod** | 1-30 dagar (30 dagar) | Antalet dagar för att behålla en säkerhetskopia. |
| **Lagringskonto** | Azure Storage-konto | Ett Azure-lagringskonto som ska användas för lagring av automatisk säkerhetskopieringsfiler i blob-lagring. En behållare skapas på den här platsen för att lagra alla säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och datornamn. |
| **Kryptering** | Aktivera/inaktivera (inaktiverad) | Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat finns certifikaten som används för att återställa säkerhetskopian `automaticbackup` i det angivna lagringskontot i samma behållare med samma namngivningskonvention. Om lösenordet ändras genereras ett nytt certifikat med det lösenordet, men det gamla certifikatet återstår att återställa tidigare säkerhetskopior. |
| **Lösenord** | Lösenordstext | Ett lösenord för krypteringsnycklar. Detta krävs endast om kryptering är aktiverat. För att återställa en krypterad säkerhetskopia måste du ha rätt lösenord och relaterat certifikat som användes när säkerhetskopieringen gjordes. |


## <a name="configure-new-vms"></a>Konfigurera nya virtuella datorer

Använd Azure-portalen för att konfigurera automatisk säkerhetskopiering när du skapar en ny virtuell SQL Server 2014-dator i distributionsmodellen för Resource Manager.

På fliken **Inställningar för SQL Server** bläddrar du ned till Automatisk **säkerhetskopiering** och väljer **Aktivera**. Följande Azure-portal skärmdump visar **SQL Automated Backup** inställningar.

![KONFIGURATION FÖR AUTOMATISK SQL-säkerhetskopiering i Azure-portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurera befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga virtuella SQL Server-datorer kan du aktivera och inaktivera automatiska säkerhetskopior, ändra kvarhållningsperioden, ange lagringskontot och aktivera kryptering från Azure-portalen. 

Navigera till [resursen virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) för den virtuella SQL Server 2014-datorn och välj sedan **Säkerhetskopior**. 

![SQL Automated Backup för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

När du är klar väljer du knappen **Använd** längst ned på sidan **Säkerhetskopior** för att spara ändringarna.

Om du aktiverar automatisk säkerhetskopiering för första gången konfigurerar Azure SQL Server IaaS-agenten i bakgrunden. Under den här tiden kanske Azure-portalen inte visar att automatisk säkerhetskopiering är konfigurerad. Vänta flera minuter innan agenten har installerats och konfigurerats. Därefter återspeglar Azure-portalen de nya inställningarna.

> [!NOTE]
> Du kan också konfigurera automatisk säkerhetskopiering med hjälp av en mall. Mer information finns i [Azure-snabbstartsmall för automatisk säkerhetskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurera med PowerShell

Du kan använda PowerShell för att konfigurera automatisk säkerhetskopiering. Innan du börjar måste du:

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

Om den inte är installerad eller inte har etablerats kan du installera den med följande kommando. Förutom vm-namn och resursgrupp måste du också ange den region (**$region**) som den virtuella datorn finns i. Ange licenstypen för din VIRTUELLA SQL Server och välj mellan antingen användningsbaserad betalning eller bring-your-own-licens via [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Mer information om licensiering finns i [licensieringsmodellen](virtual-machines-windows-sql-ahb.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Om tillägget inte redan är installerat startas SQL Server-tjänsten om du installerar tillägget.

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Verifiera aktuella inställningar

Om du har aktiverat automatisk säkerhetskopiering under etableringen kan du använda PowerShell för att kontrollera din aktuella konfiguration. Kör kommandot **Get-AzVMSqlServerExtension** och undersök egenskapen **AutoBackupSettings:**

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

Om utdata visar att **Aktivera** är inställt på **Falskt**måste du aktivera automatisk säkerhetskopiering. Den goda nyheten är att du aktiverar och konfigurerar automatisk säkerhetskopiering på samma sätt. Se nästa avsnitt för den här informationen.

> [!NOTE] 
> Om du kontrollerar inställningarna direkt efter att du har gjort en ändring är det möjligt att du får tillbaka de gamla konfigurationsvärdena. Vänta några minuter och kontrollera inställningarna igen för att se till att ändringarna har tillämpats.

### <a name="configure-automated-backup"></a>Konfigurera automatisk säkerhetskopiering
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

Använd sedan kommandot **New-AzVMSqlServerAutoBackupConfig** för att aktivera och konfigurera inställningarna för automatisk säkerhetskopiering för att lagra säkerhetskopior i Azure-lagringskontot. I det här exemplet behålls säkerhetskopiorna i 10 dagar. Det andra kommandot, **Set-AzVMSqlServerExtension**, uppdaterar den angivna Azure VM med dessa inställningar.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

> [!NOTE]
> Det finns andra inställningar för **New-AzVMSqlServerAutoBackupConfig** som endast gäller för SQL Server 2016 och Automated Backup v2. SQL Server 2014 stöder inte följande inställningar: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**och **LogBackupFrequencyInMinutes**. Om du försöker konfigurera dessa inställningar på en virtuell SQL Server 2014-dator finns det inget fel, men inställningarna tillämpas inte. Om du vill använda dessa inställningar på en virtuell SQL Server 2016-dator läser du [Automatisk säkerhetskopiering v2 för virtuella SQL Server 2016-azure-datorer](virtual-machines-windows-sql-automated-backup-v2.md).

Om du vill aktivera kryptering ändrar du det föregående skriptet för att skicka parametern **EnableEncryption** tillsammans med ett lösenord (säker sträng) för **parametern CertificatePassword.** Följande skript aktiverar inställningarna för automatisk säkerhetskopiering i föregående exempel och lägger till kryptering.

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

Om du vill övervaka automatisk säkerhetskopiering på SQL Server 2014 har du två huvudalternativ. Eftersom automatisk säkerhetskopiering använder funktionen HANTERAd SQL Server-säkerhetskopiering gäller samma övervakningstekniker för båda.

Först kan du avsöka statusen genom att anropa [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Eller fråga funktionen [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabellvärderad funktion.

> [!NOTE]
> Schemat för Hanterad säkerhetskopiering i SQL Server 2014 är **msdb.smart_admin**. I SQL Server 2016 ändrades detta till **msdb.managed_backup**och referensavsnitten använder det här nyare schemat. Men för SQL Server 2014 måste du fortsätta att använda **smart_admin** schema för alla hanterade säkerhetskopieringsobjekt.

Ett annat alternativ är att dra nytta av den inbyggda funktionen Databaspost för meddelanden.

1. Anropa [förfarandet msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) lagrad för att tilldela en e-postadress till parametern **SSMBackup2WANotificationEmailIds.** 
1. Aktivera [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) för att skicka e-postmeddelanden från Azure VM.
1. Använd SMTP-servern och användarnamnet för att konfigurera Databaspost. Du kan konfigurera Databasutskick i SQL Server Management Studio eller med Transact-SQL-kommandon. Mer information finns i [Databaspost](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurera SQL Server Agent för att använda Databaspost](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Kontrollera att SMTP-porten tillåts både via den lokala VM-brandväggen och nätverkssäkerhetsgruppen för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Automatisk säkerhetskopiering konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Det är därför viktigt att [granska dokumentationen för Hanterad säkerhetskopiering på SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Du hittar ytterligare vägledning för säkerhetskopiering och återställning för SQL Server på virtuella Azure-datorer i följande artikel: [Säkerhetskopiering och återställning för SQL Server i Virtuella Azure-datorer](virtual-machines-windows-sql-backup-recovery.md).

Information om andra tillgängliga automatiseringsuppgifter finns i [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).
