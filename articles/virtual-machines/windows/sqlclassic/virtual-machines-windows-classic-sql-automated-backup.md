---
title: Automatisk säkerhetskopiering för SQL Server-datorer (klassisk) | Microsoft Docs
description: 'Beskriver funktionen automatisk säkerhetskopiering för SQL Server som körs i Azure virtuella datorer med Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aeb97d661d330ed6afb3ca5e5e1eb924dacc4024
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096307"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatiserad säkerhetskopiering för SQLServer i Azure Virtual Machines (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klassisk](../classic/sql-automated-backup.md)
> 
> 

Automatisk säkerhetskopiering konfigureras automatiskt [hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure-dator som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera regelbundna databassäkerhetskopieringar som använder varaktiga Azure blob-lagring. Automatisk säkerhetskopiering beror på den [SQL Server IaaS Agent-tillägget](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-versionen av den här artikeln finns [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Förutsättningar
Överväg följande krav för att använda automatisk säkerhetskopiering:

**Operativsystemet**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server version/utgåva**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatiserad säkerhetskopiering för SQL Server 2016 stöds med Resource Manager-datorer. Mer information finns i [v2 för automatisk säkerhetskopiering för SQL Server 2016 Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Databaskonfiguration**:

* Måldatabaserna måste använda den fullständiga återställningsmodellen.

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

**SQL Server IaaS-tillägget**:

* [Installera SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering. För klassiska virtuella datorer, måste du konfigurera dessa inställningar med hjälp av PowerShell.

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure virtuell dator som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperiod** |1 – 30 dagar (30 dagar) |Antal dagar att behålla en säkerhetskopia. |
| **Lagringskonto** |Azure storage-konto (det storage-konto som är skapade för den angivna virtuella datorn) |Ett Azure storage-konto som ska användas för att lagra filer för automatisk säkerhetskopiering i blob storage. En behållare skapas på den här platsen för att lagra säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och namnet på datorn. |
| **Kryptering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar kryptering. När kryptering har aktiverats, finns de certifikat som används för att återställa säkerhetskopian i det angivna lagringskontot i samma automaticbackup behållare med hjälp av samma namngivningskonvention. Om lösenordet ändras, skapas ett nytt certifikat med lösenordet, men det gamla certifikatet förblir för att återställa tidigare säkerhetskopior. |
| **Lösenord** |Lösenord text (ingen) |Ett lösenord för krypteringsnycklar. Detta är endast krävs om kryptering är aktiverat. Om du vill återställa en krypterad säkerhetskopiering måste du ha rätt lösenord och relaterade certifikatet som användes när säkerhetskopian skapades. |
| **Säkerhetskopiera systemdatabaser** | Aktivera/inaktivera (inaktiverad) | Ta fullständiga säkerhetskopior av Master, Model och MSDB |
| **Konfigurera schema för säkerhetskopiering** | Manuell/automatisk (automatisk) | Välj **automatisk** automatiskt fullständiga och loggsäkerhetskopior baserat på log tillväxt. Välj **manuell** att ange schemat för fullständiga säkerhetskopior och loggsäkerhetskopior. |

## <a name="configuration-with-powershell"></a>Med PowerShell
Automatisk säkerhetskopiering har konfigurerats för en befintlig SQL Server 2014 virtuell dator i följande PowerShell-exempel. Den **New AzureVMSqlServerAutoBackupConfig** kommando konfigurerar inställningarna för automatisk säkerhetskopiering för att lagra säkerhetskopior i Azure storage-kontot som angetts av variabeln $storageaccount. Dessa säkerhetskopior bevaras i 10 dagar. Den **Set-AzureVMSqlServerExtension** kommandot uppdaterar den angivna virtuella Azure-datorer med de här inställningarna.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS Agent.

Om du vill aktivera kryptering, ändrar du föregående skript om du vill skicka parametern EnableEncryption tillsammans med ett lösenord (säker sträng) för parametern CertificatePassword. Följande skript gör att inställningarna för automatisk säkerhetskopiering i exemplet ovan och lägger till kryptering.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Om du vill inaktivera automatisk säkerhetskopiering, kör samma skript utan den **-aktivera** parametern till den **New AzureVMSqlServerAutoBackupConfig**. Precis som med installationen, kan det ta flera minuter att inaktivera automatisk säkerhetskopiering.

> [!NOTE]
> Inaktivera och avinstallerar SQL Server IaaS Agent tar inte bort de tidigare konfigurerade inställningarna för hanterad säkerhetskopiering. Innan du inaktiverar eller avinstallera SQL Server IaaS Agent bör du inaktivera automatisk säkerhetskopiering.
> 
> 

## <a name="next-steps"></a>Nästa steg
Automatisk säkerhetskopiering konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Så det är viktigt att [Läs dokumentationen för hanterad säkerhetskopiering](https://msdn.microsoft.com/library/dn449496.aspx) att förstå beteende och konsekvenser.

Du kan hitta ytterligare säkerhetskopia och återställa riktlinjerna för SQL Server på virtuella Azure-datorer i följande avsnitt: [Säkerhetskopiering och återställning för SQLServer i Azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Information om andra tillgängliga automation-aktiviteter finns i [SQL Server IaaS Agent-tillägget](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

