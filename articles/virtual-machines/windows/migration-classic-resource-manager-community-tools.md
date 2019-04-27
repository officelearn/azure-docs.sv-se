---
title: Community-verktyg – flytta klassiska resurser till Azure Resource Manager | Microsoft Docs
description: Den här artikeln visas de verktyg som har angetts av communityn för att migrera IaaS-resurser från klassisk till Azure Resource Manager-distributionsmodellen.
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
ms.openlocfilehash: 085a0ff0eee260069d693b339521a9489df78e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848242"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Community-verktyg som används för att migrera IaaS-resurser från klassiskt läge till Azure Resource Manager
Den här artikeln visas de verktyg som har angetts av community som hjälper till med migrering av IaaS-resurser från klassisk till Azure Resource Manager-distributionsmodellen.

> [!NOTE]
> Dessa verktyg är officiellt stöds inte av Microsoft Support. Därför de finns med öppen källkod på GitHub och vi är glada över att acceptera pull-begäranden för korrigeringar eller fler scenarier. Om du vill rapportera ett problem, använder du funktionen GitHub-problem.
> 
> Migrera med hjälp av verktygen orsakar driftstopp för dina klassisk virtuell dator. Om du letar efter migreringen för plattform som stöds, gå till 
> 
>   * [Plattformen stöds migrering av IaaS-resurser från klassisk till Azure Resource Manager-stacken](migration-classic-resource-manager-overview.md)
>   * [Teknisk djupdykning i plattformen stöds migrering från klassisk till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrera IaaS-resurser från klassisk till Azure Resource Manager med hjälp av Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Det här är en samling helper-verktyg som skapats som en del av enterprise-migreringar från Azure Service Management till Azure Resource Manager. Det här verktyget kan du replikera din infrastruktur till en annan prenumeration som kan användas för att testa migrering och järn eventuella problem innan du kör migreringen av prenumerationen produktion.

[Länka till verktyget-dokumentation](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz är ytterligare en möjlighet att migrera en fullständig uppsättning klassiska IaaS-resurser till Azure Resource Manager IaaS-resurser. Migreringen kan inträffa i samma prenumeration eller mellan olika prenumerationer och typer av prenumerationer (t.ex.: CSP-prenumerationer).

[Länka till verktyget-dokumentation](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

