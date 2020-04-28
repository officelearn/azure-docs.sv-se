---
title: Automatiserad uppdatering för SQL Server virtuella datorer (klassisk) | Microsoft Docs
description: Förklarar automatisk uppdaterings funktion för SQL Server Virtual Machines som körs i Azure med det klassiska distributions läget.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978086"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatiserad uppdatering för SQL Server i Azure Virtual Machines (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../classic/sql-automated-patching.md)
> 
> 

Automatisk uppdatering upprättar en underhållsperiod för en virtuell Azure-dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL Server säkerställer detta att system uppdateringar och eventuella associerade omstarter sker på bästa möjliga tid för databasen. 

> [!IMPORTANT]
> Endast Windows-uppdateringar som är markerade som **Viktiga** installeras. Andra SQL Server-uppdateringar, till exempel kumulativa uppdateringar, måste installeras manuellt. 

Automatisk uppdatering är beroende av [SQL Server IaaS Agent-tillägget](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du vill visa Resource Manager-versionen av den här artikeln, se [automatiserad uppdatering för SQL Server i Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Krav
Om du vill använda automatisk uppdatering bör du tänka på följande:

**Operativ system**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server version**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

**SQL Server IaaS-tillägg**:

* [Installera SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk uppdatering. För klassiska virtuella datorer måste du använda PowerShell för att konfigurera de här inställningarna.

| Inställning | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatiserad uppdatering** |Aktivera/inaktivera (inaktive rad) |Aktiverar eller inaktiverar automatisk uppdatering för en virtuell Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schemat för att ladda ned och installera Windows-, SQL Server-och Microsoft-uppdateringar för den virtuella datorn. |
| **Start timme för underhåll** |0-24 |Den lokala Start tiden för att uppdatera den virtuella datorn. |
| **Underhålls periodens varaktighet** |30-180 |Antalet minuter som tillåts att slutföra nedladdningen och installationen av uppdateringar. |
| **Uppdaterings kategori** |Viktigt |Kategori för uppdateringar som ska hämtas och installeras. |

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell
I följande exempel används PowerShell för att konfigurera automatiserad uppdatering på en befintlig SQL Server VM. Kommandot **New-AzureVMSqlServerAutoPatchingConfig** konfigurerar en ny underhålls period för automatiska uppdateringar.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

I följande tabell beskrivs den praktiska påverkan på den virtuella Azure-datorn:

| Parameter | Verkan |
| --- | --- |
| **DayOfWeek** |Uppdateringar installeras varje torsdag. |
| **MaintenanceWindowStartingHour** |Starta uppdateringar vid 11:10:00. |
| **MaintenanceWindowDuration** |Korrigeringsfiler måste installeras inom 120 minuter. Baserat på Start tiden måste de slutföras med 1:12:00. |
| **PatchCategory** |Den enda möjliga inställningen för den här parametern är "viktigt". |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

Om du vill inaktivera automatisk uppdatering kör du samma skript utan parametern-Enable till New-AzureVMSqlServerAutoPatchingConfig. Precis som vid installationen kan det ta flera minuter att inaktivera automatisk uppdatering.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga Automation-uppgifter finns [SQL Server IaaS agent Extension](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns [SQL Server på azure Virtual Machines-översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

