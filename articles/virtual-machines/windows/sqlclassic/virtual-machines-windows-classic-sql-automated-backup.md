---
title: "Automatisk säkerhetskopiering i SQL Server-datorer (klassisk) | Microsoft Docs"
description: "Beskriver funktionen automatisk säkerhetskopiering för SQL Server som körs i Azure Virtual Machines med Resource Manager. "
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: f7664291c2f45c422d52f682d08dbb67ab32b099
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisk säkerhetskopiering för SQLServer på virtuella Azure-datorer (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klassisk](../classic/sql-automated-backup.md)
> 
> 

Automatisk säkerhetskopiering konfigurerar automatiskt [hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure-dator som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera regelbundna säkerhetskopieringar som använder beständiga Azure blob storage. Automatisk säkerhetskopiering är beroende av den [tillägg för SQL Server IaaS Agent](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-versionen av den här artikeln finns [automatisk säkerhetskopiering för SQL Server i Azure virtuella datorer Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Förutsättningar
Överväg följande krav för att använda automatisk säkerhetskopiering:

**Operativsystemet**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-version/utgåva**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016 ännu inte har stöd för automatisk säkerhetskopiering.
> 
> 

**Databaskonfiguration**:

* Måldatabaserna måste använda den fullständiga återställningsmodellen.

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandon](/powershell/azure/overview).

**SQL Server IaaS tillägget**:

* [Installera SQL Server IaaS Extension](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering. För klassiska virtuella datorer, måste du använda PowerShell för att konfigurera dessa inställningar.

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** |Aktivera/inaktivera (inaktiverat) |Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure-dator som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperioden** |1 – 30 dagar (30 dagar) |Antal dagar att behålla en säkerhetskopia. |
| **Storage-konto** |Azure storage-konto (lagringskonto som skapats för den angivna virtuella datorn) |Ett Azure storage-konto som ska användas för att lagra filer för automatisk säkerhetskopiering i blob storage. En behållare skapas på denna plats att lagra säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och namnet på datorn. |
| **Kryptering** |Aktivera/inaktivera (inaktiverat) |Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat finns certifikat som används för att återställa säkerhetskopian i det angivna lagringskontot i samma automaticbackup behållare med samma namngivningskonvention. Om lösenordet ändras, skapas ett nytt certifikat med lösenordet, men det gamla certifikatet finns kvar för att återställa tidigare säkerhetskopior. |
| **Lösenord** |Lösenord text (ingen) |Ett lösenord för krypteringsnycklar. Detta är endast krävs om kryptering är aktiverat. Du måste ha rätt lösenord och relaterade certifikatet som användes när säkerhetskopian skapades för att återställa en krypterad säkerhetskopiering. | **Säkerhetskopieringssystem databaser** | Aktivera/inaktivera (inaktiverat) | Ta och fullständiga säkerhetskopieringar av Master, Model och MSDB |
| **Konfigurera schemat för säkerhetskopiering** | Manuell/automatisk (automatisk) | Välj **automatisk** automatiskt fullständig och loggsäkerhetskopior baserat på loggen tillväxt. Välj **manuell** att ange schemat för fullständig och loggsäkerhetskopior. |

## <a name="configuration-with-powershell"></a>Med PowerShell
I exemplet nedan PowerShell har automatisk säkerhetskopiering konfigurerats för en befintlig SQL Server 2014-VM. Den **ny AzureVMSqlServerAutoBackupConfig** kommando konfigurerar inställningar för automatisk säkerhetskopiering för att lagra säkerhetskopior i Azure storage-konto som angetts av variabeln $storageaccount. Dessa säkerhetskopior ska behållas i 10 dagar. Den **Set AzureVMSqlServerExtension** kommandot uppdaterar den angivna virtuella Azure-datorn med de här inställningarna.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-Agent.

Ändra föregående skript om du vill skicka EnableEncryption-parametern tillsammans med ett lösenord (säker sträng) för parametern CertificatePassword för att aktivera kryptering. Följande skript gör inställningar för automatisk säkerhetskopiering i föregående exempel och lägger till kryptering.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Om du vill inaktivera automatisk säkerhetskopiering, kör du skriptet samma utan den **-aktivera** parametern till den **ny AzureVMSqlServerAutoBackupConfig**. Precis som med installationen, kan det ta flera minuter att inaktivera automatisk säkerhetskopiering.

> [!NOTE]
> Inaktivering och avinstallerar SQL Server IaaS-Agent tar inte bort de tidigare konfigurerade inställningarna för hanterad säkerhetskopiering. Du bör inaktivera automatisk säkerhetskopiering innan du inaktiverar eller avinstallerar SQL Server IaaS-Agent.
> 
> 

## <a name="next-steps"></a>Nästa steg
Automatisk säkerhetskopiering konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Därför är det viktigt att [Läs dokumentationen om hanterad säkerhetskopiering](https://msdn.microsoft.com/library/dn449496.aspx) att förstå beteendet och konsekvenser.

Du kan hitta ytterligare en säkerhetskopia och återställa riktlinjerna för SQL Server på virtuella Azure-datorer i följande avsnitt: [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Information om andra tillgängliga automation-aktiviteter finns [tillägg för SQL Server IaaS Agent](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

