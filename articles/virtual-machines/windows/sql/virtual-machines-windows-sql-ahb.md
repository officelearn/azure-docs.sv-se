---
title: Ändra så att licensieringsmodellen för en SQL-VM i Azure | Microsoft Docs
description: Lär dig mer om att växla licenser för en SQL-dator i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c516bf9b48164f2ef8dc7fea6fb834bdae00a0d1
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332157"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Så här ändrar du så att licensieringsmodellen för en SQL Server-dator i Azure
Den här artikeln beskriver hur du ändrar så att licensieringsmodellen för en SQL Server-dator i Azure med hjälp av den nya SQL-resursprovider - **Microsoft.SqlVirtualMachine**. Det finns två licensiering modeller för en virtuell dator (VM) som är värd för SQL Server – betala per användning, och Använd din egen licens (BYOL). Och nu, med hjälp av PowerShell eller Azure CLI, kan du ändra vilken licensieringsmodell som använder din SQL-VM. 

Den **betala per användning** modellen innebär att kostnaden per sekund för att köra Azure VM omfattar kostnaden för SQL Server-licens.

Den **Bring-your-own-license** modellen är även känd som den [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/), och du kan använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. Mer information om priser finns i [SQL VM-pris för](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Växla mellan de två modellerna licens medför **utan avbrott**, startar inte den virtuella datorn, lägger till **utan extra kostnad** (i själva verket aktivera AHB minskar kostnaden) och är **från och med nu**. 

## <a name="prerequisites"></a>Förutsättningar
Användningen av SQL VM-resursprovidern kräver SQL IaaS-tillägget. Det innebär för att fortsätta använda SQL VM-resursprovidern, behöver du följande:
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) med den [SQL IaaS-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) installerad. 

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Registrera den befintliga SQL VM med ny resursprovider
Möjligheten att byta mellan licensieringsmodellerna är en funktion som tillhandahålls av den nya SQL-VM-resursprovidern (Microsoft.SqlVirtualMachine). Just nu, om du vill kunna växla din licensieringsmodell måste du börjar registrera den nya providern till din prenumeration och sedan registrera den befintliga virtuella datorn med den nya SQL-VM-resursprovidern. Du måste installera SQL IaaS-tillägget för att använda SQL VM-resursprovidern. På så sätt kan du registrera en virtuell dator som har distribuerats med en virtuell Hårddisk. Mer information finns i [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md). 

  >[!IMPORTANT]
  > Om du släpper ditt SQL VM-resurs ska du gå tillbaka till inställningen hårdkodad licens för avbildningen. 
  

### <a name="powershell"></a>PowerShell


Följande kodavsnitt kommer ansluta dig till Azure och kontrollera vilka prenumerations-ID som du använder. 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

Följande kodavsnitt först registrerar nya SQL-resursprovidern för din prenumeration och registrerar sedan din befintliga SQL-VM med nya resursprovidern. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portalen
Du kan också registrera nya SQL VM-resursprovidern med hjälp av portalen. Att så Följ dessa steg:
1. Öppna Azure portal och gå till **alla tjänster**. 
1. Gå till **prenumerationer** och välj prenumerationen av intresse.  
1. I den **prenumerationer** bladet går du till **Resursprovidern**. 
1. Typ `sql` i filtret för att få fram de SQL-relaterade resursprovidrar. 
1. Välj antingen *registrera*, *Omregistrera*, eller *avregistrera* för den **Microsoft.SqlVirtualMachine** providern beroende på din önskad åtgärd. 

  ![Ändra providern](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Använd PowerShell 
Du kan använda PowerShell för att ändra licensieringsmodellen.  Var noga med att din SQL-VM har redan registrerats med den nya SQL-resursprovidern innan du växlar så att licensieringsmodellen. 

Följande kodavsnitt växlar modellen betala per användning-licens till BYOL (eller med hjälp av Azure Hybrid-förmånen): 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

Följande kodavsnitt växlar BYOL-modell till betala per användning:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Om du vill växla mellan licenser, måste du använda den nya SQL-VM-resursprovidern. Det här felet kan uppstå om du försöker köra dessa kommandon innan du registrerar din SQL-VM med den nya providern: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Om du ser det här felet kan du [registrera din SQL-VM med ny resursprovider](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Använda Azure CLI
Du kan använda Azure CLI för att ändra licensieringsmodellen.  Var noga med att din SQL-VM har redan registrerats med den nya SQL-resursprovidern innan du växlar så att licensieringsmodellen. 

Följande kodavsnitt växlar modellen betala per användning-licens till BYOL (eller med hjälp av Azure Hybrid-förmånen):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Följande kodavsnitt växlar BYOL-modell till betala per användning: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Om du vill växla mellan licenser, måste du använda den nya SQL-VM-resursprovidern. Det här felet kan uppstå om du försöker köra dessa kommandon innan du registrerar din SQL-VM med den nya providern: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Om du ser det här felet kan du [registrera din SQL-VM med ny resursprovider](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Visa aktuella licensieringen 

Följande kodavsnitt kan du visa aktuella licensieringsmodellen för din SQL-VM. 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Kända fel

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>SQL IaaS-tillägget har inte installerats på den virtuella datorn
SQL IaaS-tillägget är ett nödvändigt krav för att registrera din SQL Server-dator med SQL VM-resursprovidern. Om du försöker registrera din SQL Server-VM innan du installerar tillägget SQL IaaS uppstår följande fel:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Lös problemet genom att installera SQL IaaS-tillägget innan du försöker registrera din SQL Server-VM. 

  > [!NOTE]
  > Installera SQL IaaS tillägget startar om SQL Server-tjänsten och bör endast göras under ett underhållsfönster. Mer information finns i [SQL IaaS tilläggsinstallationen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Det går inte att verifiera argumentet i parametern ”Sku”
Det här felet kan uppstå när du försöker ändra licensieringsmodellen för SQL Server-dator när du använder Azure PowerShell > 4.0:

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Lös felet genom att ta bort kommentarerna raderna i kodfragmentet tidigare nämnda PowerShell när du växlar licensieringsmodellen: 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Använd följande kod för att verifiera Azure PowerShell-version:

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer på en Windows VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server på en Windows-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en Windows VM viktig information](virtual-machines-windows-sql-server-iaas-release-notes.md)


