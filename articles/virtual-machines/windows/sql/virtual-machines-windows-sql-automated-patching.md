---
title: Automatiserad uppdatering för SQL Server virtuella datorer (Resource Manager) | Microsoft Docs
description: Förklarar automatisk uppdaterings funktion för SQL Server Virtual Machines som körs i Azure med Resource Manager.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127677"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisk uppdatering av SQL Server i Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatisk uppdatering upprättar en underhållsperiod för en virtuell Azure-dator som kör SQL Server. Automatiska uppdateringar kan endast installeras under den här underhållsperioden. För SQL Server säkerställer den här begränsningen att systemuppdateringar och eventuella tillhörande omstarter sker på bästa möjliga tid för databasen. 

> [!IMPORTANT]
> Endast Windows-och SQL Server-uppdateringar som marker ATS som **viktiga** eller **kritiska** är installerade. Andra SQL Server uppdateringar, till exempel Service Pack och ackumulerade uppdateringar som inte är markerade som **viktiga** eller **kritiska** , måste installeras manuellt. 

Automatisk uppdatering är beroende av [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Krav
Om du vill använda automatisk uppdatering bör du tänka på följande:

**Operativ system**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server version**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview) om du planerar att konfigurera automatisk uppdatering med PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatiserad uppdatering förlitar sig på SQL Server IaaS agent-tillägg. Aktuella Galleri avbildningar för virtuella SQL-datorer Lägg till tillägget som standard. Mer information finns i [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Inställningar
I följande tabell beskrivs de alternativ som kan konfigureras för automatisk uppdatering. De faktiska konfigurations stegen varierar beroende på om du använder Azure Portal-eller Azure Windows PowerShell-kommandon.

| Inställningen | Möjliga värden | Beskrivning |
| --- | --- | --- |
| **Automatiserad uppdatering** |Aktivera/inaktivera (inaktive rad) |Aktiverar eller inaktiverar automatisk uppdatering för en virtuell Azure-dator. |
| **Underhållsschema** |Varje dag, måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag |Schemat för att ladda ned och installera Windows-, SQL Server-och Microsoft-uppdateringar för den virtuella datorn. |
| **Start timme för underhåll** |0-24 |Den lokala Start tiden för att uppdatera den virtuella datorn. |
| **Underhålls periodens varaktighet** |30-180 |Antalet minuter som tillåts att slutföra nedladdningen och installationen av uppdateringar. |
| **Uppdaterings kategori** |Viktigt | Den kategori av Windows-uppdateringar som ska laddas ned och installeras.|

## <a name="configuration-in-the-portal"></a>Konfiguration i portalen
Du kan använda Azure Portal för att konfigurera automatisk uppdatering under etableringen eller för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Använd Azure Portal för att konfigurera automatisk uppdatering när du skapar en ny SQL Server virtuell dator i distributions modellen för Resource Manager.

På fliken **SQL Server inställningar** väljer du **ändra konfiguration** under **automatiserad uppdatering**. Följande Azure Portal skärm bild visar bladet **SQL-automatiserad uppdatering** .

![Automatisk SQL-uppdatering i Azure Portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

För kontexten, se avsnittet komplett om hur du [konfigurerar en SQL Server virtuell dator i Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga SQL Server virtuella datorer öppnar du [resursen SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) och väljer **korrigering** under **Inställningar**. 

![Automatisk uppdatering av SQL för befintliga virtuella datorer](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


När du är färdig klickar du på knappen **OK** längst ned på bladet **SQL Server konfiguration** för att spara ändringarna.

Om du aktiverar automatisk uppdatering för första gången konfigurerar Azure SQL Server IaaS-agenten i bakgrunden. Under den här tiden kanske Azure Portal inte visar att automatisk uppdatering har kon figurer ATS. Vänta några minuter på att agenten ska installeras, konfigureras. När Azure Portal visar de nya inställningarna.

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell
När du har slutfört etableringen av din virtuella SQL-dator använder du PowerShell för att konfigurera automatisk uppdatering.

I följande exempel används PowerShell för att konfigurera automatiserad uppdatering på en befintlig SQL Server VM. Kommandot **New-AzVMSqlServerAutoPatchingConfig** konfigurerar en ny underhålls period för automatiska uppdateringar.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s set-AzVMSqlServerExtension-AutoPatchingSettings $aps-VMName $vmname-ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Om tillägget inte redan är installerat startas SQL Server-tjänsten om du installerar tillägget.

I följande tabell beskrivs den praktiska påverkan på den virtuella Azure-datorn:

| Parameter | Verkan |
| --- | --- |
| **DayOfWeek** |Uppdateringar installeras varje torsdag. |
| **MaintenanceWindowStartingHour** |Starta uppdateringar vid 11:10:00. |
| **MaintenanceWindowsDuration** |Korrigeringsfiler måste installeras inom 120 minuter. Baserat på Start tiden måste de slutföras med 1:12:00. |
| **PatchCategory** |Den enda möjliga inställningen för den här parametern är **viktig**. Detta installerar Windows Update markerat som viktigt. inga SQL Server uppdateringar installeras som inte ingår i den här kategorin. |

Det kan ta flera minuter att installera och konfigurera SQL Server IaaS-agenten.

Om du vill inaktivera automatisk uppdatering kör du samma skript utan parametern **-Enable** till **New-AzVMSqlServerAutoPatchingConfig**. Avsaknad av parametern **-Enable** signalerar kommandot för att inaktivera funktionen.

## <a name="next-steps"></a>Nästa steg
Information om andra tillgängliga Automation-uppgifter finns [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns [SQL Server på azure Virtual Machines-översikt](virtual-machines-windows-sql-server-iaas-overview.md).

