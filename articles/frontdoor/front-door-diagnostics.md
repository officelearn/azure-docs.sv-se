---
title: Övervakning av mått och loggar i Azures tjänst för frontend-dörr | Microsoft Docs
description: Den här artikeln beskriver de olika mått och åtkomst loggar som Azure frontend-tjänsten stöder
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 229706ff91b776363d3e9de080e02cee5edf9c77
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677900"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Övervaka mått och loggar i Azures tjänst för frontend-dörr

Genom att använda Azures tjänst för front dörr kan du övervaka resurser på följande sätt:

- **Mått**. Azures front dörr har för närvarande sju mått för att visa prestanda räknare.
- **Loggar**. Med aktivitets-och diagnostikloggar kan prestanda, åtkomst och annan information sparas eller förbrukas från en resurs i övervaknings syfte.

### <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser som gör att du kan visa prestanda räknare i portalen. Följande är tillgängliga mått på front dörren:

| Mått | Metrisk visningsnamn | Enhet | Dimensioner | Beskrivning |
| --- | --- | --- | --- | --- |
| RequestCount | Antal begäranden | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet klient förfrågningar som hanteras av frontend-dörren.  |
| RequestSize | Begär ande storlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet byte som har skickats som begär Anden från klienter till front dörren. |
| ResponseSize | Svars storlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet byte som har skickats som svar från Front dörren till klienter. |
| TotalLatency | Total svars tid | Millisekunder | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Den tid som beräknas från den klientbegäran som mottagits av front dörren tills klienten bekräftade den senaste svars byten från Front dörren. |
| BackendRequestCount | Antal Server dels begär Anden | Count | HttpStatus</br>HttpStatusGroup</br>Serverdel | Antalet förfrågningar som skickats från Front dörren till Server delar. |
| BackendRequestLatency | Svars tid för Server del | Millisekunder | Serverdel | Tiden räknat från när begäran skickades av frontend till Server delen tills front dörren fick den senaste svars byten från Server delen. |
| BackendHealthPercentage | Server delens hälso procent | Percent | Serverdel</br>BackendPool | Procent andelen lyckade hälso avsökningar från Front dörren till Server delar. |
| WebApplicationFirewallRequestCount | Antal begär Anden om webb programs brand vägg | Count | policyName</br>RuleName</br>Action | Antalet klient begär Anden som bearbetats av program nivå säkerheten för front dörren. |

## <a name="activity-log"></a>Aktivitets loggar

Aktivitets loggar innehåller information om de åtgärder som utförts i front dörrs tjänsten. De bestämmer också vad, vem och när för Skriv åtgärder (skicka, posta eller ta bort) som tas i front dörr tjänsten.

>[!NOTE]
>Aktivitets loggar innehåller inte Läs åtgärder (get). De omfattar inte heller åtgärder som du utför med hjälp av antingen Azure Portal eller ursprungligt hanterings-API.

Få åtkomst till aktivitets loggar i din frontend-tjänst eller alla loggar för dina Azure-resurser i Azure Monitor. Så här visar du aktivitetsloggar:

1. Välj din instans av frontend-dörren.
2. Välj **aktivitets logg**.

    ![Aktivitetslogg](./media/front-door-diagnostics/activity-log.png)

3. Välj en filtrerings omfattning och välj sedan **Använd**.

## <a name="diagnostic-logging"></a>Diagnostikloggar
Diagnostikloggar ger omfattande information om åtgärder och fel som är viktiga för granskning och fel sökning. Diagnostikloggar skiljer sig från aktivitets loggar.

Aktivitets loggar ger insikter om de åtgärder som utförs på Azure-resurser. Diagnostikloggar ger insikter om åtgärder som din resurs utfört. Mer information finns i [Azure Monitor diagnostikloggar](../azure-monitor/platform/resource-logs-overview.md).

![Diagnostikloggar](./media/front-door-diagnostics/diagnostic-log.png)

Så här konfigurerar du diagnostikloggar för din frontend-tjänst:

1. Välj din Azure-tjänst för front dörr.

2. Välj **diagnostikinställningar**.

3. Välj **slå på diagnostik**. Arkivera diagnostikloggar tillsammans med mått till ett lagrings konto, strömma dem till en händelsehubben eller skicka dem till Azure Monitor loggar.

Frontend-tjänsten tillhandahåller för närvarande diagnostikloggar (batch-varje timme). Diagnostikloggar tillhandahåller enskilda API-begäranden med varje post med följande schema:

| Egenskap  | Beskrivning |
| ------------- | ------------- |
| clientIp | IP-adressen för klienten som gjorde begäran. |
| clientPort | IP-porten för den klient som gjorde begäran. |
| httpMethod | HTTP-metod som används av begäran. |
| HttpStatusCode | HTTP-statuskod som returnerades från proxyservern. |
| HttpStatusDetails | Resulterande status för begäran. Innebörd av detta sträng värde finns i en status referens tabell. |
| HttpVersion | Typ av begäran eller anslutning. |
| RequestBytes | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten. |
| RequestUri | URI för den mottagna begäran. |
| ResponseBytes | Byte som skickats av backend-servern som svar.  |
| RoutingRuleName | Namnet på den routningsregler som begäran matchade. |
| SecurityProtocol | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering. |
| TimeTaken | Hur lång tid åtgärden tog, i millisekunder. |
| UserAgent | Webbläsarens typ som används av klienten |
| TrackingReference | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |

## <a name="next-steps"></a>Nästa steg

- [Skapa en profil för en front dörr](quickstart-create-front-door.md)
- [Så här fungerar en front dörr](front-door-routing-architecture.md)
