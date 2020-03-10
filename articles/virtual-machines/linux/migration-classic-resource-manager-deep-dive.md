---
title: Klassisk teknik för Azure Resource Manager migrering av tekniska djup
description: Teknisk djupgående om migrering av plattformar som stöds av resurser från den klassiska distributions modellen till Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: a5277e23d92dd026aa19e278532869747709e646
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944737"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Teknisk djupgående om migrering av plattformar som stöds från klassisk till Azure Resource Manager

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Låt oss ta en titt på migreringen från den klassiska distributions modellen i Azure till Azure Resource Manager distributions modell. Vi tittar på resurser på en resurs-och funktions nivå för att hjälpa dig att förstå hur Azure-plattformen migrerar resurser mellan de två distributions modellerna. Mer information finns i artikeln om service meddelande: [migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Nästa steg

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
