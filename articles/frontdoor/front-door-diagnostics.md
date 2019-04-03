---
title: Övervaka mått och loggar i Azure ytterdörren Service | Microsoft Docs
description: Den här artikeln beskrivs de olika mått och åtkomstloggar som har stöd för Azure ytterdörren Service
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
ms.openlocfilehash: 98aabf5330589bf80f1653bb2882c015a4bc133c
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862122"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Övervaka mått och loggar i Azure ytterdörren Service

Genom att använda Azure ytterdörren Service kan övervaka du resurser på följande sätt:

- **Mått**. Application Gateway har för närvarande sju mått för att visa prestandaräknare.
- **Loggar**. Aktivitet och diagnostikloggar kan prestanda, åtkomst och andra data sparas eller konsumeras från en resurs för övervakning.

### <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser så att du kan visa prestandaräknare i portalen. Följande är tillgängliga ytterdörren mått:

| Mått | Metrisk visningsnamn | Enhet | Dimensioner | Beskrivning |
| --- | --- | --- | --- | --- |
| RequestCount | Antal begäranden | Antal | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet klientbegäranden som hanteras av ytterdörren.  |
| RequestSize | Begärandestorlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antal byte som skickas när begäranden från klienter till ytterdörren. |
| ResponseSize | Svarsstorlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antal byte som skickas som svar från åtkomsten till klienter. |
| TotalLatency | Total svarstid | Millisekunder | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Tid beräknas från klientbegäran tas emot av ytterdörren tills klienten godkänt den sista byten svar från ytterdörren. |
| BackendRequestCount | Antal förfrågningar för serverdel | Antal | HttpStatus</br>HttpStatusGroup</br>Serverdel | Antal begäranden som skickas från åtkomsten till serverdelen. |
| BackendRequestLatency | Svarstid för backend-begäran | Millisekunder | Serverdel | Den tid som beräknas från när begäran skickades av dörren till serverdelen tills ytterdörren tog emot den sista byten av svaret från serverdelen. |
| BackendHealthPercentage | Serverdelens hälsotillstånd procent | Procent | Serverdel</br>BackendPool | Procentandelen av lyckade hälsorapporter avsökningar från ytterdörren servrar. |
| WebApplicationFirewallRequestCount | Web Application Firewall begäran antal | Antal | PolicyName</br>RuleName</br>Åtgärd | Antalet klientbegäranden bearbetas av application layer säkerheten för åtkomsten. |

## <a name="activity-log"></a>Aktivitetsloggar

Aktivitetsloggar ger information om åtgärder som gjorts på ytterdörren Service. De bestämmer också vad, vem, och när för alla skrivåtgärder (put, post eller ta bort) vidtas på ytterdörren Service.

>[!NOTE]
>Aktivitetsloggar inkluderar inte läsåtgärder (get). Även omfattar de inte åtgärder som du utför med hjälp av Azure-portalen eller den ursprungliga Management-API: et.

Åtkomst till aktivitetsloggar i din ytterdörren tjänst eller alla loggar för dina Azure-resurser i Azure Monitor. Så här visar du aktivitetsloggar:

1. Välj din ytterdörren-instans.
2. Välj **aktivitetsloggen**.

    ![Aktivitetslogg](./media/front-door-diagnostics/activity-log.png)

3. Välj ett omfång som filtrerande och välj sedan **tillämpa**.

## <a name="diagnostic-logging"></a>Diagnostikloggar
Diagnostikloggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning och felsökning. Diagnostikloggar skiljer sig från aktivitetsloggar.

Aktivitetsloggar ger insikt i de åtgärder som gjorts på Azure-resurser. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit. Mer information finns i [diagnostikloggar för Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md).

![Diagnostikloggar](./media/front-door-diagnostics/diagnostic-log.png)

Så här konfigurerar du diagnostikloggar för ytterdörren tjänsten:

1. Välj Azure API Management-tjänsten.

2. Välj **diagnostikinställningar**.

3. Välj **slå på diagnostik**. Arkivera diagnostikloggar tillsammans med mått i ett lagringskonto, strömma dem till en event hub eller skicka dem till Azure Monitor-loggar.

Ytterdörren Service erbjuder för närvarande diagnostikloggar (i batchar per timme). Diagnostikloggar innehåller enskilda API-begäranden där varje inmatning har följande schema:

| Egenskap   | Beskrivning |
| ------------- | ------------- |
| ClientIp | IP-adressen för klienten som gjorde begäran. |
| ClientPort | IP-porten för den klient som gjorde begäran. |
| HttpMethod | HTTP-metod som används i begäran. |
| HttpStatusCode | HTTP-statuskod returnerades från proxyn. |
| HttpStatusDetails | Resulterande status på begäran. Betydelsen av den här strängvärde finns på en referenstabell för Status. |
| HttpVersion | Typ av begäran eller anslutning. |
| RequestBytes | Storleken på meddelandet med HTTP-begäran i byte, inklusive begärandehuvudena och begärandetexten. |
| RequestUri | URI för fick begäran. |
| ResponseBytes | Byte skickade efter backend-servern som svaret.  |
| RoutingRuleName | Namnet på en routningsregel för som matchade begäran. |
| SecurityProtocol | TLS/SSL-protokollversion som används av den förfrågan eller null om ingen kryptering. |
| TimeTaken | Hur lång tid åtgärden tog, i millisekunder. |
| UserAgent | Typ av webbläsare som användes av klienten |
| TrackingReference | Unikt referenssträng som identifierar en begäran som hanteras av ytterdörren, skickas även som rubrik för X-Azure-Ref till klienten. Krävs för att söka information i åtkomstloggar för en specifik begäran. |

## <a name="next-steps"></a>Nästa steg

- [Skapa en ytterdörren-profil](quickstart-create-front-door.md)
- [Hur ytterdörren fungerar](front-door-routing-architecture.md)
