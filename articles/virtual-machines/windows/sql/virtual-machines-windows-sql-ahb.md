---
title: Ändra licensmodellen för en VIRTUELL SQL Server i Azure
description: Lär dig hur du byter licensiering för en virtuell SQL Server-dator i Azure från användningsbaserad betalning till bring-your-own-licens med hjälp av Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201838"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Ändra licensmodellen för en virtuell SQL Server-dator i Azure
I den hÃ¤r artikeln beskrivs hur du Ã¤kt fÃ¤r Ã¤kt fÃ¤r licensmodellen fÃ¤r en virtuell SQL Server-dator (VM) i Azure med hjÃ¤s hjÃ¤r fÃ¤rsã¤ger den nya SQL VM-resursprovidern **Microsoft.SqlVirtualMachine**.

Det finns tre licensmodeller för en virtuell dator som är värd för SQL Server: pay-as-you go, Azure Hybrid Benefit och disaster recovery (DR). Du kan ändra licensmodellen för din VIRTUELLA SQL Server-dator med hjälp av Azure-portalen, Azure CLI eller PowerShell. 

- **Pay-as-you-go-modellen** innebär att kostnaden per sekund för att köra Den virtuella Azure-datorn inkluderar kostnaden för SQL Server-licensen.
- [Med Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) kan du använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. 
- Licenstypen **för haveriberedskap** används för den [kostnadsfria DR-repliken](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) i Azure. 

Azure Hybrid Benefit tillåter användning av SQL Server-licenser med Software Assurance ("Kvalificerad licens") på virtuella Azure-datorer. Med Azure Hybrid Benefit debiteras inte kunder för användning av en SQL Server-licens på en virtuell dator. Men de måste fortfarande betala för kostnaden för den underliggande molnberäkningen (det vill säga basräntan), lagring och säkerhetskopior. De måste också betala för I/O i samband med deras användning av tjänsterna (beroende på vad som är tillämpligt).

Enligt Microsofts produktvillkor: "Kunderna måste ange att de använder Azure SQL Database (hanterad instans, elastisk pool och enkel databas), Azure Data Factory, SQL Server Integration Services eller virtuella SQL Server-datorer under Azure Hybridförmån för SQL Server när du konfigurerar arbetsbelastningar på Azure."

Om du vill ange användningen av Azure Hybrid Benefit för SQL Server på en virtuell Azure-dator och vara kompatibel har du tre alternativ:

- Etablera en virtuell dator med hjälp av en SQL Server-avbildning med din egen licens från Azure Marketplace. Det här alternativet är endast tillgängligt för kunder som har ett enterprise-avtal.
- Etablera en virtuell dator med hjälp av en SQL Server-avbildning via användningsbaserad betalning från Azure Marketplace och aktivera Azure Hybrid Benefit.
- Självinstallera SQL Server på en Virtuell Azure-dator, [registrera manuellt med SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md)och aktivera Azure Hybrid Benefit.

Licenstypen för SQL Server anges när den virtuella datorn etableras. Du kan ändra det när som helst efteråt. Växla mellan licensmodeller medför inga driftstopp, startar inte om den virtuella datorn eller SQL Server-tjänsten, lägger inte till några extra kostnader och träder i kraft omedelbart. Faktum är att aktivera Azure Hybrid Benefit *minskar* kostnaden.

## <a name="prerequisites"></a>Krav

