---
title: Azure Resource Manager stöd för Load Balancer
description: I den här artikeln använder du Azure PowerShell och mallar med Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: b22b89334fbb55e594ac2b27b486cf5d0bd26f03
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196110"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager stöd med Azure Load Balancer



Azure Resource Manager är det prioriterade hanterings ramverket för tjänster i Azure. Azure Load Balancer kan hanteras med Azure Resource Manager-baserade API: er och verktyg.

## <a name="concepts"></a>Begrepp

Med Resource Manager innehåller Azure Load Balancer följande underordnade resurser:

* IP-konfiguration på klient sidan – en belastningsutjämnare kan innehålla en eller flera klient dels-IP-adresser, annars kallas virtuella IP-adresser (VIP). Dessa IP-adresser fungerar som ingresser för trafiken.
* Backend-adresspool – den här poolen är en samling IP-adresser som är kopplade till den virtuella datorns nätverks gränssnitts kort (NIC) till vilken belastningen distribueras.
* Belastnings Utjämnings regler – en regel egenskap mappar en specifik kombination av klient dels-IP och-port till en uppsättning backend-IP-adresser och port kombination. En enda belastningsutjämnare kan ha flera regler för belastnings utjämning. Varje regel är en kombination av en klient dels-IP och port och backend-IP och port som är kopplad till virtuella datorer.
* Avsökningar – avsökningar gör att du kan hålla reda på hälso tillståndet för VM-instanser. Om en hälso avsökning Miss lyckas tas den virtuella dator instansen bort från rotationen automatiskt.
* Inkommande NAT-regler – NAT-regler som definierar den inkommande trafik som flödar genom klient delens IP-adress och distribueras till backend-IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Snabbstartsmallar

Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.

Mallar kan innehålla definitioner för:
* **Virtuella datorer**
* **Virtuella nätverk**
* **Tillgänglighetsuppsättningar**
* **Nätverks gränssnitt (NIC)**
* **Lagringskonton**
* **Lastbalanserare**
* **Nätverkssäkerhetsgrupper**
* **Offentliga IP-adresser.** 

Med mallar kan du skapa allt du behöver för ett komplext program. Mallfilen kan kontrol leras i innehålls hanterings system för versions kontroll och samarbete.

[Lär dig mer om mallar](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Läs mer om nätverks resurser](../networking/networking-overview.md)

För snabb starts mallar som använder Azure Load Balancer, se [GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates) som är värd för en uppsättning community-genererade mallar.

Exempel på mallar:

* [2 virtuella datorer i en Load Balancer och belastnings Utjämnings regler](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuella datorer i ett VNET med interna Load Balancer-och belastnings Utjämnings regler](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuella datorer i en belastningsutjämnare och konfigurera NAT-regler på LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Konfigurera Azure Load Balancer med PowerShell eller CLI

Kom igång med Azure Resource Manager cmdlets, kommando rads verktyg och REST API: er

* [Azure Networking-cmdletar](https://docs.microsoft.com/powershell/module/az.network#networking) kan användas för att skapa en Load Balancer.
* [Så här skapar du en belastningsutjämnare med hjälp av Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Använda Azure CLI med Azure Resource Management](../xplat-cli-azure-resource-manager.md)
* [Load Balancer REST-API: er](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Nästa steg

[Kom igång med att skapa en belastningsutjämnare för Internet](load-balancer-get-started-internet-arm-ps.md) och konfigurera typen av [distributions läge](load-balancer-distribution-mode.md) för vissa nätverks trafik beteenden.

Lär dig hur du hanterar [timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md). De här inställningarna är viktiga när ditt program måste hålla anslutningarna Alive för servrar bakom en belastningsutjämnare.
