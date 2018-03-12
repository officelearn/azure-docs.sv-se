---
title: "Automatisk uppdatering för SQL Server-datorer (Resource Manager) | Microsoft Docs"
description: "Beskriver funktionen för automatisk uppdatering för SQL Server virtuella datorer som körs i Azure med hjälp av hanteraren för filserverresurser."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 398e682db6c42bd7f4864113ddf10a6a75e2b65b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisk uppdatering av SQL Server i Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatisk korrigering upprättar en underhållsperiod för en Azure-dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL-servern säkerställer den här rescriction att uppdateringar och eventuella tillhörande omstarter inträffar på den bästa möjliga tid för databasen. 

> [!IMPORTANT]
> Endast Windows-uppdateringar som är markerade **viktigt** är installerade. Andra SQL Server-uppdateringar, till exempel kumulativa uppdateringar måste installeras manuellt. 

Automatisk korrigering beror på den [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

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

* [Installera de senaste Azure PowerShell-kommandon](/powershell/azure/overview) om du vill konfigurera automatisk uppdatering med PowerShell.

> [!NOTE]
> Automatisk korrigering är beroende av SQL Server IaaS Agent tillägget. Aktuell SQL virtuella galleriavbildningar lägga till tillägget som standard. Mer information finns i [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk uppdatering. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandon.

| Inställning | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatisk uppdatering** |Aktivera/inaktivera (inaktiverat) |Aktiverar eller inaktiverar automatisk uppdatering för en virtuell Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schema för att hämta och installera Windows, SQL Server och Microsoft-uppdateringar för den virtuella datorn. |
| **Starttimme för underhåll** |0-24 |Lokalt starttid för att uppdatera den virtuella datorn. |
| **Underhåll fönstervaraktigheten** |30-180 |Antal minuter som tillåts att slutföra hämtningen och installationen av uppdateringar. |
| **Patch-kategori** |Viktigt | Kategori för att hämta och installera Windows-uppdateringar.|

## <a name="configuration-in-the-portal"></a>Konfigurationen för portalen
Du kan använda Azure-portalen för att konfigurera automatisk uppdatering under etableringen eller för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Använda Azure portal för att konfigurera automatisk uppdatering när du skapar en ny SQL Server-dator i Resource Manager-distributionsmodellen.

I den **SQL Server-inställningar** bladet väljer **automatisk uppdatering**. I följande Skärmbild av Azure portal visas den **SQL automatisk uppdatering** bladet.

![SQL automatisk uppdatering i Azure-portalen](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontexten, finns i avsnittet fullständig på [etablera en virtuell dator i SQL Server i Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer
Välj den virtuella datorn för SQL Server för befintliga SQL Server virtuella datorer. Välj sedan den **SQL Server-konfigurationsfilen** avsnitt i den **inställningar** bladet.

![SQL automatisk uppdatering av befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

I den **SQL Server-konfigurationsfilen** bladet klickar du på den **redigera** knapp i automatisk uppdatering av avsnittet.

![Konfigurera SQL automatisk uppdatering för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

När du är klar klickar du på den **OK** knappen längst ned på den **SQL Server-konfigurationsfilen** bladet för att spara dina ändringar.

Om du aktiverar automatisk uppdatering för första gången, konfigurerar Azure SQL Server IaaS-Agent i bakgrunden. Under denna tid kanske Azure-portalen inte visar att automatisk uppdatering har konfigurerats. Vänta några minuter för att agenten ska installeras, konfigureras. Efter det att Azure-portalen visar de nya inställningarna.

> [!NOTE]
> Du kan också konfigurera automatisk uppdatering med hjälp av en mall. Mer information finns i [Azure quickstart-mall för automatisk uppdatering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>Med PowerShell
När du har etablerat din SQL-VM, använder du PowerShell för att konfigurera automatisk uppdatering.

I följande exempel används PowerShell för att konfigurera automatisk uppdatering på en befintlig SQL Server-VM. Den **AzureRM.Compute\New AzureRmVMSqlServerAutoPatchingConfig** kommando konfigurerar en ny underhållsperiod för automatiska uppdateringar.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Om filnamnstillägget inte har installerats, installerar tillägget startar om SQL Server-tjänsten.

Baserat på det här exemplet beskrivs i följande tabell i praktiken på målet virtuella Azure-datorn:

| Parameter | Verkan |
| --- | --- |
| **DayOfWeek** |Korrigeringsprogram installerade varje torsdag. |
| **MaintenanceWindowStartingHour** |Begin uppdateringar på 11:00:00. |
| **MaintenanceWindowsDuration** |Korrigeringsfiler måste installeras inom 120 minuter. Baserat på starttid, måste de utföra av 1:00 pm. |
| **PatchCategory** |Den enda möjliga inställningen för den här parametern är **viktigt**. Detta installerar Windows update markeras viktiga; alla SQL Server-uppdateringar som inte ingår i den här kategorin installeras inte. |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-Agent.

Om du vill inaktivera automatisk uppdatering, kör du skriptet samma utan den **-aktivera** parametern till den **AzureRM.Compute\New AzureRmVMSqlServerAutoPatchingConfig**. Avsaknad av den **-aktivera** parametern signalerar kommandot för att inaktivera funktionen.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga automation-aktiviteter finns [tillägg för SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

