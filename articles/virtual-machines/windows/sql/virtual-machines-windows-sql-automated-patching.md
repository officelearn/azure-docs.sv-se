---
title: Automatisk korrigering för virtuella SQL Server-datorer (Resource Manager) | Microsoft-dokument
description: I artikeln beskrivs funktionen Automatisk korrigering för virtuella SQL Server-datorer som körs i Azure med Hjälp av Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 292f3e8819f6f9f4b2989423814e02dfcfb4bfdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127677"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisk uppdatering av SQL Server i Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatisk uppdatering upprättar en underhållsperiod för en virtuell Azure-dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL Server säkerställer den här begränsningen att systemuppdateringar och eventuella tillhörande omstarter sker på bästa möjliga tid för databasen. 

> [!IMPORTANT]
> Endast Windows- och SQL Server-uppdateringar som **markerats** som viktiga eller **kritiska** är installerade. Andra SQL Server-uppdateringar, till exempel Service Pack och kumulativa uppdateringar som inte är markerade som **viktiga** eller **kritiska** måste installeras manuellt. 

Automatisk uppdatering är beroende av [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Krav
Om du vill använda automatisk korrigering bör du tänka på följande förutsättningar:

**Operativsystem:**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-version:**

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview) om du planerar att konfigurera automatisk korrigering med PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatisk korrigering är beroende av SQL Server IaaS Agent Extension. Aktuella SQL-avbildningar för virtuella datorer lägger till det här tillägget som standard. Mer information finns i [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk korrigering. De faktiska konfigurationsstegen varierar beroende på om du använder Azure-portalen eller Azure Windows PowerShell-kommandona.

| Inställning | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatisk korrigering** |Aktivera/inaktivera (inaktiverad) |Aktiverar eller inaktiverar automatisk korrigering för en virtuell Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schemat för hämtning och installation av Windows-, SQL Server- och Microsoft-uppdateringar för den virtuella datorn. |
| **Underhållsstarttimme** |0-24 |Den lokala starttiden för att uppdatera den virtuella datorn. |
| **Varaktighet för underhållsfönstret** |30-180 |Antalet minuter som tillåts för att slutföra hämtning och installation av uppdateringar. |
| **Korrigeringsfil** |Viktigt | Kategorin Windows-uppdateringar som ska hämtas och installeras.|

## <a name="configuration-in-the-portal"></a>Konfiguration i portalen
Du kan använda Azure-portalen för att konfigurera automatisk korrigering under etablering eller för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Använd Azure-portalen för att konfigurera automatisk korrigering när du skapar en ny virtuell SQL Server-dator i resurshanterarens distributionsmodell.

På fliken **INSTÄLLNINGAR för SQL Server** väljer du Ändra **konfiguration** under **Automatisk korrigering**. Följande Azure portal skärmdump visar **SQL Automated Patching** blade.

![SQL Automated Patching i Azure Portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

För kontext, se hela avsnittet om [etablering av en virtuell SQL Server-dator i Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga virtuella SQL Server-datorer öppnar du [resursen virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) och väljer **Korrigering** under **Inställningar**. 

![AUTOMATISK SQL-korrigering för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


När du är klar klickar du på **KNAPPEN OK** längst ned i **SQL Server-konfigurationsbladet** för att spara ändringarna.

Om du aktiverar automatisk korrigering för första gången konfigurerar Azure SQL Server IaaS-agenten i bakgrunden. Under den här tiden kanske Azure-portalen inte visar att automatisk korrigering är konfigurerad. Vänta flera minuter tills agenten har installerats, konfigurerat. Efter att Azure-portalen återspeglar de nya inställningarna.

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell
När du har etablerat din VIRTUELLA SQL använder du PowerShell för att konfigurera automatisk korrigering.

I följande exempel används PowerShell för att konfigurera automatisk korrigering på en befintlig VIRTUELL SQL Server.In the following example, PowerShell is used to configure Automated Patching on an existing SQL Server VM. Kommandot **New-AzVMSqlServerAutoPatchingConfig** konfigurerar ett nytt underhållsfönster för automatiska uppdateringar.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Om tillägget inte redan är installerat startas SQL Server-tjänsten om du installerar tillägget.

Baserat på det här exemplet beskriver följande tabell den praktiska effekten på målet Azure VM:

| Parameter | Verkan |
| --- | --- |
| **Dagiveck** |Patchar installeras varje torsdag. |
| **UnderhållWindowStartingHour** |Börja uppdateringarna klockan 11:00. |
| **UnderhållFönsterUnderdykt** |Patchar måste installeras inom 120 minuter. Baserat på starttiden måste de vara klara senast 13:00. |
| **PatchCategory** |Den enda möjliga inställningen för den här parametern är **Viktigt**. Den här installationen av Windows-uppdateringen markerad Viktig; Den installerar inga SQL Server-uppdateringar som inte ingår i den här kategorin. |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

Om du vill inaktivera automatisk korrigering kör du samma skript utan **parametern -Enable** till **parametern New-AzVMSqlServerAutoPatchingConfig**. Frånvaron av parametern **-Enable** signalerar kommandot för att inaktivera funktionen.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga automatiseringsuppgifter finns i [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).

