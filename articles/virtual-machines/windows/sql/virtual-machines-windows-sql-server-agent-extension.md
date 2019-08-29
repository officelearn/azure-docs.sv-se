---
title: Automatisera hanterings uppgifter på virtuella Azure-datorer med hjälp av SQL Server IaaS agent-tillägg | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar SQL Server IaaS agent Extension, som automatiserar vissa SQL Server administrations uppgifter. Detta inkluderar automatisk säkerhets kopiering, automatisk uppdatering och Azure Key Vault integrering.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f4dd529481a6216e43d35c76ecee734543d487f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100482"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatisera hanterings uppgifter på virtuella Azure-datorer med hjälp av tillägget SQL Server IaaS-agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassisk](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent-tillägget (SqlIaasExtension) körs på virtuella Azure-datorer för att automatisera administrationsuppgifter. Den här artikeln innehåller en översikt över de tjänster som stöds av tillägget. Den här artikeln innehåller också anvisningar för installation, status och borttagning av tillägget.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

För att visa den klassiska versionen av den här artikeln, se [SQL Server IaaS agent Extension för SQL Server virtuella datorer (klassisk)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Det finns tre hanterbarhets lägen för SQL Server IaaS-tillägget: 

- **Fullständigt** läge ger alla funktioner, men kräver omstart av SQL Server-och system administratörs behörighet. Detta är det alternativ som installeras som standard. Använd den för att hantera en SQL Server VM med en enda instans. 

- **Lightweight** kräver inte omstart av SQL Server, men den stöder bara ändring av licens typ och utgåva av SQL Server. Använd det här alternativet för SQL Server virtuella datorer med flera instanser eller för deltagande i en instans av en redundanskluster (FCI). 

- **Noagent** är dedikerat för SQL Server 2008 och SQL Server 2008 R2 installerat på Windows Server 2008. Information om hur du använder det här läget för Windows Server 2008-avbildningen finns i [Windows Server 2008-registrering](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms). 

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

## <a name="prerequisites"></a>Förutsättningar
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


## <a name="change-management-modes"></a>Ändra hanterings lägen

Du kan visa det aktuella läget för SQL Server IaaS-agenten med hjälp av PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server virtuella datorer som har tillägget *Lightweight* IaaS installerat kan uppgradera läget till _full_ med hjälp av Azure Portal. SQL Server virtuella datorer i _no-agent-_ läge kan uppgraderas till _full_ efter att operativ systemet har uppgraderats till Windows 2008 R2 och senare. Det går inte att nedgradera – om du vill göra det måste du avinstallera SQL IaaS-tillägget fullständigt och installera det igen. 

Så här uppgraderar du agentens läge till fullständigt: 


# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till resursen för [virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Välj din SQL Server virtuella dator och välj **Översikt**. 
1. För SQL Server virtuella datorer med noagent-eller Lightweight IaaS-läge väljer du de **enda licens typ-och versions uppdateringar som är tillgängliga med meddelandet SQL IaaS-tillägg** .

   ![Val för att ändra läget från portalen](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Markera kryss rutan **Jag accepterar att starta om SQL Server tjänsten på den virtuella datorn** och välj sedan **Bekräfta** för att uppgradera IaaS-läget till full. 

    ![Kryss ruta för att komma överens om att starta om SQL Server tjänsten på den virtuella datorn](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Kör följande AZ CLI-kodfragment:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Kör följande PowerShell-kodfragment:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Installation
Tillägget SQL Server IaaS installeras när du registrerar din SQL Server VM med providern för [SQL VM-resurs](virtual-machines-windows-sql-register-with-resource-provider.md). Om det behövs kan du installera SQL Server IaaS-agenten manuellt med hjälp av läget fullständig eller förenklad. 

SQL Server IaaS agent extension i fullständigt läge installeras automatiskt när du etablerar en SQL Server virtuell dator med Azure Marketplace-avbildningar med hjälp av Azure Portal. 

### <a name="install-in-full-mode"></a>Installera i fullständigt läge
Det fullständiga läget för SQL Server IaaS-tillägget ger fullständig hanterbarhet för en enskild instans på SQL Server VM. Om det finns en standard instans kommer tillägget att fungera med standard instansen och stöder inte hantering av andra instanser. Om det inte finns någon standard instans men bara en namngiven instans, kommer den att hantera den namngivna instansen. Om det inte finns någon standard instans och det finns flera namngivna instanser, kommer tillägget inte att installeras. 

Installera SQL Server IaaS-agenten med fullständigt läge med hjälp av PowerShell:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Install 'Full' SQL Server IaaS agent extension
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parameter | Acceptabla värden                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` eller `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Om tillägget inte redan är installerat startas den SQL Server tjänsten om du installerar det fullständiga tillägget. Undvik att starta om tjänsten SQL Server genom att installera det lätta läget med begränsad hanterbarhet i stället.
> 
> Uppdatering av SQL Server IaaS-tillägget startar inte om SQL Server-tjänsten. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installera på en virtuell dator med en enda namngiven SQL Server-instans
SQL Server IaaS-tillägget fungerar med en namngiven instans på SQL Server om standard instansen avinstalleras och IaaS-tillägget installeras om.

Så här använder du en namngiven instans av SQL Server:
   1. Distribuera en SQL Server VM från Azure Marketplace. 
   1. Avinstallera IaaS-tillägget från [Azure Portal](https://portal.azure.com).
   1. Avinstallera SQL Server helt i SQL Server VM.
   1. Installera SQL Server med en namngiven instans i SQL Server VM. 
   1. Installera IaaS-tillägget från Azure Portal.  


### <a name="install-in-lightweight-mode"></a>Installera i Lightweight-läge
Läget för förenklad omstart kommer inte att starta om SQL Server tjänsten, men den erbjuder begränsade funktioner. 

Installera SQL Server IaaS-agenten med lättviktigt läge med hjälp av PowerShell:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parameter | Acceptabla värden                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` eller `PAYG`     |
| &nbsp;             | &nbsp;                          |


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

