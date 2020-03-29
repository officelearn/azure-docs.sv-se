---
title: Automatisk säkerhetskopiering för virtuella SQL Server-datorer (Klassisk) | Microsoft-dokument
description: 'I artikeln beskrivs funktionen Automatisk säkerhetskopiering för SQL Server som körs i Virtuella Azure-datorer med Hjälp av Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ff230d4769a23c9007b3da29858d2105366f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978107"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisk säkerhetskopiering för SQL Server i virtuella Azure-datorer (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klassisk](../classic/sql-automated-backup.md)
> 
> 

Automatisk säkerhetskopiering konfigurerar automatiskt [hanterad säkerhetskopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en Azure VM som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera vanliga säkerhetskopieringar av databaser som använder varaktig Azure-bloblagring. Automatisk säkerhetskopiering beror på [TILLÄGGET FÖR SQL Server IaaS-agent .](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om hur du visar Resource Manager-versionen av den här artikeln finns [i Automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Krav
Om du vill använda automatisk säkerhetskopiering bör du tänka på följande förutsättningar:

**Operativsystem:**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server version/utgåva:**

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatisk säkerhetskopiering för SQL Server 2016 stöds med virtuella resurser och resurser. Mer information finns i [Automatisk säkerhetskopiering v2 för SQL Server 2016 Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Databaskonfiguration:**

* Måldatabaser måste använda den fullständiga återställningsmodellen.

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

**SQL Server IaaS-tillägg:**

* [Installera SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhetskopiering. För klassiska virtuella datorer måste du använda PowerShell för att konfigurera dessa inställningar.

| Inställning | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar automatisk säkerhetskopiering för en Azure VM som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperiod** |1-30 dagar (30 dagar) |Antalet dagar för att behålla en säkerhetskopia. |
| **Lagringskonto** |Azure-lagringskonto (lagringskontot som skapats för den angivna virtuella datorn) |Ett Azure-lagringskonto som ska användas för lagring av automatisk säkerhetskopieringsfiler i blob-lagring. En behållare skapas på den här platsen för att lagra alla säkerhetskopior. Namngivningskonventionen för säkerhetskopian innehåller datum, tid och datornamn. |
| **Kryptering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat finns de certifikat som används för att återställa säkerhetskopian i det angivna lagringskontot i samma automaticbackup-behållare med samma namngivningskonvention. Om lösenordet ändras genereras ett nytt certifikat med det lösenordet, men det gamla certifikatet återstår att återställa tidigare säkerhetskopior. |
| **Lösenord** |Lösenordstext (Ingen) |Ett lösenord för krypteringsnycklar. Detta krävs endast om kryptering är aktiverat. För att återställa en krypterad säkerhetskopia måste du ha rätt lösenord och relaterat certifikat som användes när säkerhetskopieringen gjordes. |
| **Säkerhetskopiera systemdatabaser** | Aktivera/inaktivera (inaktiverad) | Gör fullständiga säkerhetskopior av Master, Model och MSDB |
| **Konfigurera schema för säkerhetskopiering** | Manuell/automatiserad (automatiserad) | Välj **Automatiserad** om du vill ta fullständiga säkerhetskopior automatiskt och logga säkerhetskopiering baserat på loggtillväxt. Välj **Manuell** om du vill ange schemat för fullständiga säkerhetskopior och logga säkerhetskopior. |

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell
I följande PowerShell-exempel konfigureras automatisk säkerhetskopiering för en befintlig VIRTUELL SQL Server 2014.In the following PowerShell example, Automated Backup is configured for an existing SQL Server 2014 VM. Kommandot **New-AzureVMSqlServerAutoBackupConfig** konfigurerar inställningarna för automatisk säkerhetskopiering för att lagra säkerhetskopior i Azure-lagringskontot som anges av variabeln $storageaccount. Dessa säkerhetskopior kommer att behållas i 10 dagar. **Kommandot Set-AzureVMSqlServerExtension** uppdaterar den angivna Azure VM-datorn med dessa inställningar.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

Om du vill aktivera kryptering ändrar du det föregående skriptet för att skicka parametern EnableEncryption tillsammans med ett lösenord (säker sträng) för parametern CertificatePassword. Följande skript aktiverar inställningarna för automatisk säkerhetskopiering i föregående exempel och lägger till kryptering.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Om du vill inaktivera automatisk säkerhetskopiering kör du samma skript utan **parametern -Enable** till **parametern New-AzureVMSqlServerAutoBackupConfig**. Som med installationen kan det ta flera minuter att inaktivera automatisk säkerhetskopiering.

> [!NOTE]
> Om du inaktiverar och avinstallerar SQL Server IaaS-agenten tas inte de tidigare konfigurerade inställningarna för hanterad säkerhetskopiering bort. Du bör inaktivera automatisk säkerhetskopiering innan du inaktiverar eller avinstallerar SQL Server IaaS-agenten.
> 
> 

## <a name="next-steps"></a>Nästa steg
Automatisk säkerhetskopiering konfigurerar hanterad säkerhetskopiering på virtuella Azure-datorer. Därför är det viktigt att [granska dokumentationen för Managed Backup för](https://msdn.microsoft.com/library/dn449496.aspx) att förstå beteendet och konsekvenserna.

Du hittar ytterligare vägledning för säkerhetskopiering och återställning för SQL Server på virtuella Azure-datorer i följande avsnitt: [Säkerhetskopiering och återställning för SQL Server i Virtuella Azure-datorer](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Information om andra tillgängliga automatiseringsuppgifter finns i [SQL Server IaaS Agent Extension](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

