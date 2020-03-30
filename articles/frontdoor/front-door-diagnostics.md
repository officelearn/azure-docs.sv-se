---
title: Övervakning av mått och loggar i Azure Ytterdörr| Microsoft-dokument
description: I den här artikeln beskrivs de olika mått och åtkomstloggar som Azure Front Door stöder
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
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471735"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Övervaka mått och loggar i Azure Front Door

Genom att använda Azure Front Door kan du övervaka resurser på följande sätt:

- **Mått**. Azure Front Door har för närvarande sju mått för att visa prestandaräknare.
- **Loggar**. Aktivitets- och diagnostikloggar gör att prestanda, åtkomst och andra data kan sparas eller förbrukas från en resurs i övervakningssyfte.

### <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser som gör att du kan visa prestandaräknare i portalen. Följande är tillgängliga frontdörrmått:

| Mått | Namn på mätdisplay | Enhet | Dimensioner | Beskrivning |
| --- | --- | --- | --- | --- |
| Begäranrring | Antal begäranden | Antal | HttpStatus (HttpStatus)</br>HttpStatusGroup</br>Klientregion</br>Antal kunder | Antalet kundförfrågningar som betjänas av Ytterdörren.  |
| Begäransstorlek | Storlek för begäran | Byte | HttpStatus (HttpStatus)</br>HttpStatusGroup</br>Klientregion</br>Antal kunder | Antalet byte som skickas som begäranden från klienter till ytterdörren. |
| SvarStorlek | Responsstorlek | Byte | HttpStatus (HttpStatus)</br>HttpStatusGroup</br>Klientregion</br>Antal kunder | Antalet byte som skickas som svar från ytterdörren till kunder. |
| TotalLatency | Total latens | Millisekunder | HttpStatus (HttpStatus)</br>HttpStatusGroup</br>Klientregion</br>Antal kunder | Den tid som beräknas från klientbegäran som tas emot av Ytterdörren tills klienten bekräftade den sista svarsbyten från ytterdörren. |
| BackendRequestCount | Antal begäranden efter rygg | Antal | HttpStatus (HttpStatus)</br>HttpStatusGroup</br>Backend | Antalet förfrågningar som skickas från ytterdörren till serverdels. |
| BackendRequestLatency | Svarstid för begäran om bakåtsträvning | Millisekunder | Backend | Den tid som beräknats från när begäran skickades av ytterdörren till serverdelen tills Ytterdörren fick den sista svarsbyten från serverdelen. |
| BackendHealthPercentage | Hälsoprocent för backend | Procent | Backend</br>BackendPool (rygg) | Andelen framgångsrika hälsosonder från ytterdörren till serverdelserna. |
| WebApplicationFirewallRequestCount | Antal begäranden om brandvägg för webbprogram | Antal | PolicyName</br>Regelnamn</br>Åtgärd | Antalet klientbegäranden som bearbetas av programskiktets säkerhet för Ytterdörren. |

## <a name="activity-logs"></a><a name="activity-log"></a>Aktivitetsloggar

Aktivitetsloggar ger information om de åtgärder som utförs på ytterdörren. De bestämmer också vad, vem och när för alla skrivåtgärder (sätta, post eller ta bort) tas på ytterdörren.

>[!NOTE]
>Aktivitetsloggar innehåller inte läs-(hämta) åtgärder. De innehåller inte heller åtgärder som du utför med hjälp av antingen Azure-portalen eller det ursprungliga hanterings-API:et.

Få åtkomst till aktivitetsloggar i ytterdörren eller alla loggar för dina Azure-resurser i Azure Monitor. Så här visar du aktivitetsloggar:

1. Välj din frontdörrsförekomst.
2. Välj **Aktivitetslogg**.

    ![Aktivitetslogg](./media/front-door-diagnostics/activity-log.png)

3. Välj ett filtreringsomfång och välj sedan **Använd**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostiska loggar
Diagnostikloggar ger omfattande information om åtgärder och fel som är viktiga för granskning och felsökning. Diagnostikloggar skiljer sig från aktivitetsloggar.

Aktivitetsloggar ger insikter om de åtgärder som utförs på Azure-resurser. Diagnostikloggar ger insikt i åtgärder som din resurs har utfört. Mer information finns i [Diagnostikloggar för Azure Monitor](../azure-monitor/platform/platform-logs-overview.md).

![Diagnostikloggar](./media/front-door-diagnostics/diagnostic-log.png)

