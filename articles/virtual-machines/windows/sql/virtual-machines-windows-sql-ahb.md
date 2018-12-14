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
ms.openlocfilehash: cd784163047f4fe15fde719ce56aba64eed60dd2
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336993"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Så här ändrar du så att licensieringsmodellen för en SQL Server-dator i Azure
Den här artikeln beskriver hur du ändrar så att licensieringsmodellen för en SQL Server-dator i Azure med hjälp av den nya SQL-resursprovider - **Microsoft.SqlVirtualMachine**. Det finns två licensiering modeller för en virtuell dator (VM) som är värd för SQL Server – betala per användning, och Använd din egen licens (BYOL). Och nu, med hjälp av PowerShell eller Azure CLI, kan du ändra vilken licensieringsmodell som använder din SQL-VM. 

Den **betala per användning** modellen innebär att kostnaden per sekund för att köra Azure VM omfattar kostnaden för SQL Server-licens.

Den **Bring-your-own-license** modellen är även känd som den [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/), och du kan använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. Mer information om priser finns i [SQL VM-pris för](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Växla mellan de två modellerna licens medför **utan avbrott**, startar inte den virtuella datorn, lägger till **utan extra kostnad** (i själva verket aktivera AHB minskar kostnaden) och är **från och med nu**. 


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
$SqlVm | Set-AzureRmResource -Force 
``` 

Följande kodavsnitt växlar BYOL-modell till betala per användning:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
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

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer på en Windows VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server på en Windows-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en Windows VM viktig information](virtual-machines-windows-sql-server-iaas-release-notes.md)


