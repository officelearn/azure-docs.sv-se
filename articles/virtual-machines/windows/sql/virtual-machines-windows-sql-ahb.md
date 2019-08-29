---
title: Ändra licens modell för en SQL Server VM i Azure
description: Lär dig hur du växlar licensiering för en SQL Server virtuell dator i Azure från betala per användning för att få en egen licens genom att använda Azure Hybrid-förmån.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cce369f6a0670790ede7367609f87c18672ddd5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100619"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Ändra licens modell för en SQL Server virtuell dator i Azure
I den här artikeln beskrivs hur du ändrar licens modellen för en SQL Server virtuell dator (VM) i Azure med hjälp av den nya providern för SQL VM-resurs, **Microsoft. SqlVirtualMachine**.

Det finns två licens modeller för en virtuell dator som är värd för SQL Server: betala per användning och Azure Hybrid-förmån. Du kan ändra licens modellen för dina SQL Server VM med hjälp av Azure Portal, Azure CLI eller PowerShell. 

Modellen "betala per användning" innebär att kostnaden för den virtuella Azure-datorn per sekund inkluderar kostnaden för den SQL Server licensen.
[Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) gör att du kan använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. 

Azure Hybrid-förmån tillåter användningen av SQL Server licenser med Software Assurance ("kvalificerad licens") på virtuella Azure-datorer. Med Azure Hybrid-förmån debiteras kunderna inte för att använda en SQL Server-licens på en virtuell dator. Men de måste fortfarande betala för kostnaden för den underliggande moln beräkningen (det vill säga bas priset), lagringen och säkerhets kopieringarna. De måste också betala för I/O som är kopplade till deras användning av tjänsterna (om det är tillämpligt).

Enligt Microsofts produkt villkor: "Kunderna måste ange att de använder Azure SQL Database (hanterad instans, Elastisk pool och Enkel databas), Azure Data Factory, SQL Server Integration Services eller SQL Server Virtual Machines under Azure Hybrid-förmån för SQL Server när du konfigurerar arbets belastningar på Azure. "

Om du vill ange användningen av Azure Hybrid-förmån för SQL Server på en virtuell Azure-dator och är kompatibel, har du tre alternativ:

- Etablera en virtuell dator med hjälp av en egen licens SQL Server-avbildning från Azure Marketplace. Det här alternativet är endast tillgängligt för kunder som har en Enterprise-avtal.
- Etablera en virtuell dator med hjälp av en SQL Server avbildning som du betalar per användning från Azure Marketplace och aktivera Azure Hybrid-förmån.
- Själv installera SQL Server på en virtuell Azure-dator, [registrera SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)manuellt och aktivera Azure Hybrid-förmån.