Så här konfigurerar du diagnostikloggar för ytterdörren:

1. Välj din Azure ytterdörr.

2. Välj **Diagnostikinställningar**.

3. Välj **Aktivera diagnostik**. Arkivera diagnostikloggar tillsammans med mått till ett lagringskonto, strömma dem till en händelsenav eller skicka dem till Azure Monitor-loggar.

Ytterdörren tillhandahåller för närvarande diagnostikloggar (batchade varje timme). Diagnostikloggar tillhandahåller enskilda API-begäranden med varje post med följande schema:

| Egenskap  | Beskrivning |
| ------------- | ------------- |
| BackendHostname (olikartade) | Om begäran vidarebefordrades till en backend representerar det här fältet värdnamnet för backend. Det här fältet är tomt om begäran omdirigerades eller vidarebefordrades till en regional cache (när cachelagring är aktiverad för routningsregeln). |
| CacheStatus | För cachelagringsscenarier definierar det här fältet cachehit/miss på POP |
| ClientIp | IP-adressen för klienten som gjorde begäran. Om det fanns ett X-Forwarded-For-huvud i begäran plockas klient-IP-adressen från samma. |
| ClientPort (klientport) | IP-porten för klienten som gjorde begäran. |
| Mer från HttpMethod | HTTP-metod som används av begäran. |
| HttpStatusCode (HttpStatusCode) | HTTP-statuskoden returnerades från proxyn. |
| HttpStatusDetails | Resulterande status på begäran. Innebörden av det här strängvärdet finns i en statusreferenstabell. |
| HttpVersion (på ett sätt) | Typ av begäran eller anslutning. |
| POP | Kort namn på kanten där begäran landade. |
| RequestBytes | Storleken på HTTP-begäran meddelandet i byte, inklusive begäran rubriker och begäran kroppen. |
| RequestUri (förfråganuri) | URI för den mottagna begäran. |
| ResponseBytes | Byte som skickas av serverdservern som svar.  |
| RoutingRuleName | Namnet på routningsregeln som begäran matchade. |
| SäkerhetProtocol | TLS/SSL-protokollversionen som används av begäran eller null om ingen kryptering. |
| Skickat tillOriginShield | Booleskt fält som representerar om det fanns en cache miss på den första miljön och begäran skickades till den regionala cachen. Ignorera det här fältet om routningsregeln är en omdirigering eller när cachelagring inte är aktiverad. |
| Tidskörd | Den tid som åtgärden vidtog, i millisekunder. |
| TrackingReference | Den unika referenssträngen som identifierar en begäran som betjänas av Ytterdörren, som också skickas som X-Azure-Ref-huvud till klienten. Krävs för att söka efter information i åtkomstloggarna för en viss begäran. |
| Useragent | Den webbläsartyp som klienten använde. |

**Anm.:** För olika routningskonfigurationer och trafikbeteenden kan vissa fält som backendHostname, cacheStatus, sentToOriginShield och POP-fältet svara med olika värden. I tabellen nedan förklaras de olika värdena, dessa fält har för olika scenarier:

| Scenarier | Antal loggposter | POP | BackendHostname (olikartade) | Skickat tillOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Routningsregel utan cachelagring aktiverad | 1 | POP-kod för kant | Backend där begäran vidarebefordrades | False | CONFIG_NOCACHE |
| Routningsregel med cachelagring aktiverad. Cache träff på kanten POP | 1 | POP-kod för kant | Tom | False | Slå |
| Routningsregel med cachelagring aktiverad. Cache miss vid kanten POP men cache hit på överordnad cache POP | 2 | 1. Edge POP-kod</br>2. Pop-kod för den överordnade cachen | 1. Pop-värdnamn för den överordnade cachen</br>2. Tom | 1. Sant</br>2. Falskt | 1. MISS</br>2 PARTIAL_HIT. |
| Routningsregel med cachelagring aktiverad. Cachemiss vid pop-pop för både kant- och överordnad cache | 2 | 1. Edge POP-kod</br>2. Pop-kod för den överordnade cachen | 1. Pop-värdnamn för den överordnade cachen</br>2. Backend som hjälper fylla cache | 1. Sant</br>2. Falskt | 1. MISS</br>2. MISS |


## <a name="next-steps"></a>Nästa steg

- [Skapa en profil för ytterdörren](quickstart-create-front-door.md)
- [Hur ytterdörren fungerar](front-door-routing-architecture.md)
