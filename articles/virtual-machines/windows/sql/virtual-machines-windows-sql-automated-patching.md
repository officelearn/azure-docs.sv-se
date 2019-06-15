---
title: Automatisk uppdatering för SQL Server-datorer (Resource Manager) | Microsoft Docs
description: Beskriver funktionen för automatisk uppdatering för SQL Server-datorer som körs i Azure med hjälp av Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4f0d681c93ab7ac7fef941892a95282a2fd59b89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075749"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisk uppdatering av SQL Server i Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatisk uppdatering upprättar en underhållsperiod för en Azure virtuell dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL Server säkerställer den här begränsningen att systemuppdateringar och eventuella tillhörande omstarter sker på bästa möjliga tid för databasen. 

> [!IMPORTANT]
> Endast Windows-uppdateringar som är markerade som **Viktiga** installeras. Andra SQL Server-uppdateringar, till exempel kumulativa uppdateringar, måste installeras manuellt. 

Automatisk uppdatering är beroende av [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
För att använda automatisk uppdatering, Överväg följande krav:

**Operativsystemet**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-version**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview) om du planerar att konfigurera automatisk uppdatering med PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatisk uppdatering är beroende av SQL Server IaaS Agent-tillägget. Aktuell SQL VM-galleriavbildningar lägga till det här tillägget som standard. Mer information finns i [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk uppdatering. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandon.

| Inställning | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatisk uppdatering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar automatisk uppdatering för en Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schema för att hämta och installera uppdateringar för Windows, SQL Server och Microsoft för den virtuella datorn. |
| **Starttid för underhåll** |0-24 |Lokalt starttid att uppdatera den virtuella datorn. |
| **Underhållsfönster** |30-180 |Hur många minuter som har behörighet att slutföra hämtningen och installationen av uppdateringar. |
| **Patch-kategori** |Viktigt | Kategorin för Windows-uppdateringar för att hämta och installera.|

## <a name="configuration-in-the-portal"></a>Konfigurationen i portalen
Du kan använda Azure-portalen för att konfigurera automatisk uppdatering under etableringen eller för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Använda Azure-portalen för att konfigurera automatisk uppdatering när du skapar en ny SQL Server-dator i distributionsmodellen för Resource Manager.

I den **SQL Server-inställningar** fliken **ändra konfiguration** under **automatisk korrigering**. I följande Skärmbild av Azure portal visas den **SQL automatisk uppdatering** bladet.

![SQL automatisk uppdatering i Azure-portalen](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontext, finns i avsnittet klar på [etablera en virtuell dator i SQL Server i Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Befintliga SQL Server-datorer, öppna ditt [SQL VM-resurs](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) och välj **uppdatering** under **inställningar**. 

![SQL automatisk uppdatering för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


När du är klar klickar du på den **OK** knappen längst ned på den **konfiguration av SQL Server** bladet för att spara dina ändringar.

Om du aktiverar automatisk uppdatering för första gången, konfigurerar SQL Server IaaS Agent i bakgrunden i Azure. Under denna tid kanske Azure-portalen inte visar att automatisk uppdatering har konfigurerats. Vänta några minuter för att agenten ska installeras, konfigureras. Efter det Azure-portalen visar de nya inställningarna.

## <a name="configuration-with-powershell"></a>Med PowerShell
När du har etablerat din SQL-VM, konfigurera automatisk uppdatering med hjälp av PowerShell.

I följande exempel används PowerShell för att konfigurera automatisk uppdatering på en befintlig SQL Server-dator. Den **New AzVMSqlServerAutoPatchingConfig** kommando konfigurerar en ny underhållsperiod för automatiska uppdateringar.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Om tillägget inte redan är installerat installerar tillägget startar om SQL Server-tjänsten.

Utifrån det här exemplet beskrivs i följande tabell i praktiken på Virtuella Azure-måldatorn:

| Parameter | Verkan |
| --- | --- |
| **dayOfWeek** |Korrigeringar installeras varje torsdag. |
| **MaintenanceWindowStartingHour** |Begin uppdaterar kl. 11:00. |
| **MaintenanceWindowsDuration** |Korrigeringar installeras inom 120 minuter. Baserat på starttiden, måste de utföra klockan 1:00. |
| **PatchCategory** |Den enda möjliga inställningen för den här parametern är **viktigt**. Detta installerar Windows update som markerats viktigt; alla SQL Server-uppdateringar som inte ingår i den här kategorin installeras inte. |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS Agent.

Om du vill inaktivera automatisk uppdatering, kör samma skript utan den **-aktivera** parametern till den **New AzVMSqlServerAutoPatchingConfig**. Om den **-aktivera** parametern signalerar kommandot för att inaktivera funktionen.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga automation-aktiviteter finns i [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

