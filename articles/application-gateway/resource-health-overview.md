---
title: Hälsoöversikt över Azure Application Gateway Resource
description: Den här artikeln är en översikt över resurshälsofunktionen för Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659507"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Hälsoöversikt över Azure Application Gateway Resource

[Azure Resource Health](../service-health/resource-health-overview.md) hjälper dig att diagnostisera och lösa problem med Azure-tjänster som påverkar dina resurser. Det informerar dig om den nuvarande och tidigare hälsa dina resurser. Och det ger teknisk support som hjälper dig att minska problem.

För Application Gateway är Resource Health beroende av signaler som skickas av gatewayen för att bedöma om det är felfritt eller inte. Om gatewayen är felfritt analyserar Resource Health ytterligare information för att fastställa orsaken till problemet. Den identifierar också åtgärder som Microsoft vidtar eller vad du kan göra för att åtgärda problemet.

Mer information om hur hälsotillstånd bedöms finns i den fullständiga listan över resurstyper och hälsokontroller i [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Hälsostatusen för Application Gateway visas som en av följande status:

## <a name="available"></a>Tillgängligt

En **tillgänglig** status innebär att tjänsten inte har upptäckt några händelser som påverkar resursens hälsotillstånd. Du ser nyligen **löst** meddelande i fall där gatewayen har återställts från oplanerade driftstopp under de senaste 24 timmarna.

![Tillgänglig hälsostatus](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Inte tillgänglig

En **status som inte är tillgänglig** innebär att tjänsten har upptäckt en pågående plattform eller icke-plattformshändelse som påverkar gatewayens hälsa.

### <a name="platform-events"></a>Plattformsevenemang

Plattformshändelser utlöses av flera komponenter i Azure-infrastrukturen. De omfattar både schemalagda åtgärder (till exempel planerat underhåll) och oväntade incidenter (till exempel en oplanerad värdomstart).

Resource Health innehåller ytterligare information om händelsen och återställningsprocessen. Du kan också kontakta supporten även om du inte har ett aktivt Microsoft-supportavtal.

![Status som inte är tillgänglig](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Okänt

Den **okända** hälsostatusen anger att Resource Health inte har fått information om gatewayen på mer än 10 minuter. Den här statusen är inte en definitiv indikation på gatewayens tillstånd. Men det är en viktig datapunkt i felsökningsprocessen.

Om gatewayen körs som förväntat ändras statusen till **Tillgänglig** efter några minuter.

Om du har problem kan **okänd** hälsostatus tyna på att en händelse på plattformen påverkar gatewayen.

![Okänd status](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degraderad

**Hälsotillståndet Försämrad** anger att gatewayen har upptäckt en prestandaförlust, även om den fortfarande är tillgänglig för användning.

![Inaktiverad status](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Nästa steg

Mer information om felsökning av brandvägg för webbprogram för programgateway (WAF) finns i [Felsöka brandvägg för webbprogram (WAF) för Azure Application Gateway](web-application-firewall-troubleshoot.md).