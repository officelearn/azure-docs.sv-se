---
title: Community-verktyg – Flytta klassiska resurser till Azure Resource Manager
description: I den här artikeln katalogiseras de verktyg som har tillhandahållits av communityn för att migrera IaaS-resurser från klassiska till Azure Resource Manager-distributionsmodellen.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 9839f411458eeb4fd071177ec8208baa94dca3a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866160"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Community-verktyg för att migrera IaaS-resurser från klassiska till Azure Resource Manager

> [!IMPORTANT]
> Idag använder cirka 90 % av virtuella IaaS virtuella datorer [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att vara helt pensionerade den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur det påverkar dig](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

I den här artikeln katalogiseras de verktyg som har tillhandahållits av communityn för att hjälpa till med migrering av IaaS-resurser från klassiska till Azure Resource Manager-distributionsmodellen.

> [!NOTE]
> Dessa verktyg stöds inte officiellt av Microsoft Support. Därför är de öppna källkod på GitHub och vi är glada att acceptera PRs för korrigeringar eller ytterligare scenarier. Om du vill rapportera ett problem använder du funktionen GitHub-problem.
> 
> Om du migrerar med dessa verktyg blir det driftstopp för den klassiska virtuella datorn. Om du letar efter migrering som stöds av plattformen kan du besöka 
> 
>   * [Plattformsstödd migrering av IaaS-resurser från Klassisk till Azure Resource Manager-stack](migration-classic-resource-manager-overview.md)
>   * [Technical Deep Dive on Platform-stödd migrering från Classic till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrera IaaS-resurser från Classic till Azure Resource Manager med Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Det här är en samling hjälpverktyg som skapats som en del av företagsmigreringar från Azure Service Management till Azure Resource Manager. Med det här verktyget kan du replikera infrastrukturen till en annan prenumeration som kan användas för att testa migrering och lösa eventuella problem innan du kör migreringen på din produktionsprenumeration.

[Länk till verktygsdokumentationen](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz är ytterligare ett alternativ för att migrera en komplett uppsättning klassiska IaaS-resurser till Azure Resource Manager IaaS-resurser. Migreringen kan ske inom samma prenumeration eller mellan olika prenumerationer och prenumerationstyper (t.ex. CSP-prenumerationer).

[Länk till verktygsdokumentationen](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Efterföljande moment

* [Översikt över plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

