---
title: Automatisera hanteringsuppgifter på Azure virtuella datorer med SQL Server IaaS Agent-tillägget | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar SQL Server agent-tillägget som automatiserar specifika uppgifter för administration av SQL Server. Dessa inkluderar automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798060"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Automatisera hanteringsuppgifter på Azure virtuella datorer med SQL Server IaaS Agent-tillägget
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent-tillägget (SqlIaasExtension) körs på virtuella Azure-datorer för att automatisera administrationsuppgifter. Den här artikeln innehåller en översikt och de tjänster som stöds av tillägget samt anvisningar för installation, status och borttagning.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den klassiska versionen av den här artikeln finns [SQL Server-Agenttillägg för SQL Server-datorer klassiska](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Det finns tre SQL hanterbarhet lägen för SQL IaaS-tillägget: **Fullständig**, **Lightweight**, och **NoAgent**. 

- **Fullständig** läge innehåller alla funktioner, men kräver en omstart av SQL Server och SA behörigheter. Det här är det alternativ som installeras som standard och bör användas för att hantera en SQL Server-dator med en enda instans. 

- **Lightweight** kräver inte omstart av SQL Server, men stöder endast ändra licenstyp och utgåva av SQL Server. Det här alternativet ska användas för SQL Server-datorer med flera instanser eller deltagande i en redundansklusterinstans (FCI). 

- **NoAgent** är reserverade för SQL Server 2008 och SQL Server 2008 R2 installeras på Windows Server 2008. Information om hur du använder `NoAgent` läge för din avbildning för Windows Server 2008, se [registrering av Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Tjänster som stöds
SQL Server IaaS Agent-tillägget har stöd för följande administrationsuppgifter:

| Administrationsfunktion | Beskrivning |
| --- | --- |
| **Automatisk SQL-säkerhetskopiering** |Automatiserar schemaläggning av säkerhetskopiering för alla databaser för antingen standardinstansen eller en [korrekt installerade](virtual-machines-windows-sql-server-iaas-faq.md#administration) namngiven instans av SQL Server på den virtuella datorn. Mer information finns i [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatisk SQL-uppdatering** |Konfigurerar en underhållsperiod då viktiga Windows-uppdateringar till den virtuella datorn kan äga rum, så att du kan undvika att uppdateringar under Högbelastningstider för din arbetsbelastning. Mer information finns i [automatisk uppdatering för SQL Server i Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault-integrering** |Kan du automatiskt installera och konfigurera Azure Key Vault på din SQL Server-VM. Mer information finns i [konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

När installerad och igång, tillgängliggör SQL Server IaaS Agent-tillägget dessa funktioner för administration på den SQL Server-panelen för den virtuella datorn i Azure-portalen och via Azure PowerShell för SQL Server marketplace-avbildningar och via Azure PowerShell för manuella installationer av tillägget. 

## <a name="prerequisites"></a>Förutsättningar
Krav för att använda SQL Server IaaS Agent-tillägget på den virtuella datorn:

**Operativsystemet**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server-versioner**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Hämta och konfigurera de senaste Azure PowerShell-kommandona](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Ändra lägen för hantering

Du kan visa det aktuella läget för din SQL IaaS-agent med hjälp av PowerShell: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

För SQL Server-datorer som har den *NoAgent* eller *lightweight* IaaS-tillägget installerat kan du uppgradera läge till *fullständig* med Azure portal. Det går inte att nedgradera - så behöver du helt avinstallera SQL IaaS-tillägget och installerar den igen. 

Att uppgradera agenten läge till *fullständig*, gör du följande: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till din [virtuella datorer med SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) resurs. 
1. Välj din SQL Server-dator och **översikt**. 
1. För SQL virtuella datorer med den *NoAgent* eller *lightweight* IaaS-lägen, Välj meddelandet för **endast licens typ och version uppdateringar är tillgängliga med tillägget SQL IaaS**.

    ![Starta ändring från portalen](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Godkänn **starta om SQL Server-tjänsten** genom att markera kryssrutan och välj sedan **Bekräfta** att uppgradera dina IaaS-läge till ”full”. 

    ![Aktivera fullständig hantering för IaaS-tillägget](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Installation
SQL IaaS-tillägget installeras när du registrerar din SQL Server-dator med den [SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Men om det behövs SQL IaaS-agenten kan också installeras manuellt med hjälp av *fullständig* eller *lightweight* installation läge. 

Den *fullständig* SQL Server IaaS Agent-tillägget installeras automatiskt när du etablerar en galleriavbildningar för SQL Server-dator med Azure portal. 

### <a name="full-mode-installation"></a>Läget för fullständig installation
Den *fullständig* SQL IaaS-tillägget ger fullständig hanterbarhet för en enskild instans på SQL Server-dator. Om det finns en standardinstans, sedan tillägget kommer att fungera med instansen och stöder inte hantering av andra instanser. Om det finns inga standardinstansen men bara en namngiven instans, ska som hanteras den namngivna instansen. Om det finns ingen standardinstans och det finns flera namngivna instanser, kan sedan tillägget inte installeras. 

Installera den *fullständig* läge med SQL IaaS startar om SQL Server-tjänsten. Om du vill undvika att starta om SQL Server-tjänsten, installera den *lightweight* läge med begränsad hanterbarhet i stället. 

Installera SQL IaaS-agenten med *fullständig* läge med hjälp av PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parameter | Godkända värden                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, eller `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Om tillägget inte redan är installerat, installerar den **fullständig** tillägget startar om SQL Server-tjänsten. Använd **lightweight** läge för att undvika att starta om SQL Server-tjänsten. 
> - SQL Server-tjänsten startar inte om du uppdaterar SQL IaaS-tillägget. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installera på en virtuell dator med en enda namngivna SQL Server-instans
SQL IaaS-tillägget fungerar med en namngiven instans på en SQL Server om standardinstansen avinstalleras och IaaS-tillägget installeras.

Om du vill använda en namngiven instans av SQL Server gör du följande:
   1. Distribuera en SQL Server-VM från marketplace. 
   1. Avinstallera tillägget IaaS inifrån den [Azure-portalen](https://portal.azure.com).
   1. Avinstallera SQL Server helt inom SQL Server-dator.
   1. Installera SQL Server med en namngiven instans i SQL Server-dator. 
   1. Installera IaaS-tillägget i Azure-portalen.  


### <a name="install-in-lightweight-mode"></a>Installera i Lightweight-läge
Enkelt läge startar inte om SQL Server-tjänsten, men den erbjuder begränsad funktionalitet. 

Installera SQL IaaS-agenten med *lightweight* läge med hjälp av PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parameter | Godkända värden                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, eller `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Hämta Status för SQL IaaS-tillägget
Ett sätt att kontrollera att tillägget har installerats är att visa agentens status på Azure-portalen. Välj **alla inställningar** i den virtuella datorn och sedan klicka på **tillägg**. Du bör se den **SqlIaasExtension** tillägg i listan.

![SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Du kan också använda den **Get-AzVMSqlServerExtension** Azure PowerShell-cmdleten.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Föregående kommando bekräftar agenten är installerad och ger allmän statusinformation. Du kan också hämta statusinformation om automatisk säkerhetskopiering och uppdatering med följande kommandon.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Borttagning av
I Azure-portalen kan du avinstallera tillägget genom att klicka på ellipsen på den **tillägg** fönster för dina VM-egenskaper. Klicka på **Ta bort**.

![Avinstallera SQL Server IaaS Agent-tillägget i Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Du kan också använda den **Remove-AzVMSqlServerExtension** PowerShell-cmdlet.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Nästa steg
Börja med någon av de tjänster som stöds av tillägget. Mer information finns i artiklar som refereras till i den [tjänster som stöds](#supported-services) i den här artikeln.

Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).

