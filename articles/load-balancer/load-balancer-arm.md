---
title: Azure Resource Manager-stöd för Load Balancer | Microsoft Docs
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
ms.openlocfilehash: 43db9db3842d05fa13c3be92df14b905d2ddfc17
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055558"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Med stöd för Azure Resource Manager med Azure-belastningsutjämnare



Azure Resource Manager är det önskade ramverket för tjänster i Azure. Azure Load Balancer kan hanteras med hjälp av Azure Resource Manager-baserade API: er och verktyg.

## <a name="concepts"></a>Begrepp

Med Resource Manager kan Azure Load Balancer innehåller följande underordnade resurser:

* Frontend IP-konfiguration – en belastningsutjämnare kan innehålla en eller flera frontend IP-adresser, även känt som en virtuell IP-adresser (VIP). Dessa IP-adresser fungerar som ingresser för trafiken.
* Backend adresspoolen – det här är IP-adresser som är associerade med det virtuella datorn Standardnätverksgränssnittet kort (nätverkskortet) som belastningen distribueras.
* Belastningsutjämningsregler – en regel för egenskapen mappar en given frontend-IP och portkombination till en uppsättning med backend-IP-adresser och portkombinationer. En enskild lastbalanserare kan ha flera regler för lastbalansering. Varje regel är en kombination av en frontend-IP och port och backend-IP och port som associeras med virtuella datorer.
* Avsökningar – avsökningar kan du hålla koll på hälsotillståndet för VM-instanser. Om en hälsoavsökning misslyckas, tas den Virtuella datorinstansen automatiskt bort från roteringen.
* Ingående NAT-regler – NAT-regler som definierar den inkommande trafik som flödar genom frontend-IP och distribueras till backend-IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Snabbstartsmallar

Med Azure Resource Manager kan du etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.

Mallar kan innehålla definitioner för virtuella datorer, virtuella nätverk, Tillgänglighetsuppsättningar, nätverksgränssnitt (NIC), Lagringskonton, belastningsutjämnare, Nätverkssäkerhetsgrupper och offentliga IP-adresser. Du kan skapa allt du behöver för ett komplexa program med mallar. Mallfilen kan checkas in innehållshanteringssystem för versionskontroll och samarbete.

[Mer information om mallar](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Mer information om nätverksresurser](../networking/networking-overview.md)

Snabbstartsmallar som använder Azure Load Balancer, finns det [GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates) som är värd för en uppsättning mallar som communityt som genereras.

Exempel på mallar:

* [2 virtuella datorer i Load Balancer och belastningsutjämningsregler](http://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuella datorer i ett virtuellt nätverk med en intern belastningsutjämnare och Load Balancer-regler](http://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuella datorer i en belastningsutjämnare och konfigurera NAT-regler på LB](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Konfigurera Azure Load Balancer med en PowerShell eller CLI

Kom igång med Azure Resource Manager-cmdletar, kommandoradsverktyg och REST API: er

* [Azure Networking cmdletar](https://docs.microsoft.com/powershell/module/azurerm.network#networking) kan användas för att skapa en belastningsutjämnare.
* [Så här skapar du en belastningsutjämnare med hjälp av Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Med hjälp av Azure CLI med Azure Resource Manager](../xplat-cli-azure-resource-manager.md)
* [Belastningsutjämnare REST API: er](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Nästa steg

Du kan också [komma igång och skapa en belastningsutjämnare mot Internet](load-balancer-get-started-internet-arm-ps.md) och konfigurerar typen av [Distributionsläge](load-balancer-distribution-mode.md) för en specifik belastningsutjämnarens nätverk trafik beteende.

Lär dig hur du hanterar [inaktiv TCP timeout-inställningar för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md). Detta är viktigt när ditt program kräver aktiva anslutningar alive för servrar bakom en belastningsutjämnare.
