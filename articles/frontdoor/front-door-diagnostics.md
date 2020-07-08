---
title: Övervaka mått och loggar i Azures front dörr | Microsoft Docs
description: I den här artikeln beskrivs de olika mått och åtkomst loggar som stöds av Azures front dörr
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
ms.openlocfilehash: f57c0353989cfcf924042d202bd80a57b476507b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322306"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Övervaka mått och loggar i Azures front dörr

Genom att använda Azures front dörr kan du övervaka resurser på följande sätt:

- **Mått**. Azures front dörr har för närvarande sju mått för att visa prestanda räknare.
- **Loggar**. Med aktivitets-och diagnostikloggar kan prestanda, åtkomst och annan information sparas eller förbrukas från en resurs i övervaknings syfte.

### <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser som gör att du kan visa prestanda räknare i portalen. Följande är tillgängliga mått på front dörren:

| Mått | Mått visnings namn | Enhet | Dimensioner | Beskrivning |
| --- | --- | --- | --- | --- |
| RequestCount | Antal begäranden | Antal | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet klient förfrågningar som hanteras av frontend-dörren.  |
| RequestSize | Begär ande storlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet byte som har skickats som begär Anden från klienter till front dörren. |
| ResponseSize | Svars storlek | Byte | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Antalet byte som har skickats som svar från Front dörren till klienter. |
| TotalLatency | Total svars tid | Millisekunder | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Den tid som beräknas från den klientbegäran som mottagits av front dörren tills klienten bekräftade den senaste svars byten från Front dörren. |
| BackendRequestCount | Antal Server dels begär Anden | Antal | HttpStatus</br>HttpStatusGroup</br>Serverdel | Antalet förfrågningar som skickats från Front dörren till Server delar. |
| BackendRequestLatency | Svars tid för Server del | Millisekunder | Serverdel | Tiden räknat från när begäran skickades av frontend till Server delen tills front dörren fick den senaste svars byten från Server delen. |
| BackendHealthPercentage | Server delens hälso procent | Procent | Serverdel</br>BackendPool | Procent andelen lyckade hälso avsökningar från Front dörren till Server delar. |
| WebApplicationFirewallRequestCount | Antal begär Anden om webb programs brand vägg | Antal | PolicyName</br>RuleName</br>Åtgärd | Antalet klient begär Anden som bearbetats av program nivå säkerheten för front dörren. |

## <a name="activity-logs"></a><a name="activity-log"></a>Aktivitetsloggar

Aktivitets loggar innehåller information om de åtgärder som utförs på front dörren. De avgör också vad, vem och när det gäller Skriv-, post-eller borttagnings åtgärder som utförs på front dörren.

>[!NOTE]
>Aktivitets loggar innehåller inte Läs åtgärder (get). De omfattar inte heller åtgärder som du utför med hjälp av antingen Azure Portal eller ursprungligt hanterings-API.

Få åtkomst till aktivitets loggar i din front dörr eller alla loggar för dina Azure-resurser i Azure Monitor. Så här visar du aktivitetsloggar:

1. Välj din instans av frontend-dörren.
2. Välj **aktivitets logg**.

    ![Aktivitetslogg](./media/front-door-diagnostics/activity-log.png)

3. Välj en filtrerings omfattning och välj sedan **Använd**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostikloggar
Diagnostikloggar ger omfattande information om åtgärder och fel som är viktiga för granskning och fel sökning. Diagnostikloggar skiljer sig från aktivitets loggar.

Aktivitets loggar ger insikter om de åtgärder som utförs på Azure-resurser. Diagnostikloggar ger insikter om åtgärder som din resurs utfört. Mer information finns i [Azure Monitor diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md).

![Diagnostikloggar](./media/front-door-diagnostics/diagnostic-log.png)

Så här konfigurerar du diagnostikloggar för din frontend-dörr:

1. Välj din Azure-front dörr.

2. Välj **diagnostikinställningar**.

3. Välj **Aktivera diagnostik**. Arkivera diagnostikloggar tillsammans med mått till ett lagrings konto, strömma dem till en händelsehubben eller skicka dem till Azure Monitor loggar.

Front dörren innehåller för närvarande diagnostikloggar (batch-varje timme). Diagnostikloggar tillhandahåller enskilda API-begäranden med varje post med följande schema:

| Egenskap  | Beskrivning |
| ------------- | ------------- |
| BackendHostname | Om begäran vidarebefordrades till en server del representerar Server delens värdnamn. Det här fältet är tomt om begäran har omdirigerats eller vidarebefordrats till ett regionalt cacheminne (när cachelagring har Aktiver ATS för regeln). |
| CacheStatus | För cachelagring av scenarier definierar det här fältet cache-träff/missar på POP |
| ClientIp | IP-adressen för klienten som gjorde begäran. Om det fanns ett X-vidarebefordrat-för-huvud i begäran plockas klientens IP-adress från samma. |
| ClientPort | IP-porten för den klient som gjorde begäran. |
| HttpMethod | HTTP-metod som används av begäran. |
| HttpStatusCode | HTTP-statuskod som returnerades från proxyservern. |
| HttpStatusDetails | Resulterande status för begäran. Innebörd av detta sträng värde finns i en status referens tabell. |
| HttpVersion | Typ av begäran eller anslutning. |
| POP | Kort namn på den kant där begäran landats. |
| RequestBytes | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten. |
| RequestUri | URI för den mottagna begäran. |
| ResponseBytes | Byte som skickats av backend-servern som svar.  |
| RoutingRuleName | Namnet på den routningsregler som begäran matchade. |
| RulesEngineMatchNames | Namnen på de regler som begäran matchade. |
| SecurityProtocol | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering. |
| SentToOriginShield | Booleskt fält som representerar om det fanns en cache missar i den första miljön och begäran skickades till den regionala cachen. Ignorera det här fältet om Routningsprincipen är en omdirigering eller om inte cachelagring är aktiverat. |
| TimeTaken | Hur lång tid åtgärden tog, i millisekunder. |
| TrackingReference | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |
| UserAgent | Webbläsarens typ som används av klienten. |

**Obs:** För olika konfigurationer av Routning och trafik, kan vissa av fälten som backendHostname, cacheStatus, sentToOriginShield och POP-fält svara med olika värden. I tabellen nedan förklaras de olika värdena, dessa fält kommer att ha för olika scenarier:

| Scenarier | Antal logg poster | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Routningsregler utan cachelagring aktiverat | 1 | Kant-POP-kod | Backend där begäran vidarebefordrades | Falskt | CONFIG_NOCACHE |
| Routningsregler med cachelagring aktiverat. Cacheträff i Edge-popup | 1 | Kant-POP-kod | Tom | Falskt | TRÄFFA |
| Routningsregler med cachelagring aktiverat. Cache missar i Edge POP, men cache träff vid överordnat cache-fönster | 2 | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. POP-värdnamn för överordnad cache</br>2. Tom | 1. True</br>2. false | 1. SAKNAT</br>2. PARTIAL_HIT |
| Routningsregler med cachelagring aktiverat. Cache missar i både Edge och överordnad cache POP | 2 | 1. Edge POP-kod</br>2. POP-kod för överordnad cache | 1. POP-värdnamn för överordnad cache</br>2. Server del som hjälper till att fylla i cache | 1. True</br>2. false | 1. SAKNAT</br>2. SAKNAT |


## <a name="next-steps"></a>Nästa steg

- [Skapa en profil för en front dörr](quickstart-create-front-door.md)
- [Så här fungerar en front dörr](front-door-routing-architecture.md)