Licens typen för SQL Server anges när den virtuella datorn har tillhandahållits. Du kan ändra det när som helst efteråt. Växling mellan licens modeller medför ingen nedtid, startar inte om den virtuella datorn, lägger till ingen ytterligare kostnad och börjar gälla omedelbart. Aktiverings Azure Hybrid-förmån *minskar* i själva verket kostnaderna.

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda SQL VM-IaaS måste tillägget SQL Server. Därför behöver du följande:
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- En [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrerad med [resurs leverantören för SQL-VM](virtual-machines-windows-sql-register-with-resource-provider.md).


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Ändra licensen för virtuella datorer som redan har registrerats hos resurs leverantören 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra licens modellen direkt från portalen: 

1. Öppna [Azure Portal](https://portal.azure.com) och öppna [resursen SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) för din SQL Server VM. 
1. Välj **Konfigurera** under **Inställningar**. 
1. Välj alternativet **Azure Hybrid-förmån** och markera kryss rutan för att bekräfta att du har en SQL Server-licens med Software Assurance. 
1. Välj **Använd** längst ned på sidan **Konfigurera** . 

![Azure Hybrid-förmån i portalen](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan använda Azure CLI för att ändra din licens modell.  

Följande kodfragment växlar din licens modell enligt principen betala per användning för att få en egen licens (eller med hjälp av Azure Hybrid-förmån):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Följande kodfragment växlar din egen egen licens modell för att betala per användning: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Du kan använda PowerShell för att ändra din licens modell.

Följande kodfragment växlar din licens modell enligt principen betala per användning för att få en egen licens (eller med hjälp av Azure Hybrid-förmån):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Följande kodfragment växlar din egen egen licens modell för att betala per användning:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Ändra licensen för virtuella datorer som inte har registrerats med resurs leverantören

Om du etablerade en SQL Server VM från Azure Marketplace-avbildningar enligt principen betala per användning, kommer SQL Server licens typen att betala per användning. Om du har upprättat en SQL Server VM med hjälp av en egen licens avbildning från Azure Marketplace blir licens typen AHUB. Alla SQL Server virtuella datorer som tillhandahålls från standardvärdet (betala per användning) eller med en egen licens för Azure Marketplace-avbildningar registreras automatiskt med den virtuella SQL-resurs leverantören, så att de kan ändra [licens typen](#change-the-license-for-vms-already-registered-with-the-resource-provider).

Du är bara berättigad till att själv installera SQL Server på en virtuell Azure-dator via Azure Hybrid-förmån. Du bör [registrera de virtuella datorerna med resurs leverantören för SQL-VM](virtual-machines-windows-sql-register-with-resource-provider.md) genom att ange den SQL Server licensen som Azure Hybrid-förmån, för att ange Azure Hybrid-förmån användning enligt Microsofts produkt villkor.

Du kan ändra licens typen för en SQL Server VM som "betala per användning" eller Azure Hybrid-förmån endast om SQL Server VM har registrerats med resurs leverantören för SQL-VM. Alla SQL Server virtuella datorer bör registreras hos resurs leverantören för licens efterlevnad.

## <a name="remarks"></a>Kommentarer

- Azure Cloud Solution Provider (CSP)-kunder kan använda Azure Hybrid-förmån genom att först distribuera en virtuell dator där du betalar per användning och sedan konvertera den till en egen licens om de har en aktiv Software Assurance.
- Om du tar bort din SQL Server VM-resurs går du tillbaka till avbildningens hårdkodade licens inställningar. 
- Möjligheten att ändra licens modellen är en funktion i SQL VM Resource Provider. Genom att distribuera en Azure Marketplace-avbildning via Azure Portal registreras automatiskt en SQL Server VM med resurs leverantören. Men kunder som själv installerar SQL Server måste [registrera sina SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)manuellt. 
- Om du lägger till en SQL Server VM i en tillgänglighets uppsättning måste den virtuella datorn skapas på nytt. Det innebär att alla virtuella datorer som läggs till i en tillgänglighets uppsättning återgår till standard licens typen betala per användning. Azure Hybrid-förmån måste aktive ras igen. 


## <a name="limitations"></a>Begränsningar

- Att ändra licens modellen är bara tillgänglig för kunder som har Software Assurance.
- Det går bara att ändra licens modellen för standard-och Enterprise-utgåvorna av SQL Server. Licens ändringar för Express, webb och utvecklare stöds inte. 
- Det går bara att ändra licens modellen för virtuella datorer som distribueras via Azure Resource Managers modellen. Virtuella datorer som distribueras via den klassiska modellen stöds inte. Du kan migrera den virtuella datorn från klassisk till Resource Manager-modellen och registrera den med resurs leverantören för SQL-VM. När den virtuella datorn har registrerats med resurs leverantören för SQL-VM kommer licens modell ändringar att vara tillgängliga på den virtuella datorn.
- Att ändra licens modellen är bara aktive rad för offentliga moln installationer.
- Det går bara att ändra licens modellen på virtuella datorer som har ett enda nätverkskort (nätverks gränssnitt). På virtuella datorer som har mer än ett nätverkskort bör du först ta bort ett av nätverkskorten (genom att använda Azure Portal) innan du provar proceduren. Annars visas ett fel som liknar följande: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  Även om du kanske kan lägga till NÄTVERKSKORTet på den virtuella datorn efter att du har ändrat licens modellen, kommer åtgärder som utförs via SQL Server konfigurations sidan i Azure Portal, som automatisk uppdatering och säkerhets kopiering inte längre att anses som stöd.

## <a name="known-errors"></a>Kända fel

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Det gick inte att hitta resursen Microsoft. SqlVirtualMachine\</SqlVirtualMachines/resurs grupp > under resurs\<gruppen resurs grupp >.
Det här felet uppstår när du försöker ändra licens modellen på en SQL Server VM som inte har registrerats med SQL VM-resurs leverantören:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Du måste registrera resurs leverantören för din [prenumeration](virtual-machines-windows-sql-register-with-resource-provider.md#register-the-sql-vm-resource-provider-with-a-subscription)och [Registrera SQL Server VM med resurs leverantören](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Det går inte att verifiera argumentet för parametern SKU
Du kan stöta på det här felet när du försöker ändra SQL Server VM licens modell genom att använda Azure PowerShell versioner senare än 4,0:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Du kan lösa det här felet genom att ta bort kommentaren till raderna i det tidigare nämnda PowerShell-kodfragmentet när du växlar din licens modell:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Använd följande kod för att verifiera din Azure PowerShell-version:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)


