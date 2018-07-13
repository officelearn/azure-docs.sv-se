---
title: Automatisera hanteringsuppgifter på SQL-datorer (klassisk) | Microsoft Docs
description: Det här avsnittet beskriver hur du hanterar SQL Server agent-tillägget som automatiserar specifika uppgifter för administration av SQL Server. Dessa inkluderar automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering. Det här avsnittet använder det klassiska distributionsläget.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 55a31d7a0ab603dd7fe7de514d11d003e044240a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008339"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatisera hanteringsuppgifter på Azure virtuella datorer med SQL Server Agent-tillägget (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../classic/sql-server-agent-extension.md)
> 
>
 
SQL Server IaaS Agent-tillägget (SQLIaaSAgent) körs på Azure virtuella datorer för att automatisera administrationsuppgifter. Det här avsnittet innehåller en översikt över de tjänster som stöds av tillägget samt anvisningar för installation, status och borttagning.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-versionen av den här artikeln finns [SQL Server-Agenttillägg för SQL Server virtuella datorer Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Tjänster som stöds
SQL Server IaaS Agent-tillägget har stöd för följande administrationsuppgifter:

| Administrationsfunktion | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatiserar schemaläggning av säkerhetskopiering för alla databaser för standardinstansen av SQL Server på den virtuella datorn. Mer information finns i [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines (klassisk)](../classic/sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar en underhållsperiod då viktiga Windows-uppdateringar till den virtuella datorn kan äga rum, så att du kan undvika att uppdateringar under Högbelastningstider för din arbetsbelastning. Mer information finns i [automatisk uppdatering för SQL Server i Azure Virtual Machines (klassisk)](../classic/sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Kan du automatiskt installera och konfigurera Azure Key Vault på din SQL Server-VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines (klassisk)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Förutsättningar
Krav för att använda SQL Server IaaS Agent-tillägget på den virtuella datorn:

### <a name="operating-system"></a>Operativsystem:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server-versioner:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Hämta och konfigurera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

Starta Windows PowerShell och anslut den till Azure-prenumerationen med den **Add-AzureAccount** kommando.

    Add-AzureAccount

Om du har flera prenumerationer använder **Select-AzureSubscription** att välja den prenumeration som innehåller dina mål klassisk virtuell dator.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Nu kan du få en lista med klassiska virtuella datorer och deras associerade tjänstnamn med den **Get-AzureVM** kommando.

    Get-AzureVM

## <a name="installation"></a>Installation
För klassiska virtuella datorer, måste du använda PowerShell för att installera SQL Server IaaS Agent-tillägget och konfigurera dess associerade tjänster. Använd den **Set-AzureVMSqlServerExtension** PowerShell-cmdlet för att installera tillägget. Följande kommando installerar tillägget på en Windows Server VM (klassisk) och ger den namnet ”SQLIaaSExtension”.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Om du uppdaterar till den senaste versionen av SQL IaaS Agent-tillägget, måste du starta om den virtuella datorn när du har uppdaterat tillägget.

> [!NOTE]
> Klassiska virtuella datorer har inte ett alternativ för att installera och konfigurera SQL IaaS Agent-tillägget via portalen.

> [!NOTE]
> SQL Server IaaS Agent-tillägget stöds bara på [galleriavbildningar för SQL Server-VM](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (betala per användning eller bring-your-own-license). Det stöds inte om du manuellt installera SQL Server på en OS-endast Windows Server-dator eller om du distribuerar en anpassad SQL Server VM-VHD. I dessa fall kan det vara möjligt att installera och hantera tillägget manuellt med hjälp av PowerShell, men vi rekommenderar starkt att i stället installera en SQL Server-VM-avbildning för galleriet och sedan anpassa den.

## <a name="status"></a>Status
Ett sätt att kontrollera att tillägget har installerats är att visa agentstatusen i Azure Portal. Välj en virtuell dator visas i bladet för virtuella datorn och klicka sedan på **tillägg**. Du bör se den **SQLIaaSAgent** tillägg i listan.

![SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Du kan också använda den **Get-AzureVMSqlServerExtension** Azure Powershell-cmdleten.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Borttagning
I Azure-portalen kan du avinstallera tillägget genom att klicka på ellipsen på den **tillägg** -bladet i egenskaperna för virtuella datorer. Klicka sedan på **avinstallera**.

![Avinstallera SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Du kan också använda den **Remove-AzureVMSqlServerExtension** Powershell-cmdlet.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
Börja med någon av de tjänster som stöds av tillägget. Mer information finns i avsnitt som refereras till i den [tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

