---
title: Automatisera hanteringsuppgifter på SQL-datorer (Resource Manager) | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar SQL Server agent-tillägget som automatiserar specifika uppgifter för administration av SQL Server. Dessa inkluderar automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 71878d5d033f0005d2c8c36d9f59799e125a19dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762709"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatisera hanteringsuppgifter på Azure virtuella datorer med SQL Server Agent-tillägget (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent-tillägget (SqlIaasExtension) körs på virtuella Azure-datorer för att automatisera administrationsuppgifter. Den här artikeln innehåller en översikt över de tjänster som stöds av tillägget samt anvisningar för installation, status och borttagning.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den klassiska versionen av den här artikeln finns [SQL Server-Agenttillägg för SQL Server-datorer klassiska](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Tjänster som stöds
SQL Server IaaS Agent-tillägget har stöd för följande administrationsuppgifter:

| Administrationsfunktion | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatiserar schemaläggning av säkerhetskopiering för alla databaser för antingen standardinstansen eller en [korrekt installerade](virtual-machines-windows-sql-server-iaas-faq.md#administration) namngiven instans av SQL Server på den virtuella datorn. Mer information finns i [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar en underhållsperiod då viktiga Windows-uppdateringar till den virtuella datorn kan äga rum, så att du kan undvika att uppdateringar under Högbelastningstider för din arbetsbelastning. Mer information finns i [automatisk uppdatering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Kan du automatiskt installera och konfigurera Azure Key Vault på din SQL Server-VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

När installerad och igång, tillgängliggör SQL Server IaaS Agent-tillägget dessa funktioner för administration på den SQL Server-panelen för den virtuella datorn i Azure-portalen och via Azure PowerShell för SQL Server marketplace-avbildningar och via Azure PowerShell för manuella installationer av tillägget. 

## <a name="prerequisites"></a>Nödvändiga komponenter
Krav för att använda SQL Server IaaS Agent-tillägget på den virtuella datorn:

**Operativsystemet**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-versioner**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Hämta och konfigurera de senaste Azure PowerShell-kommandona](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> För närvarande den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md) stöds inte för FCI för SQL Server på Azure. Vi rekommenderar att du avinstallerar tillägget från virtuella datorer som deltar i ett FCI. Funktioner som stöds av tillägget är inte tillgängliga för SQL-datorer när agenten har avinstallerats.

## <a name="installation"></a>Installation
SQL Server IaaS Agent-tillägget installeras automatiskt när du etablerar en galleriavbildningar för SQL Server-dator. SQL IaaS-tillägget erbjuder hanterbarhet för en enskild instans på SQL Server-dator. Om det finns en standardinstans, sedan tillägget kommer att fungera med instansen och stöder inte hantering av andra instanser. Om det finns inga standardinstansen men bara en namngiven instans, ska som hanteras den namngivna instansen. Om det finns ingen standardinstans och det finns flera namngivna instanser, kan sedan tillägget inte installeras. 



Om du vill installera om tillägget manuellt på en av de här SQL Server-datorer kan du använda följande PowerShell-kommando:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Om tillägget inte redan är installerat installerar tillägget startar om SQL Server-tjänsten. Uppdaterar SQL IaaS-tillägget dock inte om SQL Server-tjänsten. 

> [!NOTE]
> Det är möjligt att installera SQL Server IaaS Agent-tillägget för anpassade SQL Server-avbildningar, funktionen är för närvarande begränsad till [ändra licenstypen](virtual-machines-windows-sql-ahb.md). Andra funktioner som tillhandahålls av SQL IaaS-tillägget fungerar bara på [galleriavbildningar för SQL Server-VM](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (betala per användning eller bring-your-own-license).

### <a name="use-a-single-named-instance"></a>Använda en enda namngiven instans
SQL IaaS-tillägget fungerar med en namngiven instans på en SQL Server-avbildning om standardinstansen avinstalleras korrekt, och om IaaS-tillägget har installerats om.

Om du vill använda en namngiven instans av SQL Server gör du följande:
   1. Distribuera en SQL Server-VM från marketplace. 
   1. Avinstallera tillägget IaaS inifrån den [Azure-portalen](https://portal.azure.com).
   1. Avinstallera SQL Server helt inom SQL Server-dator.
   1. Installera SQL Server med en namngiven instans i SQL Server-dator. 
   1. Installera IaaS-tillägget i Azure-portalen.  

## <a name="status"></a>Status
Ett sätt att kontrollera att tillägget har installerats är att visa agentens status på Azure-portalen. Välj **alla inställningar** i den virtuella datorn och sedan klicka på **tillägg**. Du bör se den **SqlIaasExtension** tillägg i listan.

![SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Du kan också använda den **Get-AzVMSqlServerExtension** Azure PowerShell-cmdleten.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Föregående kommando bekräftar agenten är installerad och ger allmän statusinformation. Du kan också hämta statusinformation om automatisk säkerhetskopiering och uppdatering med följande kommandon.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Borttagning
I Azure-portalen kan du avinstallera tillägget genom att klicka på ellipsen på den **tillägg** fönster för dina VM-egenskaper. Klicka på **Ta bort**.

![Avinstallera SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Du kan också använda den **Remove-AzVMSqlServerExtension** PowerShell-cmdlet.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Nästa steg
Börja med någon av de tjänster som stöds av tillägget. Mer information finns i artiklar som refereras till i den [tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

