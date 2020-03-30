---
title: Automatisera hanteringsuppgifter på virtuella SQL-datorer (Klassisk) | Microsoft-dokument
description: I det här avsnittet beskrivs hur du hanterar tillägget SQL Server-agent, vilket automatiserar specifika SQL Server-administrationsuppgifter. Dessa inkluderar automatisk säkerhetskopiering, automatisk korrigering och Azure Key Vault-integrering. I det här avsnittet används det klassiska distributionsläget.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b76ade40db1e85abc0fb42af2e6f4ab88cb092c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982288"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatisera hanteringsuppgifter på virtuella Azure-datorer med SQL Server Agent Extension (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../classic/sql-server-agent-extension.md)
> 
>
 
SQL Server IaaS Agent Extension (SQLIaaSAgent) körs på virtuella Azure-datorer för att automatisera administrationsuppgifter. Det här avsnittet innehåller en översikt över de tjänster som stöds av tillägget samt instruktioner för installation, status och borttagning.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Mer om hur du visar Resource Manager-versionen av den här artikeln finns i [SQL Server Agent Extension för SQL Server VMs Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Tjänster som stöds
Sql Server IaaS-agenttillägget stöder följande administrationsuppgifter:

| Funktionen Administration | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatiserar schemaläggningen av säkerhetskopior för alla databaser för standardinstansen av SQL Server i den virtuella datorn. Mer information finns i [Automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines (Classic)](../classic/sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar ett underhållsfönster under vilket viktiga Windows-uppdateringar till den virtuella datorn kan äga rum, så att du kan undvika uppdateringar under rusningstid för din arbetsbelastning. Mer information finns i [Automatisk korrigering för SQL Server i Azure Virtual Machines (Classic)](../classic/sql-automated-patching.md). |
| **Integrering av Azure Key Vault** |Gör att du automatiskt kan installera och konfigurera Azure Key Vault på din VIRTUELLA SQL Server.Enables you to automatically install and configure Azure Key Vault on your SQL Server VM. Mer information finns i [Konfigurera Azure Key Vault-integrering för SQL Server på Virtuella Azure-datorer (Klassisk).](../classic/ps-sql-keyvault.md) |

## <a name="prerequisites"></a>Krav
Krav för att använda SQL Server IaaS-agenttillägget på den virtuella datorn:

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

Starta Windows PowerShell och anslut den till din Azure-prenumeration med kommandot **Add-AzureAccount.**

    Add-AzureAccount

Om du har flera prenumerationer använder du **Select-AzureSubscription** för att välja den prenumeration som innehåller din klassiska virtuella målgrupper.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Nu kan du få en lista över de klassiska virtuella datorerna och deras associerade tjänstnamn med kommandot **Get-AzureVM.**

    Get-AzureVM

## <a name="installation"></a>Installation
För klassiska virtuella datorer måste du använda PowerShell för att installera SQL Server IaaS Agent Extension och konfigurera dess associerade tjänster. Använd **cmdleten Set-AzureVMSqlServerExtension** PowerShell för att installera tillägget. Följande kommando installerar till exempel tillägget på en Windows Server VM (klassisk) och namnger det "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Om du uppdaterar till den senaste versionen av SQL IaaS Agent Extension måste du starta om den virtuella datorn när du har uppdaterat tillägget.

> [!NOTE]
> Klassiska virtuella datorer har inte möjlighet att installera och konfigurera SQL IaaS-agenttillägget via portalen.

> [!NOTE]
> Sql Server IaaS Agent Extension stöds endast på [SQL Server VM-galleriavbildningar](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (betala per användning eller bring-your-own-licens). Det stöds inte om du installerar SQL Server manuellt på en virtuell dator med enbart OPERATIVSYSTEMET Windows Server eller om du distribuerar en anpassad virtuell virtuell virtuell SQL Server-hårddisk. I dessa fall kan det vara möjligt att installera och hantera tillägget manuellt med hjälp av PowerShell, men det rekommenderas starkt att i stället installera en SQL Server VM-galleriavbildning och sedan anpassa den.

## <a name="status"></a>Status
Ett sätt att kontrollera att tillägget är installerat är att visa agentstatusen i Azure Portal. Välj en virtuell dator som visas i bladet för den virtuella datorn och klicka sedan på **Tillägg**. Du bör se **SQLIaaSAgent-tillägget** i listan.

![SQL Server IaaS-agenttillägg i Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Du kan också använda **Cmdleten Get-AzureVMSqlServerExtension** Azure Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Borttagning
I Azure Portal kan du avinstallera tillägget genom att klicka på ellipsen på **tilläggsbladet** för egenskaperna för den virtuella datorn. Klicka sedan på **Avinstallera**.

![Avinstallera SQL Server IaaS-agenttillägget i Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Du kan också använda **cmdleten Remove-AzureVMSqlServerExtension** Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Efterföljande moment
Börja använda någon av de tjänster som stöds av tillägget. Mer information finns i avsnitten som refereras i avsnittet [Tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

