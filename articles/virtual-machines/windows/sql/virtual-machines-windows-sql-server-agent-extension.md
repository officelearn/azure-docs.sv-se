---
title: Automatisera hanteringsuppgifter på SQL-datorer (Resource Manager) | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar SQL Server agent-tillägget som automatiserar specifika uppgifter för administration av SQL Server. Dessa inkluderar automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
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
ms.author: jroth
ms.openlocfilehash: 3d2873d83274e4f9bfd8aee7668162237083f452
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303062"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatisera hanteringsuppgifter på Azure virtuella datorer med SQL Server Agent-tillägget (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent-tillägget (SqlIaasExtension) körs på Azure virtuella datorer för att automatisera administrationsuppgifter. Den här artikeln innehåller en översikt över de tjänster som stöds av tillägget samt anvisningar för installation, status och borttagning.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den klassiska versionen av den här artikeln finns [SQL Server-Agenttillägg för SQL Server-datorer klassiska](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Tjänster som stöds
SQL Server IaaS Agent-tillägget har stöd för följande administrationsuppgifter:

| Administrationsfunktion | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatiserar schemaläggning av säkerhetskopiering för alla databaser för standardinstansen av SQL Server på den virtuella datorn. Mer information finns i [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar en underhållsperiod då viktiga Windows-uppdateringar till den virtuella datorn kan äga rum, så att du kan undvika att uppdateringar under Högbelastningstider för din arbetsbelastning. Mer information finns i [automatisk uppdatering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Kan du automatiskt installera och konfigurera Azure Key Vault på din SQL Server-VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

När installerad och igång, tillgängliggör SQL Server IaaS Agent-tillägget dessa funktioner för administration på den SQL Server-panelen för den virtuella datorn i Azure-portalen och via Azure PowerShell för SQL Server marketplace-avbildningar och via Azure PowerShell för manuella installationer av tillägget. 

## <a name="prerequisites"></a>Förutsättningar
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

> [!IMPORTANT]
> För närvarande den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md) stöds inte för FCI för SQL Server på Azure. Vi rekommenderar att du avinstallerar tillägget från virtuella datorer som deltar i ett FCI. Funktioner som stöds av tillägget är inte tillgängliga för SQL-datorer när agenten har avinstallerats.

## <a name="installation"></a>Installation
SQL Server IaaS Agent-tillägget installeras automatiskt när du etablerar en galleriavbildningar för SQL Server-dator. Om du vill installera om tillägget manuellt på en av de här SQL Server-datorer kan du använda följande PowerShell-kommando:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!IMPORTANT]
> Om tillägget inte redan är installerat installerar tillägget startar om SQL Server-tjänsten. Uppdaterar SQL IaaS-tillägget dock inte om SQL Server-tjänsten. 

> [!NOTE]
> SQL Server IaaS Agent-tillägget stöds bara på [galleriavbildningar för SQL Server-VM](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (betala per användning eller bring-your-own-license). Det stöds inte om du manuellt installera SQL Server på en OS-endast Windows Server-dator eller om du distribuerar en anpassad SQL Server VM-VHD. I dessa fall kan det vara möjligt att installera och hantera tillägget manuellt med hjälp av PowerShell, men du får inte konfigurationsinställningarna för SQL Server i Azure-portalen. Vi rekommenderar dock starkt att i stället installera en SQL Server-VM-avbildning för galleriet och sedan anpassa den.

## <a name="status"></a>Status
Ett sätt att kontrollera att tillägget har installerats är att visa agentens status på Azure-portalen. Välj **alla inställningar** i den virtuella datorn och sedan klicka på **tillägg**. Du bör se den **SqlIaasExtension** tillägg i listan.

![SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Du kan också använda den **Get-AzureRmVMSqlServerExtension** Azure PowerShell-cmdleten.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Föregående kommando bekräftar agenten är installerad och ger allmän statusinformation. Du kan också hämta statusinformation om automatisk säkerhetskopiering och uppdatering med följande kommandon.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Borttagning
I Azure-portalen kan du avinstallera tillägget genom att klicka på ellipsen på den **tillägg** fönster för dina VM-egenskaper. Klicka på **Ta bort**.

![Avinstallera SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Du kan också använda den **Remove-AzureRmVMSqlServerExtension** PowerShell-cmdlet.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Nästa steg
Börja med någon av de tjänster som stöds av tillägget. Mer information finns i artiklar som refereras till i den [tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

