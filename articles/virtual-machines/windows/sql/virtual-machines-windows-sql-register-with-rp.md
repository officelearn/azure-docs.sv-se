---
title: Registrera SQL Server-dator i Azure med SQL VM-resursprovidern | Microsoft Docs
description: Registrera din SQL Server-dator med SQL VM-resursprovidern så att förbättra hanterbarhet.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786738"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrera SQL Server-dator i Azure med SQL VM-resursprovidern

Den här artikeln beskriver hur du registrerar din Azure SQL Server-dator (VM) med SQL VM-resursprovidern. 

Distribuera en marketplace-avbildning för SQL Server-dator via Azure portal automatiskt registrerar SQL Server-dator med resursprovidern. Men om du väljer att installera lokal SQL Server på en Azure-dator i stället för att välja en avbildning från Azure Marketplace, ska du registrera din SQL Server-VM med resursprovidern i dag, för:

-  **Efterlevnad** – enligt villkoren för Microsoft-produkter, kunder som använder den [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) måste uppge till Microsoft när du använder Azure Hybrid-förmånen – och du vill göra det de måste registrera med SQL VM-resursprovidern. 

-  **Funktionen fördelar** -registrerar din SQL Server-VM med resursprovidern låser upp [automatisk uppdatering](virtual-machines-windows-sql-automated-patching.md), [automatisk säkerhetskopiering](virtual-machines-windows-sql-automated-backup-v2.md), funktioner för övervakning och hanterbarhet, samt [licensiering](virtual-machines-windows-sql-ahb.md) och [edition](virtual-machines-windows-sql-change-edition.md) flexibilitet. Tidigare var dessa bara tillgängliga för SQL Server-VM-avbildningar från Azure Marketplace.

Lokal installation av SQL Server på en Azure virtuell dator eller etablera en virtuell Azure-dator från en anpassad virtuell Hårddisk med SQL Server är kompatibel med Azure Hybrid-förmånen för SQL Server med villkoret att kunder anger som använder till Microsoft genom att registrera med SQL VM-resurs providern. 

Om du vill använda SQL VM-resursprovidern, måste du även registrera SQL VM-resursprovidern med din prenumeration. Detta kan åstadkommas med Azure-portalen, Azure CLI och PowerShell. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill registrera din SQL Server-VM med resursprovidern, behöver du följande: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [SQLServer-dator](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) och [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Registrera med SQL VM-resursprovidern
Om den [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md) redan är installerad på den virtuella datorn och sedan registrera med SQL VM-resursprovidern är helt enkelt skapa en metadataresurs av typen Microsoft.SqlVirtualMachine/SqlVirtualMachines. Nedan visas i kodfragmentet att registrera med SQL VM-resursprovidern om SQL IaaS-tillägget har redan installerats på den virtuella datorn. Du måste ange typ av SQL Server-licens som önskas när du registrerar med SQL VM-resursprovidern som antingen ”PAYG ' eller 'AHUB'. 

Registrera SQL Server-dator med hjälp av PowerShell med följande kodavsnitt:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

Om SQL IaaS-tillägget inte är installerad på den virtuella datorn, kan sedan du registrera med SQL VM-resursprovidern genom att ange SQL-läge för enkel hantering. I lightweight SQL hanteringsläge SQL VM-resursprovidern blir automatiskt installera SQL IaaS-tillägget i [enkelt läge](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) och verifiera SQL Server instance metadata; detta tar inte starta om SQL Server-tjänsten. Du måste ange typ av SQL Server-licens som önskas när du registrerar med SQL VM-resursprovidern som antingen ”PAYG ' eller 'AHUB'. 

Registrera SQL Server-VM i lightweight SQL management-läge med hjälp av PowerShell med följande kodavsnitt:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
Registrera med SQL VM-resursprovidern i [enkelt läge](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) kommer garantera kompatibilitet och aktivera flexibla licensiering samt uppdateringar för SQL Server-versionen på plats. Instanser för redundanskluster och flera instanser distributioner kan registreras med SQL VM-resursprovidern endast i lightweight-läge. Du kan följa anvisningarna som finns på Azure portal för att uppgradera till [standardläge](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) och aktivera omfattande hanterbarhet funktionsuppsättningen med SQL Server startas om när som helst. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registrera SQL Server 2008/R2 på Windows Server 2008-datorer

SQL Server 2008 och 2008 R2 som installerats på Windows Server 2008 kan registreras med SQL-VM resursproviderns i den [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) läge. Det här alternativet garanterar att och tillåter SQL Server-dator som ska övervakas i Azure portal med begränsade funktioner.

I följande tabell beskrivs de godkända värdena för parametrarna som angavs under registreringen:

| Parameter | Godkända värden                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, eller `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` eller `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Använd följande Powershell-kodavsnitt för att registrera din SQL Server 2008 eller 2008 R2 på Windows Server 2008-instans:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>Kontrollera registreringsstatus
Du kan kontrollera om din SQL Server redan har registrerats med resursprovidern SQL VM med Azure-portalen, Azure CLI eller PowerShell. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Du kan kontrollera status för registrering med Azure portal genom att göra följande.

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till din [virtuella datorer med SQL](virtual-machines-windows-sql-manage-portal.md).
1. Välj din SQL Server-VM i listan. Om din SQL Server-VM inte listas här, är det troligt att SQL Server-dator inte har registrerats hos resursprovidern SQL VM. 
1. Visa värde under `Status`. Om `Status = Succeeded`, och sedan SQL Server-dator har registrerats med SQL VM-resursprovidern. 

    ![Kontrollera status med SQL RP-registrering](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Kontrollera aktuell registreringsstatus för SQL Server-dator med kommandot AZ CLI. `ProvisioningState` Visar `Succeeded` om registreringen har lyckats. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Kontrollera aktuell registreringsstatus för SQL Server-dator med följande PowerShell-cmdlet. `ProvisioningState` Visar `Succeeded` om registreringen har lyckats. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Ett fel indikerar att SQL Server-dator inte har registrerats med resursprovidern. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrera resursprovidern för SQL VM med prenumeration 

Om du vill registrera din SQL Server-dator med SQL VM-resursprovidern, måste du registrera resursprovidern i din prenumeration. Du kan göra det med Azure portal eller Azure CLI.

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)


Följande steg ska registrera SQL VM-resursprovidern på Azure-prenumerationen med hjälp av Azure portal. 

1. Öppna Azure portal och gå till **alla tjänster**. 
1. Gå till **prenumerationer** och välj prenumerationen av intresse.  
1. På den **prenumerationer** sidan, gå till **resursprovidrar**. 
1. Typ `sql` i filtret för att få fram de SQL-relaterade resursprovidrar. 
1. Välj antingen *registrera*, *Omregistrera*, eller *avregistrera* för den **Microsoft.SqlVirtualMachine** providern beroende på din önskad åtgärd. 

   ![Ändra providern](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Följande kodavsnitt kommer registrera SQL VM-resursprovidern på Azure-prenumerationen. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Följande PowerShell-kodavsnitt registrerar resursprovidern SQL VM på Azure-prenumerationen.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Kommentarer

 - SQL VM-resursprovidern stöder endast SQL Server-datorer som distribueras med Resource Manager. SQL Server-datorer med hjälp av den klassiska modellen inte stöds. 
 - SQL VM-resursprovidern har endast stöd för SQL Server-datorer som distribuerats till det offentliga molnet. Distributioner till privat eller myndighetsmolnet, stöds inte. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer på en Windows VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server på en Windows-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en Windows VM viktig information](virtual-machines-windows-sql-server-iaas-release-notes.md)


