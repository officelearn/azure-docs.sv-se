---
title: Automatisera hanterings uppgifter på virtuella SQL-datorer (klassisk) | Microsoft Docs
description: I det här avsnittet beskrivs hur du hanterar SQL Server Agent-tillägget som automatiserar vissa SQL Server administrations uppgifter. Detta inkluderar automatisk säkerhets kopiering, automatisk uppdatering och Azure Key Vault integrering. I det här avsnittet används det klassiska distributions läget.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982288"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatisera hanterings uppgifter på Azure Virtual Machines med SQL Server Agent-tillägget (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../classic/sql-server-agent-extension.md)
> 
>
 
SQL Server IaaS agent Extension (Sqliaasagent är) körs på virtuella Azure-datorer för att automatisera administrations uppgifter. Det här avsnittet innehåller en översikt över de tjänster som stöds av tillägget samt anvisningar för installation, status och borttagning.

> [!IMPORTANT] 
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om hur du visar Resource Manager-versionen av den här artikeln finns i [SQL Server Agent tillägget för SQL Server VM Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Tjänster som stöds
Tillägget SQL Server IaaS-Agent stöder följande administrations aktiviteter:

| Administrations funktion | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatisera schemaläggning av säkerhets kopieringar för alla databaser för standard instansen av SQL Server i den virtuella datorn. Mer information finns i [Automatisk säkerhets kopiering för SQL Server i Azure Virtual Machines (klassisk)](../classic/sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar en underhålls period då viktiga Windows-uppdateringar av din virtuella dator kan ske, så att du kan undvika uppdateringar under hög belastnings tider för din arbets belastning. Mer information finns i [Automatisk uppdatering för SQL Server i Azure Virtual Machines (klassisk)](../classic/sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Gör att du kan installera och konfigurera Azure Key Vault automatiskt på din SQL Server VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på virtuella Azure-datorer (klassisk)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Krav
Krav för att använda SQL Server IaaS agent Extension på den virtuella datorn:

### <a name="operating-system"></a>Operativsystem:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server versioner:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Hämta och konfigurera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).

Starta Windows PowerShell och Anslut den till din Azure-prenumeration med kommandot **Add-AzureAccount** .

    Add-AzureAccount

Om du har flera prenumerationer använder du **Select-AzureSubscription** för att välja den prenumeration som innehåller den klassiska virtuella datorn.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

I det här läget kan du hämta en lista över de klassiska virtuella datorerna och deras associerade tjänst namn med kommandot **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Installation
För klassiska virtuella datorer måste du använda PowerShell för att installera SQL Server IaaS agent extension och konfigurera tillhör ande tjänster. Använd cmdleten **set-AzureVMSqlServerExtension** PowerShell för att installera tillägget. Följande kommando installerar till exempel tillägget på en virtuell Windows Server-dator (klassisk) och namnger den "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Om du uppdaterar till den senaste versionen av SQL IaaS agent Extension måste du starta om den virtuella datorn när du har uppdaterat tillägget.

> [!NOTE]
> Klassiska virtuella datorer har inte möjlighet att installera och konfigurera SQL IaaS agent-tillägget via portalen.

> [!NOTE]
> Tillägget SQL Server IaaS-agent stöds bara på [SQL Server VM Galleri avbildningar](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (betala per användning eller ta med egen licens). Det finns inte stöd för att installera SQL Server manuellt på en virtuell Windows Server-dator med operativ system eller om du distribuerar en anpassad SQL Server VM VHD. I dessa fall kan det vara möjligt att installera och hantera tillägget manuellt med hjälp av PowerShell, men det rekommenderas starkt att istället installera en SQL Server VM Galleri avbildning och sedan anpassa den.

## <a name="status"></a>Status
Ett sätt att kontrol lera att tillägget är installerat är att Visa agent status i Azure-portalen. Välj en virtuell dator som visas på bladet virtuell dator och klicka sedan på **tillägg**. Du bör se **sqliaasagent är** -tillägget som visas.

![SQL Server IaaS agent extension i Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Du kan också använda cmdleten **Get-AzureVMSqlServerExtension** Azure PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Borttagning
I Azure-portalen kan du avinstallera tillägget genom att klicka på ellipsen på bladet **tillägg** i egenskaperna för den virtuella datorn. Klicka sedan på **Avinstallera**.

![Avinstallera SQL Server IaaS agent extension i Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Du kan också använda PowerShell **-cmdleten Remove-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Efterföljande moment
Börja använda en av de tjänster som stöds av tillägget. Mer information finns i avsnittet som refereras i avsnittet [tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på azure Virtual Machines översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

