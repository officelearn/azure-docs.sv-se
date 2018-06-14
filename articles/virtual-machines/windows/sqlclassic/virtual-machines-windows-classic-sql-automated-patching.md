---
title: Automatisk uppdatering för SQL Server-datorer (klassisk) | Microsoft Docs
description: Beskriver funktionen för automatisk uppdatering för SQL Server virtuella datorer som körs i Azure med hjälp av klassisk distribution.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 1da89bbf88e9f1f2105e7afab5c3fdabcecdbc64
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852718"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisk uppdatering för SQLServer på virtuella Azure-datorer (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../classic/sql-automated-patching.md)
> 
> 

Automatisk korrigering upprättar en underhållsperiod för en Azure-dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL Server säkerställer detta att uppdateringar och eventuella tillhörande omstarter inträffar på den bästa möjliga tid för databasen. 

> [!IMPORTANT]
> Endast Windows-uppdateringar som är markerade **viktigt** är installerade. Andra SQL Server-uppdateringar, till exempel kumulativa uppdateringar måste installeras manuellt. 

Automatisk korrigering beror på den [tillägg för SQL Server IaaS Agent](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-versionen av den här artikeln finns [automatisk uppdatering för SQL Server i Azure virtuella datorer Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda automatisk uppdatering, Överväg följande krav:

**Operativsystemet**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-version**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandon](/powershell/azure/overview).

**SQL Server IaaS tillägget**:

* [Installera SQL Server IaaS Extension](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk uppdatering. För klassiska virtuella datorer, måste du använda PowerShell för att konfigurera dessa inställningar.

| Inställning | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatisk uppdatering** |Aktivera/inaktivera (inaktiverat) |Aktiverar eller inaktiverar automatisk uppdatering för en virtuell Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schema för att hämta och installera Windows, SQL Server och Microsoft-uppdateringar för den virtuella datorn. |
| **Starttimme för underhåll** |0-24 |Lokalt starttid för att uppdatera den virtuella datorn. |
| **Underhåll fönstervaraktigheten** |30-180 |Antal minuter som tillåts att slutföra hämtningen och installationen av uppdateringar. |
| **Patch-kategori** |Viktigt |Hämta och installera uppdateringar kategori. |

## <a name="configuration-with-powershell"></a>Med PowerShell
I följande exempel används PowerShell för att konfigurera automatisk uppdatering på en befintlig SQL Server-VM. Den **ny AzureVMSqlServerAutoPatchingConfig** kommando konfigurerar en ny underhållsperiod för automatiska uppdateringar.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Baserat på det här exemplet beskrivs i följande tabell i praktiken på målet virtuella Azure-datorn:

| Parameter | Verkan |
| --- | --- |
| **DayOfWeek** |Korrigeringsprogram installerade varje torsdag. |
| **MaintenanceWindowStartingHour** |Begin uppdateringar på 11:00:00. |
| **MaintenanceWindowsDuration** |Korrigeringsfiler måste installeras inom 120 minuter. Baserat på starttid, måste de utföra av 1:00 pm. |
| **PatchCategory** |Inställningen bara möjligt för den här parametern är ”viktigt”. |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-Agent.

Om du vill inaktivera automatisk uppdatering, kör du samma skript utan att aktivera parametern - att New-AzureVMSqlServerAutoPatchingConfig. Precis som med installationen, kan det ta flera minuter att inaktivera automatisk uppdatering.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga automation-aktiviteter finns [tillägg för SQL Server IaaS Agent](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

