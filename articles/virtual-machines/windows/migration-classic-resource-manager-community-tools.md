---
title: Community-verktyg – flytta klassiska resurser till Azure Resource Manager
description: Den här artikeln innehåller en förteckning över de verktyg som har tillhandahållits av communityn för att migrera IaaS-resurser från klassisk till Azure Resource Manager distributions modellen.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 9839f411458eeb4fd071177ec8208baa94dca3a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81866160"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Community-verktyg för att migrera IaaS-resurser från klassisk till Azure Resource Manager

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Den här artikeln innehåller en förteckning över de verktyg som har tillhandahållits av communityn för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager distributions modell.

> [!NOTE]
> De här verktygen stöds inte officiellt av Microsoft Support. De är därför öppna i GitHub och vi är glada att ta emot pull för korrigeringar eller ytterligare scenarier. Om du vill rapportera ett problem använder du funktionen GitHub-problem.
> 
> Genom att migrera med dessa verktyg kan du göra nedtid för den klassiska virtuella datorn. Om du letar efter migrering av plattformar som stöds går du till 
> 
>   * [Plattform som stöds migrering av IaaS-resurser från klassisk till Azure Resource Manager stack](migration-classic-resource-manager-overview.md)
>   * [Teknisk djupgående om migrering av plattformar som stöds från klassisk till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrera IaaS-resurser från klassisk till Azure Resource Manager att använda Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Det här är en samling hjälp verktyg som skapats som en del av företagets migreringar från Azure Service Management till Azure Resource Manager. Med det här verktyget kan du replikera din infrastruktur till en annan prenumeration som kan användas för att testa migreringen och stryk eventuella problem innan du kör migreringen i produktions prenumerationen.

[Länk till verktygs dokumentationen](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz är ett ytterligare alternativ för att migrera en fullständig uppsättning klassiska IaaS-resurser för att Azure Resource Manager IaaS-resurser. Migreringen kan ske inom samma prenumeration eller mellan olika prenumerationer och prenumerations typer (t. ex. CSP-prenumerationer).

[Länk till verktygs dokumentationen](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Nästa steg

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

