---
title: Automatisk uppdatering för SQL Server-datorer (klassisk) | Microsoft Docs
description: Beskriver funktionen för automatisk uppdatering för SQL Server-datorer som körs i Azure med hjälp av det klassiska distributionsläget.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa912e3eb76d72e7a79c83d7e51d493310bd36b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362143"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisk uppdatering för SQLServer i Azure Virtual Machines (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../classic/sql-automated-patching.md)
> 
> 

Automatisk uppdatering upprättar en underhållsperiod för en Azure virtuell dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL Server säkerställer detta att uppdateringar och eventuella tillhörande omstarter klockan på bästa möjliga tid för databasen. 

> [!IMPORTANT]
> Endast Windows-uppdateringar som är markerade som **Viktiga** installeras. Andra SQL Server-uppdateringar, till exempel kumulativa uppdateringar, måste installeras manuellt. 

Automatisk uppdatering är beroende av [SQL Server IaaS Agent-tillägget](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-versionen av den här artikeln finns [automatisk uppdatering för SQL Server i Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
För att använda automatisk uppdatering, Överväg följande krav:

**Operativsystemet**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-version**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

**SQL Server IaaS-tillägget**:

* [Installera SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk uppdatering. För klassiska virtuella datorer, måste du konfigurera dessa inställningar med hjälp av PowerShell.

| Inställning | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatisk uppdatering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar automatisk uppdatering för en Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schema för att hämta och installera uppdateringar för Windows, SQL Server och Microsoft för den virtuella datorn. |
| **Starttid för underhåll** |0-24 |Lokalt starttid att uppdatera den virtuella datorn. |
| **Underhållsfönster** |30-180 |Hur många minuter som har behörighet att slutföra hämtningen och installationen av uppdateringar. |
| **Patch-kategori** |Viktigt |Kategori med uppdateringar att ladda ned och installera. |

## <a name="configuration-with-powershell"></a>Med PowerShell
I följande exempel används PowerShell för att konfigurera automatisk uppdatering på en befintlig SQL Server-dator. Den **New AzureVMSqlServerAutoPatchingConfig** kommando konfigurerar en ny underhållsperiod för automatiska uppdateringar.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Utifrån det här exemplet beskrivs i följande tabell i praktiken på Virtuella Azure-måldatorn:

| Parameter | Verkan |
| --- | --- |
| **dayOfWeek** |Korrigeringar installeras varje torsdag. |
| **MaintenanceWindowStartingHour** |Begin uppdaterar kl. 11:00. |
| **MaintenanceWindowDuration** |Korrigeringar installeras inom 120 minuter. Baserat på starttiden, måste de utföra klockan 1:00. |
| **PatchCategory** |Den enda möjliga värdet på parametern är ”viktigt”. |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS Agent.

Om du vill inaktivera automatisk uppdatering, kör samma skript utan att aktivera parametern - att New-AzureVMSqlServerAutoPatchingConfig. Precis som med installationen, kan det ta flera minuter att inaktivera automatisk uppdatering.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga automation-aktiviteter finns i [SQL Server IaaS Agent-tillägget](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

