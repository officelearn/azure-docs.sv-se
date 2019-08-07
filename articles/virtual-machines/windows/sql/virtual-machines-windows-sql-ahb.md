---
title: Ändra licensierings modell för en SQL Server VM i Azure
description: Lär dig hur du växlar licensiering för en virtuell SQL-dator i Azure från "betala per användning" till "ta egen licens" med hjälp av Azure Hybrid-förmån.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816731"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Ändra licensierings modell för en SQL Server virtuell dator i Azure
Den här artikeln beskriver hur du ändrar licensierings modellen för en SQL Server virtuell dator i Azure med hjälp av den nya providern för SQL VM-resurs- **Microsoft. SqlVirtualMachine**.

Det finns två licensierings modeller för en virtuell dator (VM) som värd SQL Server betala per användning och Azure Hybrid-förmån (AHB). Nu kan du använda antingen Azure Portal, Azure CLI eller PowerShell för att ändra licens modell för din SQL Server VM. 

Modellen för att **betala** per användning (PAYG) innebär att kostnaden för den virtuella Azure-datorn per sekund inkluderar kostnaden för den SQL Server licensen.
Med [Azure Hybrid-förmån (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) kan du använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. 

Microsoft Azure Hybrid-förmånen för SQL Server gör det möjligt att använda SQL Server licenser med Software Assurance ("kvalificerad licens") på Azure Virtual Machines. Med Azure Hybrid-förmån för SQL Server debiteras kunderna inte för användningen av SQL Server licens på den virtuella datorn, men de måste fortfarande betala för kostnaden för den underliggande moln beräkningen (det vill säga bas priset), lagringen och Server gruppen samt I/O som är kopplade till deras u Se för tjänsterna (om det är tillämpligt).

Enligt Microsofts produkt villkor måste kunderna ange att de använder Azure SQL Database (hanterad instans, Elastisk pool och Enkel databas), Azure Data Factory, SQL Server Integration Services eller SQL Server Virtual Machines i Azure Hybrid Förmånen för SQL Server när du konfigurerar arbets belastningar på Azure. "

Det finns tre alternativ för att ange användningen av Azure Hybrid-förmån för SQL Server på Azure VM och vara kompatibel:

1. Etablera en virtuell dator med hjälp av en BYOL SQL Server avbildning från Azure Marketplace, som endast är tillgänglig för kunder med Enterprise-avtal.
1. Etablera en virtuell dator med hjälp av en PAYG SQL Server avbildning från Azure Marketplace och aktivera AHB.
1. Själv installera SQL Server på en virtuell Azure-dator, [registrera deras SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) manuellt och aktivera AHB.

Licens typen för SQL Server anges när den virtuella datorn är etablerad och kan ändras när som helst efteråt. Växling mellan licens modeller medför **ingen stillestånds**tid, startar inte om den virtuella datorn, ger **ingen extra kostnad** (i själva verket aktiverar AHB *lägre* kostnader) och träder i **kraft omedelbart**. 

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda SQL-providern för virtuella dator resurser krävs SQL IaaS-tillägget. För att du ska kunna fortsätta med att använda den virtuella SQL-resurs leverantören behöver du därför följande:
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- En [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrerad med den [virtuella SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md) -providern installerad. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Ändra licens för virtuella datorer som redan har registrerats med resurs leverantören 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra licensierings modellen direkt från portalen. 

1. Öppna [Azure Portal](https://portal.azure.com) och starta [resursen SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) för din SQL Server VM. 
1. Välj **Konfigurera** under **Inställningar**. 
1. Välj alternativet **Azure Hybrid-förmån** och markera kryss rutan för att bekräfta att du har en SQL Server-licens med Software Assurance. 
1. Välj **Använd** längst ned på sidan **Konfigurera** . 

![AHB i portalen](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Du kan använda Azure CLI för att ändra licensierings modell.  

Följande kodfragment växlar licens modellen betala per användning till BYOL (eller med hjälp av Azure Hybrid-förmån):

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Du kan använda PowerShell för att ändra licensierings modell.

Följande kodfragment växlar licens modellen betala per användning till BYOL (eller med hjälp av Azure Hybrid-förmån):

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

Följande kodfragment växlar din BYOL-modell för att betala per användning:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Ändra licens för virtuella datorer som inte har registrerats med resurs leverantören

Om du etablerade en SQL Server VM från PAYG Azure Marketplace-avbildningar blir SQL-licensserver PAYG. Om du etablerade en SQL Server VM med en BYOL-avbildning från Azure Marketplace blir licens typen AHUB. Alla SQL Server virtuella datorer som tillhandahålls från standard (PAYG) eller BYOL Azure Marketplace-avbildningar registreras automatiskt med den virtuella SQL-providern för virtuella datorer, så att de kan ändra [licens typen](#change-license-for-vms-already-registered-with-resource-provider)

Du är bara berättigad till att själv installera SQL Server på virtuella Azure-datorer via Azure Hybrid-förmån och du bör [registrera de virtuella datorerna med en SQL VM-resurs leverantör](virtual-machines-windows-sql-register-with-resource-provider.md) genom att ange SQL Server licens som AHB för att ange AHB-användningen enligt Microsofts produkt villkor.

Du kan bara ändra licens typen för en SQL Server VM som PAYG eller AHB om den virtuella SQL-datorn har registrerats med SQL VM Resource Provider; och alla virtuella SQL-datorer bör registreras med SQL VM RP för licens efterlevnad.

## <a name="remarks"></a>Kommentarer

 - Azure Cloud Solution Partner (CSP)-kunder kan använda Azure Hybrid-förmån genom att först distribuera en virtuell dator där du betalar per användning och sedan konvertera den till en egen licens om de har aktiva SA.
 - Om du tar bort din SQL Server VM-resurs går du tillbaka till avbildningens hårdkodade licens inställningar. 
  - Möjligheten att ändra licensierings modellen är en funktion i SQL VM Resource Provider. Om du distribuerar en Marketplace-avbildning via Azure Portal registreras automatiskt en SQL Server VM med resurs leverantören. Kunder som själv installerar SQL Server måste dock [registrera sina SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)manuellt. 
- Om du lägger till en SQL Server VM i en tillgänglighets uppsättning måste den virtuella datorn skapas om. Det innebär att alla virtuella datorer som läggs till i en tillgänglighets uppsättning återgår till standard licens typen betala per användning och att AHB måste aktive ras igen. 


## <a name="limitations"></a>Begränsningar

 - Att ändra licensierings modellen är bara tillgänglig för kunder med Software Assurance.
 - Det går bara att ändra licensierings modellen för standard-och Enterprise-versionen av SQL Server. Licens ändringar för Express, webb och utvecklare stöds inte. 
 - Det går bara att ändra licensierings modellen för virtuella datorer som distribueras med Resource Manager-modellen. Virtuella datorer som distribueras med den klassiska modellen stöds inte. Du kan migrera den virtuella datorn från den klassiska till Resource Manager-modellen (ARM) och registrera dig för en SQL VM-resurs leverantör. När den virtuella datorn har registrerats med resurs leverantören för SQL-VM kommer licensierings modell ändringar att vara tillgängliga på den virtuella datorn. 
 - Att ändra licensierings modellen är bara aktive rad för offentliga moln installationer.
 - Det går bara att ändra licensierings modellen på virtuella datorer som har ett enda nätverkskort (nätverks gränssnitt). På virtuella datorer som har mer än ett nätverkskort bör du först ta bort ett av nätverkskorten (genom att använda Azure Portal) innan du provar proceduren. Annars kan du köra ett fel som liknar följande: `The virtual machine '\<vmname\>' has more than one NIC associated.`Även om du kanske kan lägga till NÄTVERKSKORTet på den virtuella datorn efter att du har ändrat licensierings läget, kommer åtgärder som görs via sidan för SQL-konfiguration i Azure Portal, t. ex. automatisk uppdatering och säkerhets kopiering, inte längre att anses som stöd.


## <a name="known-errors"></a>Kända fel

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Det gick inte att hitta resursen Microsoft. SqlVirtualMachine\</SqlVirtualMachines/resurs grupp > under resurs\<gruppen resurs grupp >. Det går inte att hitta egenskapen sqlServerLicenseType för det här objektet. Kontrol lera att egenskapen finns och kan ställas in.
Det här felet uppstår vid försök att ändra licensierings modellen på ett SQL Server VM som inte har registrerats med den virtuella SQL-adressresursen. Du måste registrera resurs leverantören för din [prenumeration](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)och registrera SQL Server VM med SQL- [adressresursen](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Det går inte att verifiera argumentet för parametern SKU
Du kan stöta på det här felet när du försöker ändra SQL Server VM licensierings modell när du använder Azure PowerShell > 4,0:`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Du kan lösa det här felet genom att ta bort kommentaren till de här raderna i det tidigare nämnda PowerShell-kodfragmentet när du byter licensierings modell:

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
* [SQL Server vanliga frågor och svar om Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server om pris vägledning för Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server en versions information för Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


