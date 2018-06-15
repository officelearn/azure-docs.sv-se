---
title: Azure Resource Manager-stöd för belastningsutjämnaren | Microsoft Docs
description: Med hjälp av powershell för belastningsutjämnaren med Azure Resource Manager. Med hjälp av mallar för belastningsutjämnare
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: d0394f11-ee5a-4407-9d86-79c936297265
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 599c016763fde6f1dc8221fffa554cf68e8c498f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790397"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Med Azure belastningsutjämnare Azure Resource Manager-stöd



Azure Resource Manager är prioriterade Hanteringsramen för tjänster i Azure. Azure belastningsutjämnare kan hanteras med Azure Resource Manager-baserade API: er och verktyg.

## <a name="concepts"></a>Begrepp

Med Resource Manager Azure belastningsutjämnare innehåller följande underordnade resurser:

* Frontend IP-konfiguration – en belastningsutjämnare kan innehålla en eller flera klientdelens IP-adresser, kallas även för en virtuell IP-adresser (VIP). Dessa IP-adresser fungerar som ingresser för trafiken.
* Backend-adresspool – det här är IP-adresser som är associerade med den virtuella nätverk gränssnittet Card (NIC) som belastningen distribueras.
* Regler för belastningsutjämning – en regel för egenskapen mappar en given klientdels-IP och port-kombination till en uppsättning backend IP-adresser och port-kombination. En enskild belastningsutjämnare kan ha flera regler för belastningsutjämning. Varje regel är en kombination av en klientdel IP och port och serverdels-IP och port som associeras med virtuella datorer.
* Avsökningar – avsökningar gör att du kan hålla reda på hälsotillståndet för VM-instanser. Om en hälsoavsökningen misslyckas, är VM-instans tas bort från rotationen automatiskt.
* Inkommande NAT-regler – NAT-regler som definierar den inkommande trafik som passerar genom klientdels-IP och distribueras till backend-IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Snabbstartsmallar

Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.

Mallar kan innehålla definitioner för virtuella datorer, virtuella nätverk, Tillgänglighetsuppsättningar, nätverksgränssnitt (NIC), Storage-konton, belastningsutjämnare, Nätverkssäkerhetsgrupper och offentliga IP-adresser. Mallar kan skapa du allt du behöver för komplexa program. Mallfilen kan kontrolleras till innehållshantering system för versionskontroll och samarbete.

[Mer information om mallar](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Mer information om nätverksresurser](../networking/networking-overview.md)

Snabbstartsmallar med hjälp av Azure belastningsutjämnare, finns det [GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates) som är värd för en uppsättning community genereras mallar.

Exempel på mallar:

* [2 virtuella datorer i en belastningsutjämnare och belastningsutjämningsregler](http://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuella datorer i ett VNET med en intern belastningsutjämnare och belastningsutjämnare regler](http://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuella datorer i en belastningsutjämnare och konfigurera NAT-regler på LB](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Konfigurera Azure belastningsutjämnare med PowerShell eller CLI

Kom igång med Azure Resource Manager cmdlets, kommandoradsverktyg och REST API: er

* [Azure-nätverk Cmdlets](https://msdn.microsoft.com/library/azure/mt163510.aspx) kan användas för att skapa en belastningsutjämnare.
* [Så här skapar du en belastningsutjämnare med Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Använda Azure CLI med Azure Resource Manager](../xplat-cli-azure-resource-manager.md)
* [Belastningsutjämnaren REST API: er](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Nästa steg

Du kan också [komma igång med en Internetuppkopplad belastningsutjämnaren](load-balancer-get-started-internet-arm-ps.md) och konfigurera vilken typ av [distribution läge](load-balancer-distribution-mode.md) för en särskild belastningsutjämnare trafik nätverksproblem.

Lär dig att hantera [inaktiv TCP timeout-inställningar för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md). Detta är viktigt när programmet måste keep alive för servrar bakom en belastningsutjämnare för anslutningar.
