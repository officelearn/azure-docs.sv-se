---
title: "Automatisera hanteringsuppgifter på SQL virtuella datorer (klassisk) | Microsoft Docs"
description: "Det här avsnittet beskriver hur du hanterar SQL Server agent-tillägg som automatiserar specifika administrationsuppgifter för SQL Server. Dessa inkluderar automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering. Det här avsnittet använder läget för klassisk distribution."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30fa9128cd51a7498449c991b58500ad9acdd3d4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatisera hanteringsuppgifter på Azure Virtual Machines med SQL Server Agent-tillägget (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../classic/sql-server-agent-extension.md)
> 
>
 
SQL Server IaaS Agent tillägget (SQLIaaSAgent) körs på virtuella Azure-datorer att automatisera administrationsuppgifter. Det här avsnittet innehåller en översikt över de tjänster som stöds av tillägget samt anvisningar för installation, status och borttagning.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-versionen av den här artikeln finns [SQL Server Agent-tillägg för SQL Server VMs Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Tjänster som stöds
SQL Server IaaS Agent tillägget stöder följande administrationsuppgifter:

| Funktionen för administration | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatiserar schemaläggning av säkerhetskopieringar för alla databaser för standardinstansen av SQL Server på den virtuella datorn. Mer information finns i [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines (klassisk)](../classic/sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar en underhållsperiod då uppdateringar till den virtuella datorn kan ske, så du kan undvika uppdateringar under Högbelastningstider för din arbetsbelastning. Mer information finns i [automatisk uppdatering för SQL Server i Azure Virtual Machines (klassisk)](../classic/sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Kan du automatiskt installera och konfigurera Azure Key Vault på SQL Server-VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines (klassisk)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Krav
Krav för att använda SQL Server IaaS Agent tillägget på den virtuella datorn:

### <a name="operating-system"></a>Operativsystem:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server-versioner:
* SQLServer 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Hämta och konfigurera de senaste Azure PowerShell-kommandon](/powershell/azure/overview).

Starta Windows PowerShell och ansluta till din Azure-prenumeration med den **Add-AzureAccount** kommando.

    Add-AzureAccount

Om du har flera prenumerationer använder **Välj AzureSubscription** att välja den prenumeration som innehåller mål-klassiska VM.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Nu kan du hämta en lista över de klassiska virtuella datorerna och deras associerade tjänstnamn med den **Get-AzureVM** kommando.

    Get-AzureVM

## <a name="installation"></a>Installation
För klassiska virtuella datorer, måste du använda PowerShell för att installera tillägget SQL Server IaaS-agenten och konfigurera tillhörande tjänster. Använd den **Set AzureVMSqlServerExtension** PowerShell-cmdlet för att installera tillägget. Följande kommando tillägget installeras på en Windows Server-VM (klassisk) och namnges ”SQLIaaSExtension”.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Om du uppdaterar till den senaste versionen av tillägget SQL IaaS-agenten måste du starta om den virtuella datorn när du har uppdaterat tillägget.

> [!NOTE]
> Klassiska virtuella datorer har inte ett alternativ för att installera och konfigurera SQL IaaS Agent tillägget via portalen.
> 
> 

## <a name="status"></a>Status
Ett sätt att kontrollera att tillägget installeras är att visa agentens status i Azure Portal. Välj en virtuell dator som anges i bladet för virtuella datorer och klicka sedan på **tillägg**. Du bör se den **SQLIaaSAgent** tillägg i listan.

![SQL Server IaaS-Agent tillägget i Azure-portalen](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Du kan också använda den **Get-AzureVMSqlServerExtension** Azure Powershell-cmdlet.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Borttagning
I Azure-portalen kan du avinstallera tillägget genom att klicka på knappen på den **tillägg** bladet för de virtuella datorn. Klicka på **avinstallera**.

![Avinstallera SQL Server IaaS-Agent tillägget i Azure-portalen](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Du kan också använda den **ta bort AzureVMSqlServerExtension** Powershell-cmdlet.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
Börja med en av de tjänster som stöds av tillägget. Mer information finns i avsnitt som refereras till i den [-tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

