---
title: Automatisk säkerhets kopiering för SQL Server Virtual Machines (klassisk) | Microsoft Docs
description: 'Förklarar funktionen automatisk säkerhets kopiering för SQL Server som körs i Azure Virtual Machines med hjälp av Resource Manager. '
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
ms.openlocfilehash: c792b217f49121b6d3d6eaf2d8f8380997683bd8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014680"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatiserad säkerhets kopiering för SQL Server i Azure Virtual Machines (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
> * [Klassisk](../classic/sql-automated-backup.md)
> 
> 

Automatisk säkerhets kopiering konfigurerar automatiskt [hanterad säkerhets kopiering till Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) för alla befintliga och nya databaser på en virtuell Azure-dator som kör SQL Server 2014 Standard eller Enterprise. På så sätt kan du konfigurera regelbundna säkerhets kopieringar av databaser som använder varaktiga Azure Blob Storage. Automatiserad säkerhets kopiering beror på [SQL Server IaaS agent Extension](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du vill visa Resource Manager-versionen av den här artikeln, se [Automatisk säkerhets kopiering för SQL Server i Azure Virtual Machines Resource Manager](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda automatisk säkerhets kopiering bör du tänka på följande:

**Operativ system**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server version/utgåva**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatisk säkerhets kopiering för SQL Server 2016 stöds med virtuella Resource Manager-datorer. Mer information finns i [Automatisk säkerhets kopiering v2 för SQL Server 2016 Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Databas konfiguration**:

* Mål databaserna måste använda den fullständiga återställnings modellen.

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

**SQL Server IaaS-tillägg**:

* [Installera SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk säkerhets kopiering. För klassiska virtuella datorer måste du använda PowerShell för att konfigurera de här inställningarna.

| Inställningen | Intervall (standard) | Beskrivning |
| --- | --- | --- |
| **Automatisk säkerhetskopiering** |Aktivera/inaktivera (inaktive rad) |Aktiverar eller inaktiverar automatisk säkerhets kopiering för en virtuell Azure-dator som kör SQL Server 2014 Standard eller Enterprise. |
| **Kvarhållningsperiod** |1-30 dagar (30 dagar) |Antalet dagar som säkerhets kopian ska sparas. |
| **Lagringskonto** |Azure Storage-konto (lagrings kontot som skapats för den angivna virtuella datorn) |Ett Azure Storage-konto som ska användas för att lagra automatiska säkerhetskopieringsfiler i Blob Storage. En behållare skapas på den här platsen för att lagra alla säkerhetskopieringsfiler. Fil namns konventionen för säkerhets kopiering innehåller datum, tid och dator namn. |
| **Kryptering** |Aktivera/inaktivera (inaktive rad) |Aktiverar eller inaktiverar kryptering. När kryptering är aktiverat finns de certifikat som används för att återställa säkerhets kopian i det angivna lagrings kontot i samma automaticbackup-behållare med samma namngivnings konvention. Om lösen ordet ändras genereras ett nytt certifikat med det lösen ordet, men det gamla certifikatet kvarstår för att återställa tidigare säkerhets kopior. |
| **Lösenord** |Lösen ords text (ingen) |Ett lösen ord för krypterings nycklar. Detta krävs endast om kryptering har Aktiver ATS. För att kunna återställa en krypterad säkerhets kopia måste du ha rätt lösen ord och relaterat certifikat som användes när säkerhets kopieringen gjordes. |
| **Säkerhetskopiera systemdatabaser** | Aktivera/inaktivera (inaktive rad) | Ta fullständig säkerhets kopiering av Master, Model och MSDB |
| **Konfigurera schema för säkerhetskopiering** | Manuellt/automatiserat (automatiserat) | Välj **Automatisk** för att automatiskt ta full säkerhets kopior och logg säkerhets kopior baserat på logg tillväxt. Välj **manuell** om du vill ange schemat för fullständiga säkerhets kopior och logg säkerhets kopior. |

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell
I följande PowerShell-exempel konfigureras automatisk säkerhets kopiering för en befintlig SQL Server 2014 VM. Kommandot **New-AzureVMSqlServerAutoBackupConfig** konfigurerar inställningarna för automatisk säkerhets kopiering för lagring av säkerhets kopior i Azure Storage-kontot som anges av variabeln $storageaccount. Dessa säkerhets kopior kommer att behållas i 10 dagar. Kommandot **set-AzureVMSqlServerExtension** uppdaterar den angivna virtuella Azure-datorn med de här inställningarna.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

Om du vill aktivera kryptering ändrar du det tidigare skriptet för att skicka parametern EnableEncryption tillsammans med ett lösen ord (säker sträng) för parametern CertificatePassword. Följande skript aktiverar de automatiska säkerhets kopierings inställningarna i föregående exempel och lägger till kryptering.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Om du vill inaktivera automatisk säkerhets kopiering kör du samma skript utan parametern **-Enable** till **New-AzureVMSqlServerAutoBackupConfig**. Precis som vid installationen kan det ta flera minuter att inaktivera automatisk säkerhets kopiering.

> [!NOTE]
> Att inaktivera och avinstallera SQL Server IaaS-agenten tar inte bort tidigare konfigurerade inställningar för hanterad säkerhets kopiering. Du bör inaktivera automatisk säkerhets kopiering innan du inaktiverar eller avinstallerar SQL Server IaaS-agenten.
> 
> 

## <a name="next-steps"></a>Nästa steg
Automatisk säkerhets kopiering konfigurerar hanterad säkerhets kopiering på virtuella Azure-datorer. Det är därför viktigt att [granska dokumentationen för hanterad säkerhets kopiering](https://msdn.microsoft.com/library/dn449496.aspx) för att förstå beteendet och konsekvenserna.

Du hittar ytterligare vägledning för säkerhets kopiering och återställning av SQL Server på virtuella Azure-datorer i följande avsnitt: [säkerhets kopiering och återställning för SQL Server i Azure-Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Information om andra tillgängliga Automation-uppgifter finns [SQL Server IaaS agent Extension](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns [SQL Server på azure Virtual Machines-översikt](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

