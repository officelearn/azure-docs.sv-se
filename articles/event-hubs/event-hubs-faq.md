---
title: Azure Event Hubs vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor (och svar FAQ) om Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 06/07/2018
ms.author: shvija
ms.openlocfilehash: 3e98d27c297e223231e0990adc51f8765678b884
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006632"
---
# <a name="event-hubs-frequently-asked-questions"></a>Vanliga och frågor svar om Event Hubs

## <a name="general"></a>Allmänt

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Vad är skillnaden mellan Event Hubs Basic och Standard-nivån?

Standardnivån för Azure Event Hubs tillhandahåller funktioner utöver vad som är tillgängligt i Basic-nivån. Följande funktioner ingår i Standard:

* Längre kvarhållning för händelse
* Fler asynkrona anslutningar med en extra avgift för mer än det antal som ingår
* Mer än en enda [konsumentgrupp](event-hubs-features.md#consumer-groups)
* [samla in](event-hubs-capture-overview.md)

Läs mer om prissättning för nivåerna, inklusive Event Hubs Dedicated den [Händelsehubbar prisinformation](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Vad är throughput units för Event Hubs?

Du kan uttryckligen välja Event Hubs-dataflödesenheter, antingen via Azure portal eller Event Hubs Resource Manager-mallar. Throughput units gäller för alla händelsehubbar i ett namnområde för Event Hubs och varje throughput unit berättigar namnrymden till följande funktioner:

* Upp till 1 MB per sekund för ingångshändelser (händelser skickas till ett händelsenav), men inga fler än 1000 ingångshändelser, hanteringsåtgärder eller kontrollen API-anrop per sekund.
* Upp till 2 MB per sekund för utgångshändelser (händelser som konsumeras från ett händelsenav), men inte mer än 4096 utgångshändelser.
* Upp till 84 GB händelselagring (tillräckligt för standardlagringstiden på 24 timmar).

Throughput units för Event Hubs debiteras per timme baserat på det maximala antalet enheter som valts under timmen. Du kan automatiskt [öka antalet dataflödesenheter med hjälp av automatisk ökning](event-hubs-auto-inflate.md) när användningen ökar.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Hur övervakas Händelsehubbar dataflödesbegränsningar för enhet?

Om den totala ingångar eller den sammanlagda takten för ingångshändelser på alla händelsehubbar i ett namnområde överskrider den sammanlagda dataflödet enhet, avsändare begränsas och få felmeddelanden som anger att ingående kvoten har överskridits.

Om den totala genomströmningen av utgångar eller den sammanlagda takten för utgångshändelser på alla händelsehubbar i ett namnområde överskrider den sammanlagda dataflödet enhet mottagare begränsas och få felmeddelanden som anger att den utgående kvoten har överskridits. In- och utgångskvoterna upprätthålls separat, så att ingen avsändare kan orsaka att händelseförbrukningen, inte heller kan någon mottagare hindra att händelser skickas in till en händelsehubb.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Finns det en gräns för det antal Throughput Units som kan väljas?

Det finns en standardkvot på 20 throughput units per namnrymd. Du kan begära en större kvot på genomflödesenheter genom att skicka in ett supportärende. Paket är tillgängliga i 20 och 100 genomflödesenheter utanför 20 throughput unit-gränsen. Observera att med hjälp av fler än 20 dataflödesenheter tar bort möjligheten att ändra antal throughput units utan att skicka in ett supportärende.

Med hjälp av den [automatisk ökning](event-hubs-auto-inflate.md) funktion, kan du automatiskt öka antalet dataflödesenheter när användningen ökar.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan jag använda en enkel AMQP-anslutning för att skicka och ta emot från flera event hubs?

Ja, så länge alla event hubs finns i samma namnområde.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Vad är den högsta bevarandeperioden för händelser?

Event Hubs Standard-nivån stöder för närvarande en högsta kvarhållningsperiod på 7 dagar. Observera att händelsehubbar inte är avsedda för permanent datalagring. Lagringstider som är mer än 24 timmar avses för scenarier där det är lämpligt att spela upp en händelseström till samma system; till exempel att finjustera eller verifiera en ny maskininlärningsmodell på befintliga data. Om du behöver kvarhållning av meddelanden i längre än 7 dagar, så att [Event Hubs Capture](event-hubs-capture-overview.md) på din event hub hämtar data från din event hub i Storage-konto eller Azure Data Lake-tjänstkonto valfritt. Avbildningsfunktionen utgår en avgift baserat på dina köpta genomflödesenheterna.

### <a name="where-is-azure-event-hubs-available"></a>Var finns Azure Event Hubs?

Händelsehubbar i Azure är tillgängligt i alla regioner som stöds Azure. En lista på den [Azure-regioner](https://azure.microsoft.com/regions/) sidan.  

## <a name="best-practices"></a>Bästa praxis

### <a name="how-many-partitions-do-i-need"></a>Hur många partitioner behöver jag?

Observera att antalet partitioner i en händelsehubb inte kan ändras efter installationen. Det är viktigt att tänka om hur många partitioner som du behöver för att komma igång med det i åtanke. 

Event Hubs har utformats så att en enskild partition läsare per konsumentgrupp. I de flesta användningar räcker standardinställningen för fyra partitioner. Om du vill skala ditt händelsebearbetning är kan du överväga att lägga till ytterligare partitioner. Det finns ingen dataflödesgräns för specifika på en partition, men den sammanlagda genomströmningen i namnområdet begränsas av antalet dataflödesenheter. När du ökar antalet dataflödesenheter i namnområdet, kan du ytterligare partitioner så att samtidiga läsare att uppnå sina egna maximalt dataflöde.

Men om du har en modell där programmet har en mappning till en viss partition kan öka antalet partitioner inte av en förmån för dig. Mer information om detta finns i [tillgänglighet och konsekvens](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-more-pricing-information"></a>Var hittar jag mer information om priser?

Fullständig information om priser för Event Hubs finns i den [Händelsehubbar prisinformation](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Finns det en kostnad för att behålla Event Hubs-händelser i mer än 24 timmar?

Event Hubs Standard-nivån tillåter meddelandelagring punkter som är längre än 24 timmar innan på högst 7 dagar. Om storleken på det totala antalet lagrade händelser överstiger lagringskvoten för antalet dataflödesenheter som valts (84 GB per dataflödesenhet), debiteras den storlek som överstiger kvoten enligt taxan för publicerade Azure Blob-lagring. Lagringskvoten i varje throughput unit täcker alla lagringskostnader för lagringstider på 24 timmar (standard) även om dataflödesenheterna används till den maximala kvoten för ingångshändelser.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hur lagringsstorlek Händelsehubbar beräknas och debiteras?

Den totala storleken för alla lagrade händelser, inklusive eventuell intern tillhörande information för händelserubriker eller strukturer för lagring av disk på i alla händelsehubbar mäts under hela dagen. Vid slutet av dagen beräknas den högsta lagringsstorleken. Den dagliga lagringskvoten beräknas baserat på det minsta antal dataflödesenheten som valdes under dagen (varje dataflödesenhet ger en kvot på 84 GB). Om den totala storleken överstiger den dagliga lagringskvoten beräknade, debiteras lagringen som använder Azure Blob-lagringstaxor (vid den **lokalt Redundant lagring** pris).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hur beräknas ingångshändelser i Event Hubs?

Varje händelse som skickas till ett händelsenav räknas som ett faktureringsbart meddelande. En *ingångshändelse* definieras som en enhet med data som är mindre än eller lika med 64 KB. Varje händelse som är mindre än eller lika med 64 KB i storlek anses vara en fakturerbar händelse. Om händelsen är större än 64 KB beräknas antalet faktureringsbara händelser enligt händelsestorleken i multipler av 64 KB. Exempelvis kan en händelse på 8 KB skickas till event hub faktureras som en händelse, men ett meddelande på 96 KB som skickas till event hub faktureras som två händelser.

Händelser som konsumeras från ett händelsenav, samt hanteringsåtgärder och kontrollen anropar som t.ex kontrollpunkter, räknas inte som fakturerbara ingångshändelser, men nivån upp till throughput unit-kvoten.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gäller brokered connection-avgifter för Event Hubs?

Connection-avgifter gäller endast när AMQP-protokollet används. Du debiteras inga anslutningsavgifter för att skicka händelser via HTTP, oavsett antalet sändande system eller enheter. Om du planerar att använda AMQP (t.ex, för att få effektivare händelseströmning eller för att aktivera tvåvägskommunikation i IoT-kommando och kontroll scenarier) kan du läsa den [information om priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/) för information om hur många anslutningar som ingår i varje tjänstnivå.

### <a name="how-is-event-hubs-capture-billed"></a>Hur faktureras Event Hubs Capture?

Capture aktiveras när en händelsehubb i namnområdet har avbildningsalternativet aktiverat. Event Hubs Capture faktureras per timme per köpt dataflödesenhet. När antalet dataflödesenheter ökar eller minskar, återspeglar Event Hubs Capture fakturering ändringarna i hela timmar. Läs mer om Event Hubs Capture fakturering [information om priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Blir jag Debiterad för det lagringskonto jag väljer för Event Hubs Capture?

Capture använder ett lagringskonto som du anger när du aktiverat på en händelsehubb. Eftersom det här är ditt lagringskonto faktureras ändringar för den här konfigurationen på Azure-prenumerationen.

## <a name="quotas"></a>Kvoter

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Finns det kvoter som är associerade med Event Hubs?

En lista över alla Event Hubs-kvoter finns i [kvoter](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Vilka är några av undantagen som genereras av Event Hubs och föreslagna åtgärder?

En lista över möjliga undantag för Händelsehubbar finns i [undantag översikt](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostikloggar

Event Hubs stöder två typer av [diagnostikloggar](event-hubs-diagnostic-logs.md) -avbilda felloggar och driftloggar - som representeras i json och kan aktiveras via Azure portal.

### <a name="support-and-sla"></a>Support och servicenivåavtal

Teknisk support för Event Hubs är tillgängliga via den [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Support för fakturering och prenumerationshantering ges utan kostnad.

Mer information om serviceavtal finns på [serviceavtal](https://azure.microsoft.com/support/legal/sla/) sidan.

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Event Hubs automatisk ökning](event-hubs-auto-inflate.md)