Att ändra licensieringsmodellen för den virtuella virtuella SQL Server-datorn har följande krav: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [VIRTUELL SQL Server som](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrerats hos SQL [VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) är ett krav för att använda [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Virtuella datorer som redan är registrerade hos resursleverantören 

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra licensmodellen direkt från portalen: 

1. Öppna [Azure-portalen](https://portal.azure.com) och öppna [SQL-resursen](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) för virtuella datorer för din VIRTUELLA SQL Server-dator. 
1. Välj **Konfigurera** under **Inställningar**. 
1. Markera alternativet **Azure Hybrid Benefit** och markera kryssrutan för att bekräfta att du har en SQL Server-licens med Software Assurance. 
1. Välj **Använd** längst ned på sidan **Konfigurera.** 

![Azure Hybrid-förmån i portalen](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan använda Azure CLI för att ändra din licensmodell.  


**Azure hybrid fördel**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Betala allteftersom:** 

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

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Du kan använda PowerShell för att ändra din licensmodell.

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

**Katastrofåterställning** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Virtuella datorer som inte är registrerade hos resursleverantören

Om du har etablerat en virtuell SQL Server-dator från Azure Marketplace-avbildningar som du har, blir sql server-licenstypen användningsbaserad betalning. Om du har etablerat en virtuell SQL Server-dator med hjälp av en avbildning av din egen licens från Azure Marketplace blir licenstypen AHUB. Alla virtuella SQL Server-datorer som etablerats från standard (betala per användning) eller Azure Marketplace-avbildningar för din egen licens registreras automatiskt hos SQL VM-resursprovidern, så att de kan ändra [licenstypen](#vms-already-registered-with-the-resource-provider).

Du är endast kvalificerad att själv installera SQL Server på en Azure VM via Azure Hybrid Benefit. Du bör [registrera dessa virtuella datorer med SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md) genom att ange SQL Server-licensen som Azure Hybrid Benefit för att ange Azure Hybrid Benefit-användningen enligt Microsofts produktvillkor.

Du kan ändra licenstypen för en VIRTUELL SQL Server som användningsbaserad betalning eller Azure Hybrid-förmån endast om SQL Server-VM är registrerad hos SQL VM-resursprovidern.

## <a name="remarks"></a>Anmärkningar

- Azure Cloud Solution Provider (CSP) kunder kan använda Azure Hybrid Benefit genom att först distribuera en pay-as-you-go VM och sedan konvertera den till bring-your-own-licens, om de har aktiva Software Assurance.
- Om du släpper din SQL Server VM-resurs går du tillbaka till den hårdkodade licensinställningen för avbildningen. 
- Möjligheten att ändra licensmodellen är en funktion i SQL VM-resursprovidern. Distribuera en Azure Marketplace-avbildning via Azure-portalen registrerar automatiskt en VIRTUELL SQL Server med resursleverantören. Men kunder som själv installerar SQL Server måste manuellt [registrera sin SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Om du lägger till en virtuell SQL Server-dator i en tillgänglighetsuppsättning måste den virtuella datorn skapas om. Därför går alla virtuella datorer som läggs till i en tillgänglighetsuppsättning tillbaka till standardlicenstypen för användningsbaserad betalning. Azure Hybrid Benefit måste aktiveras igen. 


## <a name="limitations"></a>Begränsningar

Att ändra licensmodellen är:
   - Endast tillgängligt för kunder med [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Stöds endast för standard- och företagsutgåvorna av SQL Server. Licensändringar för Express, Webb och Utvecklare stöds inte. 
   - Stöds endast för virtuella datorer som distribueras via Azure Resource Manager-modellen. Virtuella datorer som distribueras via den klassiska modellen stöds inte. 
   - Endast tillgängligt för moln för offentliga myndigheter eller Azure Government. 
   - Stöds endast på virtuella datorer som har ett enda nätverksgränssnitt (NIC). 


## <a name="known-errors"></a>Kända fel

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Resursen "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>" under resursgruppens\<> hittades inte.

Det här felet uppstår när du försöker ändra licensmodellen på en VIRTUELL SQL Server-dator som inte har registrerats hos SQL VM-resursprovidern:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Du måste registrera prenumerationen hos resursleverantören och sedan [registrera din VIRTUELLA SQL Server-dator med resursleverantören](virtual-machines-windows-sql-register-with-resource-provider.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Den virtuella\<datorns\>vmname har mer än ett nätverkskort associerat

Det här felet uppstår på virtuella datorer som har mer än ett nätverkskort. Ta bort en av nätverkskorten innan du ändrar licensieringsmodellen. Även om du kan lägga till nätverkskortet tillbaka till den virtuella datorn när du har ändrat licensmodellen, kommer åtgärder i Azure-portalen, till exempel automatisk säkerhetskopiering och korrigering, inte längre att stödjas. 


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prisvägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)


