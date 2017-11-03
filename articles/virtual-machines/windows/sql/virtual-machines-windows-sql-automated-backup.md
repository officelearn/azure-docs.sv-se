---
title: "Automatisk säkerhetskopiering i SQL Server 2014 virtuella Azure-datorer | Microsoft Docs"
description: "Beskriver funktionen automatisk säkerhetskopiering för SQL Server 2014 virtuella datorer som körs i Azure. Den här artikeln är specifik för virtuella datorer med Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 91aab896dd5f06c950ee0ed8f36cc6a953d91611
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatisk säkerhetskopiering för SQL Server 2014 virtuella datorer (Resource Manager)

> [!div class="op_single_selector"]
> * [SQLServer 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Automatisk säkerhetskopiering konfigurerar automatiskt [hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure-dator som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera regelbundna säkerhetskopieringar som använder beständiga Azure blob storage. Automatisk säkerhetskopiering är beroende av den [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Krav
Överväg följande krav för att använda automatisk säkerhetskopiering:

**Operativsystemet**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server-version/utgåva**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatisk säkerhetskopiering fungerar med SQL Server 2014. Om du använder SQL Server 2016 kan du använda automatisk säkerhetskopiering v2 för att säkerhetskopiera databaserna. Mer information finns i [automatisk säkerhetskopiering v2 för SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Databaskonfiguration**:

- Måldatabaserna måste använda den fullständiga återställningsmodellen. Mer information om inverkan av den fullständiga återställningsmodellen säkerhetskopior finns [säkerhetskopiering Under den fullständiga återställningsmodellen](https://technet.microsoft.com/library/ms190217.aspx).
- Måldatabaserna måste vara på standardinstansen för SQL Server. SQL Server IaaS-tillägg stöder inte namngivna instanser.

**Azure distributionsmodell**:

- Resource Manager

**Azure PowerShell**:

- [Installera de senaste Azure PowerShell-kommandon](/powershell/azure/overview) om du vill konfigurera automatisk säkerhetskopiering med PowerShell.

> [!NOTE]
> Automatisk säkerhetskopiering är beroende av SQL Server IaaS Agent tillägget. Aktuell SQL virtuella galleriavbildningar lägga till tillägget som standard. Mer information finns i [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar

I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandon.

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** | Aktivera/inaktivera (inaktiverat) | Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure-dator som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperioden** | 1 – 30 dagar (30 dagar) | Antal dagar att behålla en säkerhetskopia. |
| **Storage-konto** | Azure Storage-konto | Ett Azure storage-konto som ska användas för att lagra filer för automatisk säkerhetskopiering i blob storage. En behållare skapas på denna plats att lagra säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och namnet på datorn. |
| **Kryptering** | Aktivera/inaktivera (inaktiverat) | Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat certifikat som används för att återställa säkerhetskopian finns i det angivna lagringskontot i samma `automaticbackup` behållaren som använder samma namngivningskonvention. Om lösenordet ändras, skapas ett nytt certifikat med lösenordet, men det gamla certifikatet finns kvar för att återställa tidigare säkerhetskopior. |
| **Lösenord** | Text för lösenord | Ett lösenord för krypteringsnycklar. Detta är endast krävs om kryptering är aktiverat. Du måste ha rätt lösenord och relaterade certifikatet som användes när säkerhetskopian skapades för att återställa en krypterad säkerhetskopiering. |

## <a name="configuration-in-the-portal"></a>Konfigurationen för portalen

Du kan använda Azure-portalen för att konfigurera automatisk säkerhetskopiering under etableringen eller för befintliga SQL Server 2014 virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer

Använda Azure portal för att konfigurera automatisk säkerhetskopiering när du skapar en ny SQL Server 2014 virtuell dator i Resource Manager-distributionsmodellen.

I den **SQL Server-inställningar** bladet väljer **automatisk säkerhetskopiering**. I följande Skärmbild av Azure portal visas den **SQL automatisk säkerhetskopiering** bladet.

![Automatisk säkerhetskopiering i SQL-konfigurationen i Azure-portalen](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Kontexten, finns i avsnittet fullständig på [etablera en virtuell dator i SQL Server i Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

Välj den virtuella datorn för SQL Server för befintliga SQL Server virtuella datorer. Välj sedan den **SQL Server-konfigurationsfilen** avsnitt i den **inställningar** bladet.

![SQL automatisk säkerhetskopiering av befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

I den **SQL Server-konfigurationsfilen** bladet, klickar du på den **redigera** i avsnittet automatisk säkerhetskopiering.

![Konfigurera automatisk säkerhetskopiering i SQL för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

När du är klar klickar du på den **OK** knappen längst ned på den **SQL Server-konfigurationsfilen** bladet för att spara dina ändringar.

Om du aktiverar automatisk säkerhetskopiering för första gången, konfigurerar Azure SQL Server IaaS-Agent i bakgrunden. Under denna tid kanske Azure-portalen inte visar att automatisk säkerhetskopiering har konfigurerats. Vänta några minuter för att agenten ska installeras, konfigureras. Efter det att Azure-portalen visar de nya inställningarna.

> [!NOTE]
> Du kan också konfigurera automatisk säkerhetskopiering med en mall. Mer information finns i [Azure quickstart-mall för automatisk säkerhetskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Med PowerShell

Du kan använda PowerShell för att konfigurera automatisk säkerhetskopiering. Innan du börjar måste du:

- [Hämta och installera den senaste Azure PowerShell](http://aka.ms/webpi-azps).
- Öppna Windows PowerShell och associera den med ditt konto. Du kan göra detta genom att följa stegen i den [konfigurera prenumerationen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) under avsnittet etablering.

### <a name="install-the-sql-iaas-extension"></a>Installera SQL IaaS-tillägg
Om du har etablerat en SQL Server-datorn från Azure portal bör SQL Server IaaS-tillägget vara installerad. Du kan fastställa om det har installerats för den virtuella datorn genom att anropa **Get-AzureRmVM** kommandot och undersöka den **tillägg** egenskapen.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Du bör se den visas som ”SqlIaaSAgent” eller ”SQLIaaSExtension” om tillägget SQL Server IaaS-Agent är installerad. **ProvisioningState** för tillägget bör också visa ”lyckades”.

Om det inte är installerat eller inte fungerar som ska etableras, kan du installera det med följande kommando. Utöver VM namn och resursen gruppen, måste du också ange regionen (**$region**) som den virtuella datorn finns i.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

### <a id="verifysettings"></a>Kontrollera aktuella inställningar

Om du har aktiverat automatisk säkerhetskopiering under etableringen kan du använda PowerShell för att kontrollera den aktuella konfigurationen. Kör den **Get-AzureRmVMSqlServerExtension** kommando och undersöka den **AutoBackupSettings** egenskapen:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
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

Om din utdata visar att **aktivera** är inställd på **FALSKT**, och sedan att aktivera automatisk säkerhetskopiering. Goda nyheter är att aktivera och konfigurera automatisk säkerhetskopiering på samma sätt. Finns i nästa avsnitt för den här informationen.

> [!NOTE] 
> Om du kontaktar inställningarna innan du gör en ändring, är det möjligt att du kommer tillbaka gamla konfigurationsvärden. Vänta en stund och kontrollera inställningarna igen för att säkerställa att ändringarna har tillämpats.

### <a name="configure-automated-backup"></a>Konfigurera automatisk säkerhetskopiering
Du kan använda PowerShell för att aktivera automatisk säkerhetskopiering samt för att ändra dess konfiguration och funktionalitet när som helst.

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
> Automatisk säkerhetskopiering stöder inte lagra säkerhetskopior i premium-lagring, men det kan göra säkerhetskopior från Virtuella diskar som använder Premium-lagring.

Använd sedan den **ny AzureRmVMSqlServerAutoBackupConfig** kommando för att aktivera och konfigurera inställningar för automatisk säkerhetskopiering för att lagra säkerhetskopior i Azure storage-konto. I det här exemplet ställs säkerhetskopiorna ska behållas i 10 dagar. Det andra kommandot **Set AzureRmVMSqlServerExtension**, uppdaterar den angivna virtuella Azure-datorn med de här inställningarna.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-Agent.

> [!NOTE]
> Det finns andra inställningar för **ny AzureRmVMSqlServerAutoBackupConfig** som gäller bara för SQL Server 2016 och automatisk säkerhetskopiering v2. SQL Server 2014 stöder inte följande inställningar: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**, och **LogBackupFrequencyInMinutes**. Om du försöker konfigurera inställningarna på en virtuell dator för SQL Server 2014 inga fel, men inte hämta tillämpas inställningarna. Om du vill använda de här inställningarna på en virtuell dator för SQL Server 2016 [automatisk säkerhetskopiering v2 för SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

Om du vill aktivera kryptering, ändra tidigare skriptet skickar den **EnableEncryption** parametern tillsammans med ett lösenord (säker sträng) för den **CertificatePassword** parametern. Följande skript gör inställningar för automatisk säkerhetskopiering i föregående exempel och lägger till kryptering.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Bekräfta dina inställningar tillämpas [verifiera konfigurationen av automatisk säkerhetskopiering](#verifysettings).

### <a name="disable-automated-backup"></a>Inaktivera automatisk säkerhetskopiering

Om du vill inaktivera automatisk säkerhetskopiering, kör du skriptet samma utan den **-aktivera** parametern till den **ny AzureRmVMSqlServerAutoBackupConfig** kommando. Avsaknad av den **-aktivera** parametern signalerar kommandot för att inaktivera funktionen. Precis som med installationen, kan det ta flera minuter att inaktivera automatisk säkerhetskopiering.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Exempelskriptet

Följande skript innehåller en uppsättning variabler som du kan anpassa om du vill aktivera och konfigurera automatisk säkerhetskopiering för den virtuella datorn. I ditt fall kan du behöva anpassa skriptet baserat på dina krav. Du skulle behöva ändra om du vill aktivera kryptering eller inaktivera säkerhetskopiering för systemdatabaser.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Automatisk säkerhetskopiering konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Därför är det viktigt att [Läs dokumentationen om hanterad säkerhetskopiering](https://msdn.microsoft.com/library/dn449496.aspx) att förstå beteendet och konsekvenser.

Du kan hitta ytterligare en säkerhetskopia och återställa riktlinjerna för SQL Server på virtuella Azure-datorer i följande avsnitt: [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Information om andra tillgängliga automation-aktiviteter finns [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

