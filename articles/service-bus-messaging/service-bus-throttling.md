---
title: Översikt över Azure Service Bus begränsning | Microsoft-dokument
description: Översikt över servicebussbegränsning - standard- och premiumnivåer.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598297"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Begränsningsåtgärder på Azure Service Bus

Inbyggda molnlösningar ger en uppfattning om obegränsade resurser som kan skalas med din arbetsbelastning. Även om det här begreppet är mer sant i molnet än med lokala system, finns det fortfarande begränsningar som finns i molnet.

Dessa begränsningar kan orsaka begränsning av klientprogrambegäranden på både standard- och premiumnivåer som beskrivs i den här artikeln. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Begränsning i Azure Service Bus Standard-nivå

Azure Service Bus Standard-nivån fungerar som en konfiguration med flera innehavare med en prismodell för användningsbaserad betalning. Här delar flera namnområden i samma kluster de tilldelade resurserna. Standardnivå är det rekommenderade valet för utvecklar-, testnings- och QA-miljöer tillsammans med produktionssystem för lågt dataflöde.

Tidigare hade Azure Service Bus grova begränsningsgränser som är strikt beroende av resursutnyttjande. Det finns dock en möjlighet att förfina begränsningslogiken och tillhandahålla förutsägbart begränsningsbeteende till alla namnområden som delar dessa resurser.

I ett försök att säkerställa rättvis användning och distribution av resurser i alla Azure Service Bus Standard-namnområden som använder samma resurser har begränsningslogiken ändrats till kreditbaserad.

> [!NOTE]
> Det är viktigt att notera att begränsning inte är ***ny*** för Azure Service Bus eller någon molnbaserad tjänst.
>
> Kreditbaserad begränsning förfinar helt enkelt hur olika namnområden delar resurser i en standardmiljö för flera innehavare och möjliggör därmed rättvis användning av alla namnområden som delar resurserna.

### <a name="what-is-credit-based-throttling"></a>Vad är kreditbaserad begränsning?

Kreditbaserad begränsning begränsar antalet åtgärder som kan utföras på ett visst namnområde under en viss tidsperiod. 

Nedan finns arbetsflödet för kreditbaserad begränsning - 

  * I början av varje tidsperiod tillhandahåller vi ett visst antal krediter till varje namnområde.
  * Alla åtgärder som utförs av avsändaren eller mottagaren klientprogram kommer att räknas mot dessa krediter (och subtraheras från tillgängliga krediter).
  * Om krediterna är uttömda begränsas efterföljande åtgärder till början av nästa tidsperiod.
  * Krediterna fylls på i början av nästa tidsperiod.

### <a name="what-are-the-credit-limits"></a>Vilka är kreditgränserna?

Kreditgränserna är för närvarande inställda på "1000" krediter varje sekund (per namnområde).

Alla åtgärder är inte skapade lika. Här är kreditkostnaderna för var och en av 

| Åtgärd | Kreditkostnad|
|-----------|-----------|
| Dataåtgärder (Skicka, SendAsync, Receive, ReceiveAsync, Peek) |1 kredit per meddelande |
| Hanteringsåtgärder (Skapa, Läsa, Uppdatera, Ta bort i köer, ämnen, prenumerationer, filter) | 10 hp |

> [!NOTE]
> Observera att när du skickar till ett ämne utvärderas varje meddelande mot filter innan de görs tillgängliga på prenumerationen.
> Varje filterutvärdering räknas också mot kreditgränsen (dvs. 1 kredit per filterutvärdering).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Hur vet jag att jag blir strypt?

När klientprogrambegäranden begränsas tas svaret på nedanstående server emot av ditt program och loggas.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Hur kan jag undvika att bli strypt?

Med delade resurser är det viktigt att genomdriva någon form av rättvis användning över olika Service Bus-namnområden som delar dessa resurser. Begränsning säkerställer att en topp i en enskild arbetsbelastning inte orsakar att andra arbetsbelastningar på samma resurser begränsas.

Som nämnts senare i artikeln finns det ingen risk att begränsas eftersom klient-SDK:erna (och andra Azure PaaS-erbjudanden) har standardprincipen för återförsök inbyggd i dem. Alla begränsade begäranden kommer att göras om med exponentiell backoff och kommer så småningom att gå igenom när krediterna fylls på.

Det är förståeligt att vissa program kan vara känsliga för att begränsas. I så fall rekommenderas att [du migrerar det aktuella servicebussstandardnamnområdet till Premium](service-bus-migrate-standard-premium.md). 

