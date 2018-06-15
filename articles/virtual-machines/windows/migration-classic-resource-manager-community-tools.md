---
title: Verktyg för Community - flytta klassiska resurser till Azure Resource Manager | Microsoft Docs
description: Den här artikeln katalogiserar de verktyg som har angetts av grupp för att migrera IaaS-resurser från klassiska till Azure Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: cce1906e75646f2fb9ea30842e968d14830f3497
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913944"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Community-verktyg som används för att migrera IaaS-resurser från klassiskt läge till Azure Resource Manager
Den här artikeln katalogiserar de verktyg som tillhandahålls av gemenskapen för att underlätta migreringen av IaaS-resurser från klassiska till Azure Resource Manager-distributionsmodellen.

> [!NOTE]
> Dessa verktyg officiellt stöds inte av Microsoft-supporten. Därför är öppen källkod på GitHub och vi är glada över att acceptera fso för korrigeringar eller fler scenarier. Om du vill rapportera ett problem med funktionen GitHub problem.
> 
> Migrera med de här verktygen kommer driftstopp för dina klassiska virtuella datorer. Om du letar efter migrering plattform som stöds finns 
> 
>   * [Migrering av plattform som stöds av IaaS-resurser från klassiska till Azure Resource Manager-stacken](migration-classic-resource-manager-overview.md)
>   * [Tekniska ingående om plattformen stöds migreringen från klassiskt till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Detta är en samling helper-verktyg som skapats som en del av enterprise migrering från Azure-tjänsthantering till Azure Resource Manager. Det här verktyget kan du replikera infrastrukturen till en annan prenumeration som kan användas för att testa migrering och järn eventuella problem innan du kör migreringen av prenumerationen produktion.

[Länka till verktyget-dokumentation](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz är ytterligare en möjlighet att migrera en fullständig uppsättning klassiska IaaS-resurser till Azure Resource Manager IaaS-resurser. Migreringen kan ske inom samma prenumeration eller mellan olika prenumerationer och prenumerationstyper (ex: CSP prenumerationer).

[Länka till verktyget-dokumentation](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformar som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använda CLI för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

