---
title: Ändra licensieringsmodellen för en SQL Server-VM i Azure
description: Lär dig hur du växlar licenser för en SQL-dator i Azure från ”betala per användning” till ”bring-your-own-license” med Azure Hybrid-förmånen.
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786766"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Så här ändrar du licensieringsmodell för en SQL Server-dator i Azure
Den här artikeln beskriver hur du ändrar så att licensieringsmodellen för en SQL Server-dator i Azure med hjälp av den nya SQL-VM-resursprovidern - **Microsoft.SqlVirtualMachine**.

Det finns två licensieringsmodellerna för en virtuell dator (VM) som värd för SQL Server - betala per användning och Azure Hybrid-förmånen (AHB). Och nu, med hjälp av Azure portal, Azure CLI eller PowerShell kan du ändra licensieringsmodellen för din SQL Server-VM. 

Den **användningsbaserad** (PAYG) modellen innebär att kostnaden per sekund för att köra Azure VM omfattar kostnaden för SQL Server-licens.
Den [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) kan du använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. 

Microsoft Azure Hybrid-förmånen för SQL Server kan använda SQL Server-licenser med Software Assurance (”kvalificerade License”) på Azure Virtual Machines. Kunder debiteras inte för användning av SQL Server-licens på den virtuella datorn med Azure Hybrid-förmånen för SQL Server, men de måste fortfarande betala för kostnaden för de beräkningsresurser till molnet (det vill säga grundpris), lagring, säkerhetskopiering, samt och i/o som är associerad med deras u Se tjänster (som tillämpligt).

Enligt villkoren för Microsoft-produkt ”kunder måste ange att de använder Azure SQL Database (hanterad instans, elastisk Pool och enkel databas), Azure Data Factory, SQL Server Integration Services eller SQL Server-datorer under Azure Hybrid Förmån för SQL Server när du konfigurerar arbetsbelastningar på Azure ”.

Visar användningen av Azure Hybrid-förmånen för SQL Server på virtuella Azure-datorn och vara kompatibel genom att det finns tre alternativ:

1. Etablera en virtuell dator med en SQL Server BYOL-avbildning från Azure marketplace, endast tillgängligt för kunder med Enterprise-avtal.
1. Etablera en virtuell dator med en PAYG SQL Server-avbildning från Azure marketplace och aktivera AHB.
1. Installera lokal SQL Server på en Azure-dator manuellt [registrera sina SQL Server-VM](virtual-machines-windows-sql-register-with-resource-provider.md) och aktivera AHB.

Licenstyp av SQL Server har angetts när den virtuella datorn har etablerats och kan ändras när som helst efteråt. Växla mellan licensmodeller medför **utan avbrott**, startar inte den virtuella datorn, lägger till **utan extra kostnad** (i själva verket aktivera AHB *minskar* kostnaden) och är **från och med nu**. 

## <a name="prerequisites"></a>Förutsättningar