Vid migreringen kan du allokera dedikerade resurser till tjänstbussens namnområde och skala upp resurserna på rätt sätt om det finns en ökning av arbetsbelastningen och minska sannolikheten för att begränsas. När arbetsbelastningen minskar till normala nivåer kan du dessutom skala ned de resurser som allokerats till ditt namnområde.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Begränsning i Azure Service Bus Premium-nivå

[Azure Service Bus Premium-nivån](service-bus-premium-messaging.md) allokerar dedikerade resurser, i form av meddelandeenheter, till varje namnområdesinställning av kunden. Dessa dedikerade resurser ger förutsägbart dataflöde och svarstid och rekommenderas för system med hög genomströmning eller känslig produktionskvalitet.

Dessutom gör Premium-nivån det också möjligt för kunder att skala upp sin dataflödeskapacitet när de upplever toppar i arbetsbelastningen.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Hur fungerar begränsning i Service Bus Premium?

Med exklusiv resursallokering för Service Bus Premium drivs begränsningen enbart av begränsningarna för de resurser som allokerats till namnområdet.

Om antalet begäranden är fler än de aktuella resurserna kan serva begränsas begäranden.

### <a name="how-will-i-know-that-im-being-throttled"></a>Hur vet jag att jag blir strypt?

Det finns olika sätt att identifiera begränsning i Azure Service Bus Premium - 
  * **Begränsade begäranden** visas på [Azure Monitor Request-måtten](service-bus-metrics-azure-monitor.md#request-metrics) för att identifiera hur många begäranden som begränsades.
  * Hög **CPU-användning** indikerar att den aktuella resursallokeringen är hög och begäranden kan begränsas om den aktuella arbetsbelastningen inte minskar.
  * Hög **minnesanvändning** indikerar att den aktuella resursallokeringen är hög och begäranden kan begränsas om den aktuella arbetsbelastningen inte minskar.

### <a name="how-can-i-avoid-being-throttled"></a>Hur kan jag undvika att bli strypt?

Eftersom Service Bus Premium-namnområdet redan har dedikerade resurser kan du minska risken för att få begränsat genom att skala upp antalet meddelandeenheter som tilldelats ditt namnområde i händelse (eller förväntan) för en ökning av arbetsbelastningen.

Skala upp/ned kan uppnås genom att skapa [runbooks](../automation/automation-create-alert-triggered-runbook.md) som kan utlösas av ändringar i ovanstående mått.

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="how-does-throttling-affect-my-application"></a>Hur påverkar begränsningen mitt program?

När en begäran begränsas innebär det att tjänsten är upptagen eftersom den står inför fler begäranden än resurserna tillåter. Om samma åtgärd försöker igen efter en stund, när tjänsten har arbetat igenom sin aktuella arbetsbelastning, kan begäran uppfyllas.

Eftersom begränsning är det förväntade beteendet för alla molninbyggda tjänster har vi skapat logiken för återförsök i Azure Service Bus SDK själv. Standardinställningen är inställd på automatiskt nytt försök med en exponentiell back-off för att säkerställa att vi inte har samma begäran som begränsas varje gång.

Standardlogiken för återförsök gäller för varje åtgärd.

### <a name="does-throttling-result-in-data-loss"></a>Leder begränsningen till dataförlust?

Azure Service Bus är optimerad för persistens, vi ser till att alla data som skickas till Service Bus har åtagit sig att lagras innan tjänsten bekräftar att begäran är lyckad.

När begäran har slutförts "ACK" (bekräftad) av Service Bus innebär det att Service Bus har behandlat begäran. Om Service Bus returnerar en "NACK" (fel), innebär det att Service Bus inte har kunnat behandla begäran och klientprogrammet måste försöka igen begäran.

Men när en begäran begränsas innebär tjänsten att den inte kan acceptera och bearbeta begäran just nu på grund av resursbegränsningar. Detta **innebär inte** någon form av dataförlust eftersom Service Bus helt enkelt inte har tittat på begäran. I det här fallet säkerställer förlita sig på standardprincipen för återförsök i Service Bus SDK att begäran så småningom bearbetas.

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus-meddelanden finns i följande avancerade avsnitt:

* [Översikt över servicebussmeddelanden](service-bus-messaging-overview.md)
* [Snabbstart: Skicka och ta emot meddelanden med Azure-portalen och .NET](service-bus-quickstart-portal.md)
* [Självstudie: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)

