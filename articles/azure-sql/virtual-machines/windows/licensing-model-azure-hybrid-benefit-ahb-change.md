---
title: Ändra licens modell för en virtuell SQL-dator i Azure
description: Lär dig hur du växlar licensiering för en SQL Server VM i Azure från betala per användning för att få en egen licens genom att använda Azure Hybrid-förmån.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f02f31e0fc8943682af77ca6f506d15f36e88146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668910"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Ändra licensmodellen för en virtuell SQL-dator i Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Den här artikeln beskriver hur du ändrar licens modellen för en SQL Server virtuell dator (VM) i Azure med hjälp av den nya SQL Server VM Resource providern **Microsoft. SqlVirtualMachine**.

Det finns tre licens modeller för en virtuell dator som är värd för SQL Server: betala per användning, Azure Hybrid-förmån (AHB) och haveri beredskap (DR). Du kan ändra licens modellen för dina SQL Server VM med hjälp av Azure Portal, Azure CLI eller PowerShell. 

- Modellen " **betala per** användning" innebär att kostnaden för den virtuella Azure-datorn per sekund inkluderar kostnaden för den SQL Server licensen.
- [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) gör att du kan använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. 
- Licens typen för **haveri beredskap** används för den [kostnads fria Dr-repliken](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) i Azure. 

Azure Hybrid-förmån tillåter användningen av SQL Server licenser med Software Assurance ("kvalificerad licens") på virtuella Azure-datorer. Med Azure Hybrid-förmån debiteras kunderna inte för att använda en SQL Server-licens på en virtuell dator. Men de måste fortfarande betala för kostnaden för den underliggande moln beräkningen (det vill säga bas priset), lagringen och säkerhets kopieringarna. De måste också betala för I/O som är kopplade till deras användning av tjänsterna (om det är tillämpligt).

Enligt Microsofts produkt villkor: "kunder måste ange att de använder Azure SQL Database (hanterad instans, Elastisk pool och Enkel databas), Azure Data Factory, SQL Server Integration Services eller SQL Server Virtual Machines under Azure Hybrid-förmån för SQL Server när du konfigurerar arbets belastningar i Azure."

Om du vill ange användningen av Azure Hybrid-förmån för SQL Server på virtuell Azure-dator och är kompatibel, har du tre alternativ:

- Etablera en virtuell dator med hjälp av en egen licens SQL Server-avbildning från Azure Marketplace. Det här alternativet är endast tillgängligt för kunder som har en Enterprise-avtal.
- Etablera en virtuell dator med hjälp av en SQL Server avbildning som du betalar per användning från Azure Marketplace och aktivera Azure Hybrid-förmån.
- Själv installera SQL Server på den virtuella Azure-datorn, [Registrera dig manuellt med resurs leverantören för SQL-VM](sql-vm-resource-provider-register.md)och aktivera Azure Hybrid-förmån.

Licens typen för SQL Server kan konfigureras när den virtuella datorn är etablerad eller när som helst efteråt. Växling mellan licens modeller medför ingen nedtid, startar inte om den virtuella datorn eller tjänsten SQL Server, lägger inte till några ytterligare kostnader och börjar gälla omedelbart. Aktiverings Azure Hybrid-förmån *minskar* i själva verket kostnaderna.

## <a name="prerequisites"></a>Krav