Användningen av SQL VM-resursprovidern kräver SQL IaaS-tillägget. Det innebär för att fortsätta använda SQL VM-resursprovidern, behöver du följande:
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- [Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A [SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrerats hos den [SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) installerad. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Ändra licensen för virtuella datorer som redan har registrerats hos resursprovidern 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra så att licensieringsmodellen direkt från portalen. 

1. Öppna den [Azure-portalen](https://portal.azure.com) och starta den [SQL VM-resurs](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) för din SQL Server-VM. 
1. Välj **konfigurera** under **inställningar**. 
1. Välj den **Azure Hybrid-förmånen** och välja kryssrutan för att bekräfta att du har en SQL Server-licens med Software Assurance. 
1. Välj **tillämpa** längst ned på den **konfigurera** sidan. 

![AHB i portalen](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Du kan använda Azure CLI för att ändra licensieringsmodellen.  

Följande kodavsnitt växlar modellen betala per licens till BYOL (eller med hjälp av Azure Hybrid-förmånen):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Följande kodavsnitt växlar bring-your-own-license-modell till betala per användning: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Du kan använda PowerShell för att ändra licensieringsmodellen.

Följande kodavsnitt växlar modellen betala per licens till BYOL (eller med hjälp av Azure Hybrid-förmånen):

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

Följande kodavsnitt växlar BYOL-modell till betala per användning:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Ändra licensen för virtuella datorer som inte är registrerad hos resursprovidern

Om du har etablerat en SQL Server-VM från PAYG Azure Marketplace-avbildningar vara PAYG licenstyp för SQL. Om du har etablerat en SQL Server VM med en BYOL-avbildning från Azure Marketplace kommer vara licenstypen AHUB. Alla virtuella SQLServer-datorer som etablerats från standard (PAYG) eller BYOL Azure Marketplace-avbildningar registreras automatiskt med SQL VM-resursprovidern så att de kan ändra den [licenstyp](#change-license-for-vms-already-registered-with-resource-provider)

Du bara är berättigade att själv installera SQL Server på virtuella Azure-datorer via Azure Hybrid-förmånen och bör du [registrera dessa virtuella datorer med SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) genom att ange SQL Server-licens som AHB för att indikera AHB användningen enligt Villkor för Microsoft-produkt.

Du kan bara ändra licenstypen för en SQL Server VM som PAYG eller AHB om SQL-VM är registrerad med SQL VM-resursprovidern; och alla virtuella SQL-datorer ska registreras med SQL VM RP för Licenskontroll.

## <a name="remarks"></a>Kommentarer

 - Azure Cloud Solution Partner (CSP)-kunder kan använda Azure Hybrid-förmånen genom att först distribuera en betala per virtuell dator och sedan konvertera den till bring-your-own-license om de har aktiva SA.
 - Om du släpper din SQL Server-VM-resurs ska du gå tillbaka till inställningen hårdkodade licens för avbildningen. 
  - Ändra så att licensieringsmodellen är en funktion i SQL VM-resursprovidern. Distribuera en marketplace-avbildning via Azure portal automatiskt registrerar en SQL Server VM med resursprovidern. Men kunder som lokal installation av SQL Server måste du manuellt [registrera sina SQL Server-VM](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Lägga till en SQL Server VM till en tillgänglighetsuppsättning måste återskapa den virtuella datorn. Som sådana, alla virtuella datorer som har lagts till i en tillgänglighetsuppsättning set går tillbaka till typ av licens för betala per användning och AHB måste aktiveras igen. 


## <a name="limitations"></a>Begränsningar

 - Ändra så att licensieringsmodellen är endast tillgängligt för kunder med software assurance.
 - Ändra så att licensieringsmodellen stöds endast för standard- och enterprise-utgåva av SQL Server. För Express-, webb- och Developer licensändringar som inte stöds. 
 - Ändra så att licensieringsmodellen stöds bara för virtuella datorer som distribueras med hjälp av Resource Manager-modellen. Virtuella datorer som distribueras med den klassiska modellen stöds inte. 
 - Ändra så att licensieringsmodellen aktiveras endast för offentliga moln-installationer.
 - Ändra så att licensieringsmodellen stöds bara på virtuella datorer som har ett enda nätverkskort (nätverksgränssnitt). På virtuella datorer som har mer än ett NIC, du bör först ta bort ett nätverkskort (med hjälp av Azure-portalen) innan du försöker proceduren. I annat fall körs i ett fel som liknar följande: `The virtual machine '\<vmname\>' has more than one NIC associated.` Även om du kan lägga till nätverkskortet tillbaka till den virtuella datorn när du har ändrat licensieringsalternativ, åtgärder via den SQL-konfigurationssidan i Azure-portalen, som automatisk uppdatering och säkerhetskopiering, inte längre betraktas som stöds.


## <a name="known-errors"></a>Kända fel

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Resursen ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resursgrupp > ”i resursgruppen”\<resursgrupp >' hittades inte. Egenskapen 'sqlServerLicenseType' kan inte hittas på det här objektet. Kontrollera att egenskapen finns och kan ställas in.
Det här felet uppstår vid försök att ändra licensieringsmodell på en SQL Server VM som inte har registrerats med SQL VM-resursprovidern. Du måste registrera resursprovidern till din [prenumeration](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), och sedan registrera SQL Server-dator med SQL [resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Det går inte att verifiera argumentet i parametern ”Sku”
Det här felet kan uppstå när du försöker ändra licensieringsmodellen för SQL Server-dator när du använder Azure PowerShell > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Lös felet genom att ta bort kommentarerna raderna i kodfragmentet tidigare nämnda PowerShell när du växlar licensieringsmodellen:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Använd följande kod för att verifiera Azure PowerShell-version:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer på en Windows VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server på en Windows-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en Windows VM viktig information](virtual-machines-windows-sql-server-iaas-release-notes.md)


