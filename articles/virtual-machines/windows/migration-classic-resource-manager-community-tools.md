---
title: Community-verktyg – flytta klassiska resurser till Azure Resource Manager | Microsoft Docs
description: Den här artikeln innehåller en förteckning över de verktyg som har tillhandahållits av communityn för att migrera IaaS-resurser från klassisk till Azure Resource Manager distributions modellen.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 2b5d218812878db6fb110c043afffd2f9a2d344d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102664"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Community-verktyg som används för att migrera IaaS-resurser från klassiskt läge till Azure Resource Manager
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
migAz är ett ytterligare alternativ för att migrera en fullständig uppsättning klassiska IaaS-resurser för att Azure Resource Manager IaaS-resurser. Migreringen kan ske inom samma prenumeration eller mellan olika prenumerationer och prenumerations typer (t. ex.: CSP-prenumerationer).

[Länk till verktygs dokumentationen](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Nästa steg

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

