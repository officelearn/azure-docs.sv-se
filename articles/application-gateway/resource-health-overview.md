---
title: Översikt över Azure Application Gateway Resource Health
description: Den här artikeln är en översikt över resource health-funktionen för Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659507"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Översikt över Azure Application Gateway Resource Health

[Azure Resource Health](../service-health/resource-health-overview.md) hjälper dig att diagnostisera och få support när ett Azure-tjänst-problem påverkar dina resurser. Informerar dig om nya och gamla hälsotillståndet för dina resurser. Och du får teknisk support för att hjälpa dig att lösa problem.

För Application Gateway förlitar sig Resource Health på signaler som genereras av gateway för att utvärdera om den är felfri eller inte. Om gatewayen är i feltillstånd, analyserar Resource Health ytterligare information för att fastställa orsaken till problemet. Den identifierar också åtgärder som Microsoft tar eller vad du kan göra för att åtgärda problemet.

För ytterligare information om hur hälsotillstånd bedöms, Läs den fullständiga listan över resurstyper och hälsokontroller i [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Hälsostatus för Application Gateway visas som en av följande statusar:

## <a name="available"></a>Tillgängligt

En **tillgänglig** status innebär att tjänsten inte har identifierats eventuella händelser som påverkar hälsotillståndet för resursen. Du ser den **nyligen löst** meddelande i fall där gatewayen har återställts från oplanerade driftstopp under de senaste 24 timmarna.

![Tillgängliga hälsostatus](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Ej tillgänglig

En **ej tillgänglig** status innebär att tjänsten har identifierat en pågående eller icke-plattformen händelse som påverkar hälsotillståndet för gatewayen.

### <a name="platform-events"></a>Plattformshändelser

Plattformshändelser har utlösts av flera komponenter i Azure-infrastrukturen. De omfattar både schemalagda åtgärder (till exempel planerat underhåll) och oväntade händelser (till exempel en omstart för oplanerad värden).

Resource Health finns ytterligare information om händelsen och återställningsprocessen. Du kan också kontakta support även om du inte har en aktiv Microsoft supportavtal.

![Status otillgänglig](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Okänt

Den **okänd** hälsostatus anger Resource Health har inte mottagit information om gatewayen för mer än 10 minuter. Denna status är inte en slutgiltig indikation på tillståndet för gatewayen. Men det är en viktig datapunkt i felsökningen.

Om gatewayen körs som förväntat, status ändras till **tillgänglig** efter ett par minuter.

Om du har problem, de **okänd** hälsostatus kan det vara att en händelse i plattformen påverkar gatewayen.

![Okänd status](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degraderad

Den **degraderad** hälsostatus anger din gateway har upptäckt en förlust i prestanda, även om det är fortfarande tillgängliga för användning.

![Degrated status](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Nästa steg

Läs om hur du felsöker Application Gateway Web Application Firewall (WAF) i [felsöka Web Application Firewall (WAF) för Azure Application Gateway](web-application-firewall-troubleshoot.md).