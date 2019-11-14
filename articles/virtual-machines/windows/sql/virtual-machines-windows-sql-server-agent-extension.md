---
title: Automatisera hanterings uppgifter med IaaS agent-tillägg
description: Den här artikeln beskriver hur du hanterar SQL Server IaaS agent Extension, som automatiserar vissa SQL Server administrations uppgifter. Detta inkluderar automatisk säkerhets kopiering, automatisk uppdatering och Azure Key Vault integrering.
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
ms.openlocfilehash: 9aae386e21df6711fc4984a7abfd34f418399f76
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034201"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatisera hanterings uppgifter på virtuella Azure-datorer med hjälp av tillägget SQL Server IaaS-agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent-tillägget (SqlIaasExtension) körs på virtuella Azure-datorer för att automatisera administrationsuppgifter. Den här artikeln innehåller en översikt över de tjänster som stöds av tillägget. Den här artikeln innehåller också anvisningar för installation, status och borttagning av tillägget.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

För att visa den klassiska versionen av den här artikeln, se [SQL Server IaaS agent Extension för SQL Server virtuella datorer (klassisk)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Tjänster som stöds
Tillägget SQL Server IaaS-Agent stöder följande administrations aktiviteter:

| Administrations funktion | Beskrivning |
| --- | --- |
| **SQL Server automatisk säkerhets kopiering** |Automatisera schemaläggning av säkerhets kopieringar för alla databaser antingen för standard instansen eller en [korrekt installerad](virtual-machines-windows-sql-server-iaas-faq.md#administration) namngiven instans av SQL Server på den virtuella datorn. Mer information finns i [Automatisk säkerhets kopiering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server automatisk uppdatering** |Konfigurerar en underhålls period då viktiga Windows-uppdateringar av din virtuella dator kan ske, så att du kan undvika uppdateringar under hög belastnings tider för din arbets belastning. Mer information finns i [automatiserad uppdatering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Gör att du kan installera och konfigurera Azure Key Vault automatiskt på din SQL Server VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure-Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

När SQL Server IaaS agent-tillägget är installerat och körs, blir administrations funktionerna tillgängliga:

* På den SQL Server panelen i den virtuella datorn i Azure Portal och via Azure PowerShell för SQL Server avbildningar på Azure Marketplace.
* Genom Azure PowerShell för manuella installationer av tillägget. 

## <a name="prerequisites"></a>Krav
Här följer kraven för att använda SQL Server IaaS agent Extension på den virtuella datorn:

**Operativsystem**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server version**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Hämta och konfigurera de senaste Azure PowerShell-kommandona](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Installation
Tillägget SQL Server IaaS installeras när du registrerar din SQL Server VM med [providern för SQL VM-resurs](virtual-machines-windows-sql-register-with-resource-provider.md). Om det behövs kan du installera SQL Server IaaS-agenten manuellt med hjälp av PowerShell-kommandot nedan: 

  ```powershell-interactive
    Set-AzVMExtension -ResourceGroupName "<ResourceGroupName>" `
    -Location "<VMLocation>" -VMName "<VMName>" `
    -Name "SqlIaasExtension" -Publisher "Microsoft.SqlServer.Management" `
    -ExtensionType "SqlIaaSAgent" -TypeHandlerVersion "2.0";  
  ```

> [!NOTE]
> Installation av tillägget startar om tjänsten SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installera på en virtuell dator med en enda namngiven SQL Server-instans
SQL Server IaaS-tillägget fungerar med en namngiven instans på SQL Server om standard instansen avinstalleras och IaaS-tillägget installeras om.

Följ dessa steg om du vill använda en namngiven instans av SQL Server:
   1. Distribuera en SQL Server VM från Azure Marketplace. 
   1. Avinstallera IaaS-tillägget från [Azure Portal](https://portal.azure.com).
   1. Avinstallera SQL Server helt i SQL Server VM.
   1. Installera SQL Server med en namngiven instans i SQL Server VM. 
   1. Installera IaaS-tillägget från Azure Portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Hämta status för SQL Server IaaS-tillägget
Ett sätt att kontrol lera att tillägget är installerat är att Visa agent statusen i Azure Portal. Välj **alla inställningar** i fönstret virtuell dator och välj sedan **tillägg**. Du bör se **SqlIaasExtension** -tillägget som visas.

![Status för SQL Server IaaS agent extension i Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Du kan också använda cmdleten **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Föregående kommando bekräftar att agenten är installerad och innehåller allmän statusinformation. Du kan hämta detaljerad statusinformation om automatisk säkerhets kopiering och korrigering med hjälp av följande kommandon:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Borttagning
I Azure Portal kan du avinstallera tillägget genom att välja ellipsen i fönstret **tillägg** i egenskaperna för den virtuella datorn. Välj sedan **Ta bort**.

![Avinstallera SQL Server IaaS agent extension i Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Du kan också använda PowerShell **-cmdleten Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Nästa steg
Börja använda en av de tjänster som tillägget stöder. Mer information finns i artiklarna som refereras i avsnittet [tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server för azure Virtual Machines?](virtual-machines-windows-sql-server-iaas-overview.md).
