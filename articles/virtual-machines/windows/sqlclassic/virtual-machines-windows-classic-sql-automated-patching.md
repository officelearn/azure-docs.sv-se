---
title: Automatisk korrigering för virtuella SQL Server-datorer (Klassisk) | Microsoft-dokument
description: I artikeln beskrivs funktionen Automatisk korrigering för virtuella SQL Server-datorer som körs i Azure med det klassiska distributionsläget.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978086"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisk korrigering för SQL Server i virtuella Azure-datorer (Klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../classic/sql-automated-patching.md)
> 
> 

Automatisk uppdatering upprättar en underhållsperiod för en virtuell Azure-dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL Server säkerställer detta att systemuppdateringar och eventuella associerade omstarter sker vid bästa möjliga tidpunkt för databasen. 

> [!IMPORTANT]
> Endast Windows-uppdateringar som är markerade som **Viktiga** installeras. Andra SQL Server-uppdateringar, till exempel kumulativa uppdateringar, måste installeras manuellt. 

Automatisk uppdatering är beroende av [SQL Server IaaS Agent-tillägget](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om hur du visar Resource Manager-versionen av den här artikeln finns [i Automatisk korrigering för SQL Server i Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Krav
Om du vill använda automatisk korrigering bör du tänka på följande förutsättningar:

**Operativsystem:**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-version:**

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

**SQL Server IaaS-tillägg:**

* [Installera SQL Server IaaS-tillägget](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk korrigering. För klassiska virtuella datorer måste du använda PowerShell för att konfigurera dessa inställningar.

| Inställning | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatisk korrigering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar automatisk korrigering för en virtuell Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schemat för hämtning och installation av Windows-, SQL Server- och Microsoft-uppdateringar för den virtuella datorn. |
| **Underhållsstarttimme** |0-24 |Den lokala starttiden för att uppdatera den virtuella datorn. |
| **Varaktighet för underhållsfönstret** |30-180 |Antalet minuter som tillåts för att slutföra hämtning och installation av uppdateringar. |
| **Korrigeringsfil** |Viktigt |Kategorin uppdateringar att hämta och installera. |

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell
I följande exempel används PowerShell för att konfigurera automatisk korrigering på en befintlig VIRTUELL SQL Server.In the following example, PowerShell is used to configure Automated Patching on an existing SQL Server VM. Kommandot **New-AzureVMSqlServerAutoPatchingConfig** konfigurerar ett nytt underhållsfönster för automatiska uppdateringar.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Baserat på det här exemplet beskriver följande tabell den praktiska effekten på målet Azure VM:

| Parameter | Verkan |
| --- | --- |
| **Dagiveck** |Patchar installeras varje torsdag. |
| **UnderhållWindowStartingHour** |Börja uppdateringarna klockan 11:00. |
| **UnderhållFönster** |Patchar måste installeras inom 120 minuter. Baserat på starttiden måste de vara klara senast 13:00. |
| **PatchCategory** |Den enda möjliga inställningen för den här parametern är "Viktigt". |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

Om du vill inaktivera automatisk korrigering kör du samma skript utan parametern -Enable till parametern New-AzureVMSqlServerAutoPatchingConfig. Som med installationen kan det ta flera minuter att inaktivera automatisk korrigering.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga automatiseringsuppgifter finns i [SQL Server IaaS Agent Extension](../classic/sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

