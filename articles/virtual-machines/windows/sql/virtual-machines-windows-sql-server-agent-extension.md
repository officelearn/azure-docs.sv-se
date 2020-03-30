---
title: Automatisera hanteringsuppgifter med IaaS Agent-tillägget
description: I den hÃ¤r artikeln beskrivs hur du hanterar SQL Server IaaS Agent Extension, som automatiserar specifika SQL Server administrationsuppgifter. Dessa inkluderar automatisk säkerhetskopiering, automatisk korrigering och Azure Key Vault-integrering.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030786"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatisera hanteringsuppgifter på virtuella Azure-datorer med hjälp av TILLÄGGET SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent-tillägget (SqlIaasExtension) körs på virtuella Azure-datorer för att automatisera administrationsuppgifter. Den här artikeln innehåller en översikt över de tjänster som tillägget stöder. Den här artikeln innehåller också instruktioner för installation, status och borttagning av tillägget.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den klassiska versionen av den här artikeln finns i [SQL Server IaaS Agent Extension för virtuella SQL Server-datorer (klassiskt).](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)


## <a name="supported-services"></a>Tjänster som stöds
Sql Server IaaS-agenttillägget stöder följande administrationsuppgifter:

| Funktionen Administration | Beskrivning |
| --- | --- |
| **Automatisk säkerhetskopiering av SQL Server** |Automatiserar schemaläggningen av säkerhetskopior för alla databaser för antingen standardinstansen eller en [korrekt installerad](virtual-machines-windows-sql-server-iaas-faq.md#administration) instans av SQL Server på den virtuella datorn. Mer information finns i [Automatisk säkerhetskopiering för SQL Server i virtuella Azure-datorer (Resource Manager).](virtual-machines-windows-sql-automated-backup.md) |
| **Automatisk korrigering av SQL Server** |Konfigurerar ett underhållsfönster under vilket viktiga Windows-uppdateringar till den virtuella datorn kan äga rum, så att du kan undvika uppdateringar under rusningstid för din arbetsbelastning. Mer information finns i [Automatisk korrigering för SQL Server i virtuella Azure-datorer (Resource Manager).](virtual-machines-windows-sql-automated-patching.md) |
| **Azure Key Vault-integrering** |Gör att du automatiskt kan installera och konfigurera Azure Key Vault på din VIRTUELLA SQL Server.Enables you to automatically install and configure Azure Key Vault on your SQL Server VM. Mer information finns i [Konfigurera Azure Key Vault-integrering för SQL Server på Virtuella Azure-datorer (Resource Manager).](virtual-machines-windows-ps-sql-keyvault.md) |

När SQL Server Iaas Agent Extension har installerats och körs gör det administrationsfunktionerna tillgängliga:

* På SQL Server-panelen på den virtuella datorn i Azure-portalen och via Azure PowerShell för SQL Server-avbildningar på Azure Marketplace.
* Via Azure PowerShell för manuella installationer av tillägget. 

## <a name="prerequisites"></a>Krav
Här är kraven för att använda SQL Server IaaS-agenttillägget på den virtuella datorn:

**Operativsystem:**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server-version:**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Hämta och konfigurera de senaste Azure PowerShell-kommandona](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Installation
SQL Server IaaS-tillägget installeras när du registrerar din VIRTUELLA SQL Server-dator med [SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). Om det behövs kan du installera SQL Server IaaS-agenten manuellt med kommandot PowerShell nedan: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> Om du installerar tillägget startas SQL Server-tjänsten om. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installera på en virtuell dator med en enda namngiven SQL Server-instans
SQL Server IaaS-tillägget fungerar med en namngiven instans på SQL Server om standardinstansen avinstalleras och IaaS-tillägget installeras om.

Så här använder du en namngiven instans av SQL Server:
   1. Distribuera en VIRTUELL SQL Server-dator från Azure Marketplace. 
   1. Avinstallera IaaS-tillägget från [Azure-portalen](https://portal.azure.com).
   1. Avinstallera SQL Server helt inom DEN VIRTUELLA SQL Server-datorn.
   1. Installera SQL Server med en namngiven instans i VIRTUELL SQL Server. 
   1. Installera IaaS-tillägget från Azure-portalen.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Hämta status för SQL Server IaaS-tillägget
Ett sätt att kontrollera att tillägget är installerat är att visa agentstatus i Azure-portalen. Välj **Alla inställningar** i fönstret för den virtuella datorn och välj sedan **Tillägg**. Du bör se **sqliaasextension-tillägget** i listan.

![Status för SQL Server IaaS-agenttillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Du kan också använda **Azure PowerShell-cmdleten Get-AzVMSqlServerExtension:**

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Det tidigare kommandot bekräftar att agenten är installerad och ger allmän statusinformation. Du kan få specifik statusinformation om automatisk säkerhetskopiering och korrigering med hjälp av följande kommandon:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Borttagning
I Azure-portalen kan du avinstallera tillägget genom att välja ellipsen i **fönstret Tillägg** för egenskaperna för den virtuella datorn. Välj sedan **Ta bort**.

![Avinstallera SQL Server IaaS-agenttillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Du kan också använda **PowerShell-cmdleten Remove-AzVMSqlServerExtension** PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Nästa steg
Börja använda en av de tjänster som tillägget stöder. Mer information finns i artiklarna som refereras i avsnittet [Tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [Vad är SQL Server på virtuella Azure-datorer?](virtual-machines-windows-sql-server-iaas-overview.md).
