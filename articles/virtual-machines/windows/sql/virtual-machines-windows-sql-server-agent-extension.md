---
title: "Automatisera hanteringsuppgifter på SQL virtuella datorer (Resource Manager) | Microsoft Docs"
description: "Det här avsnittet beskriver hur du hanterar SQL Server agent-tillägg som automatiserar specifika administrationsuppgifter för SQL Server. Dessa inkluderar automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering. Det här avsnittet använder distributionsläget för Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7152d184bb6d1d4b81aeb47e2c7c9160ada36023
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatisera hanteringsuppgifter på Azure Virtual Machines med SQL Server Agent-tillägget (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../classic/sql-server-agent-extension.md)
> 
> 

SQL Server IaaS Agent tillägget (SQLIaaSExtension) körs på virtuella Azure-datorer att automatisera administrationsuppgifter. Det här avsnittet innehåller en översikt över de tjänster som stöds av tillägget samt anvisningar för installation, status och borttagning.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den klassiska versionen av den här artikeln finns [SQL Server Agent-tillägget för SQL Server VMs klassiska](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Tjänster som stöds
SQL Server IaaS Agent tillägget stöder följande administrationsuppgifter:

| Funktionen för administration | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatiserar schemaläggning av säkerhetskopieringar för alla databaser för standardinstansen av SQL Server på den virtuella datorn. Mer information finns i [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar en underhållsperiod då uppdateringar till den virtuella datorn kan ske, så du kan undvika uppdateringar under Högbelastningstider för din arbetsbelastning. Mer information finns i [automatisk uppdatering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Kan du automatiskt installera och konfigurera Azure Key Vault på SQL Server-VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

När du installerade och körs, tillgängliggör SQL Server IaaS Agent tillägget dessa funktioner för administration på SQL Server-panelen på den virtuella datorn i Azure-portalen och via Azure PowerShell för SQL Server marketplace-bilder och via Azure PowerShell för manuella installationer av tillägget. 

## <a name="prerequisites"></a>Krav
Krav för att använda SQL Server IaaS Agent tillägget på den virtuella datorn:

**Operativsystemet**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-versioner**:

* SQLServer 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Hämta och konfigurera de senaste Azure PowerShell-kommandon](/powershell/azure/overview)

## <a name="installation"></a>Installation
Tillägget SQL Server IaaS-Agent installeras automatiskt när du etablerar en galleriavbildningar för SQL Server-virtuella datorn. Om du behöver installera tillägget manuellt på något av dessa SQL Server-datorer kan du använda följande PowerShell-kommando:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

Det är också möjligt att installera tillägget SQL Server IaaS-Agent på en virtuell dator bara Operativsystemet Windows Server. Detta stöds endast om du har installerat SQL Server manuellt på den datorn. Installera tillägget manuellt med hjälp av samma **Set AzureVMSqlServerExtension** PowerShell-cmdlet.

> [!NOTE]
> Om du manuellt installera tillägget SQL Server IaaS-Agent på en OS-endast Windows Server VM, kan du inte hantera konfigurationsinställningarna för SQL Server via Azure-portalen. I det här scenariot måste du göra alla ändringar med PowerShell.

## <a name="status"></a>Status
Ett sätt att kontrollera att tillägget installeras är att visa agentens status i Azure Portal. Välj **alla inställningar** i bladet för virtuella datorer och sedan klicka på **tillägg**. Du bör se den **SQLIaaSExtension** tillägg i listan.

![SQL Server IaaS-Agent tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Du kan också använda den **Get-AzureVMSqlServerExtension** Azure Powershell-cmdlet.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Föregående kommando bekräftar att agenten är installerad och ger allmän statusinformation. Du kan också hämta statusinformation om automatisk säkerhetskopiering och korrigering med följande kommandon.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Borttagning
I Azure-portalen kan du avinstallera tillägget genom att klicka på knappen på den **tillägg** bladet för de virtuella datorn. Klicka på **ta bort**.

![Avinstallera SQL Server IaaS-Agent tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Du kan också använda den **ta bort AzureRmVMSqlServerExtension** Powershell-cmdlet.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Nästa steg
Börja med en av de tjänster som stöds av tillägget. Mer information finns i avsnitt som refereras till i den [-tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