Att ändra licensierings modellen för SQL Server VM har följande krav: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrerad med [resurs leverantören för SQL-VM](sql-vm-resource-provider-register.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) är ett krav för att använda [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Virtuella datorer som redan har registrerats med resurs leverantören 

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra licens modellen direkt från portalen: 

1. Öppna [Azure Portal](https://portal.azure.com) och öppna [resursen SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) för din SQL Server VM. 
1. Välj **Konfigurera** under **Inställningar**. 
1. Välj alternativet **Azure Hybrid-förmån** och markera kryss rutan för att bekräfta att du har en SQL Server-licens med Software Assurance. 
1. Välj **Använd** längst ned på sidan **Konfigurera** . 

![Azure Hybrid-förmån i portalen](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan använda Azure CLI för att ändra din licens modell.  


**Azure Hybrid-förmån**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Betala per**användning: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Haveriberedskap**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda PowerShell för att ändra din licens modell.

**Azure Hybrid-förmån**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Betala per användning**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Haveriberedskap** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Virtuella datorer som inte är registrerade hos resurs leverantören

Om du etablerade en SQL Server VM från Azure Marketplace-avbildningar enligt principen betala per användning, kommer SQL Server licens typen att betala per användning. Om du har upprättat en SQL Server VM med hjälp av en egen licens avbildning från Azure Marketplace blir licens typen AHUB. Alla SQL Server virtuella datorer som tillhandahålls från standardvärdet (betala per användning) eller med en egen licens för Azure Marketplace-avbildningar registreras automatiskt med den virtuella SQL-resurs leverantören, så att de kan ändra [licens typen](#vms-already-registered-with-the-resource-provider).

Du är endast berättigad till att själv installera SQL Server på virtuella Azure-datorer via Azure Hybrid-förmån. Du bör [registrera de virtuella datorerna med resurs leverantören för SQL-VM](sql-vm-resource-provider-register.md) genom att ange den SQL Server licensen som Azure Hybrid-förmån, för att ange Azure Hybrid-förmån användning enligt Microsofts produkt villkor.

Du kan ändra licens typen för en SQL Server VM som "betala per användning" eller Azure Hybrid-förmån endast om SQL Server VM har registrerats med resurs leverantören för SQL-VM.

## <a name="remarks"></a>Kommentarer

- Azure Cloud Solution Provider (CSP)-kunder kan använda Azure Hybrid-förmån genom att först distribuera en virtuell dator där du betalar per användning och sedan konvertera den till en egen licens om de har en aktiv Software Assurance.
- Om du tar bort din SQL Server VM-resurs går du tillbaka till avbildningens hårdkodade licens inställningar. 
- Möjligheten att ändra licens modellen är en funktion i SQL VM Resource Provider. Genom att distribuera en Azure Marketplace-avbildning via Azure Portal registreras automatiskt en SQL Server VM med resurs leverantören. Men kunder som själv installerar SQL Server måste [registrera sina SQL Server VM](sql-vm-resource-provider-register.md)manuellt. 
- Om du lägger till en SQL Server VM i en tillgänglighets uppsättning måste den virtuella datorn skapas på nytt. Det innebär att alla virtuella datorer som läggs till i en tillgänglighets uppsättning återgår till standard licens typen betala per användning. Azure Hybrid-förmån måste aktive ras igen. 


## <a name="limitations"></a>Begränsningar

Att ändra licens modellen är:
   - Endast tillgängligt för kunder med [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Stöds endast för standard-och Enterprise-utgåvorna av SQL Server. Licens ändringar för Express, webb och utvecklare stöds inte. 
   - Stöds endast för virtuella datorer som distribueras via Azure Resource Managers modellen. Virtuella datorer som distribueras via den klassiska modellen stöds inte. 
   - Endast tillgängligt för offentliga eller Azure Government moln. 
   - Stöds endast på virtuella datorer som har ett enda nätverks gränssnitt (NIC). 


## <a name="known-errors"></a>Kända fel

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Det gick inte att hitta resursen Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> under resurs gruppen \<resource-group> .

Det här felet uppstår när du försöker ändra licens modellen på en SQL Server VM som inte har registrerats med SQL VM-resurs leverantören:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Du måste registrera din prenumeration med resurs leverantören och sedan [registrera SQL Server VM med resurs leverantören](sql-vm-resource-provider-register.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Den virtuella datorn \<vmname\> har mer än ett nätverkskort kopplat

Det här felet uppstår på virtuella datorer som har mer än ett nätverkskort. Ta bort ett nätverkskort innan du ändrar licensierings modellen. Även om du kan lägga till NIC-kortet på den virtuella datorn efter att du har ändrat licens modellen, stöds inte längre åtgärder i Azure Portal, till exempel automatisk säkerhets kopiering och korrigering. 


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](../../database/doc-changes-updates-release-notes.md)


