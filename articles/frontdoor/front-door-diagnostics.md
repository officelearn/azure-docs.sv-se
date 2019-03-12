---
title: Azure ytterdörren Service – mått och loggning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå de olika mått och åtkomstloggar som har stöd för Azure ytterdörren Service
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
ms.openlocfilehash: d409d451385ba1f88189b12cf372845e70a87429
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726378"
---
# <a name="monitoring-metrics-for-front-door"></a>Övervakning av mått för ytterdörren

Genom att använda Azure ytterdörren Service kan övervaka du resurser på följande sätt:
* [Loggar](#diagnostic-logging): Loggar kan prestanda, åtkomst och andra data sparas eller konsumeras från en resurs för övervakning.

* [Mått](#metrics): Application Gateway har för närvarande sju mått för att visa prestandaräknare.

## <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser där du kan visa prestandaräknare i portalen. För ytterdörren finns följande mått:

| Mått | Metrisk visningsnamn | Enhet | Dimensioner | Beskrivning |
| --- | --- | --- | --- | --- |
| RequestCount | Antal begäranden | Antal | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet klientbegäranden som hanteras av ytterdörren.  |
| RequestSize | Begärandestorlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antal byte som skickas när begäranden från klienter till ytterdörren. |
| ResponseSize | Svarsstorlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antal byte som skickas som svar från åtkomsten till klienter. |
| TotalLatency | Total svarstid | Millisekunder | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Den tid som beräknas från när klientbegäran togs emot av ytterdörren tills klienten godkänt den sista byten svar från ytterdörren. |
| BackendRequestCount | Antal förfrågningar för serverdel | Antal | HttpStatus</br>HttpStatusGroup</br>Serverdel | Antal begäranden som skickas från åtkomsten till serverdelen. |
| BackendRequestLatency | Svarstid för backend-begäran | Millisekunder | Serverdel | Den tid som beräknas från när begäran skickades av dörren till serverdelen tills ytterdörren tog emot den sista byten av svaret från serverdelen. |
| BackendHealthPercentage | Serverdelens hälsotillstånd procent | Procent | Serverdel</br>BackendPool | Procentandelen av lyckade hälsorapporter avsökningar från ytterdörren servrar. |
| WebApplicationFirewallRequestCount | Web Application Firewall begäran antal | Antal | PolicyName</br>RuleName</br>Åtgärd | Antalet klientbegäranden bearbetas av application layer säkerheten för åtkomsten. |

## <a name="activity-log"></a>Aktivitetsloggar

Aktivitetsloggar ger insikt i de åtgärder som vidtogs för ytterdörren. Med aktivitetsloggar som du kan fastställa den ”vad, vem, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas på ytterdörren.

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET), åtgärder som utförs i Azure-portalen eller via ursprungliga hanterings-API:er.

Du kan få åtkomst till aktivitetsloggar i ytterdörren eller komma åt loggar för alla dina Azure-resurser i Azure Monitor. 

Så här visar du aktivitetsloggar:

1. Välj din ytterdörren-instans.
2. Klicka på **Aktivitetslogg**.

    ![aktivitetslogg](./media/front-door-diagnostics/activity-log.png)

3. Välj önskat filtreringsomfång och klicka på **Använd**.

## <a name="diagnostic-logging"></a>Diagnostikloggar
Diagnostikloggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning, samt i felsökningssyfte. Diagnostikloggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina Azure-resurser. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit. Läs mer om [diagnostikloggar för Azure Monitor](..\azure-monitor\platform\diagnostic-logs-overview.md). 

Så här konfigurerar du diagnostikloggar för ytterdörren:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Diagnostikinställningar**.

    ![diagnostikloggar](./media/front-door-diagnostics/diagnostic-log.png)

3. Klicka på **Slå på diagnostik**. Du kan arkivera diagnostikloggar tillsammans med mått i ett lagringskonto, strömma dem till en händelsehubb eller skicka dem till Azure Monitor-loggar. 

Azure ytterdörren-tjänsten har för närvarande diagnostik loggar (i batchar per timme) om enskilda API-begäran med varje inmatning har följande schema:

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

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
