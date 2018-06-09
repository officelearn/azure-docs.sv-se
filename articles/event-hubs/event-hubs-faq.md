---
title: Azure Event Hubs vanliga frågor och svar | Microsoft Docs
description: Händelsehubbar i Azure vanliga frågor (FAQ)
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 06/07/2018
ms.author: sethm
ms.openlocfilehash: 4c28f1ae5517e54fab259e680128d6c666c82fbe
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235319"
---
# <a name="event-hubs-frequently-asked-questions"></a>Vanliga och frågor svar om Händelsehubbar

## <a name="general"></a>Allmänt

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Vad är skillnaden mellan Event Hubs Basic och Standard nivåer?

Standardnivån av Händelsehubbar i Azure tillhandahåller funktioner utöver vad som är tillgängliga i den grundläggande nivån. Följande funktioner ingår i Standard:
* Längre kvarhållning av händelse
* Ytterligare asynkrona anslutningar med en överförbrukning kostnad för mer än det antal som ingår
* Mer än en enskild konsument-grupp
* [Avbilda](event-hubs-capture-overview.md)

Mer information om prisnivåer, inklusive Event Hubs dedikerade, finns det [Händelsehubbar prisinformationen](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Vad är Händelsehubbar enheter?

Du välja uttryckligen Händelsehubbar genomflödesenheter, antingen via Azure-portalen eller Event Hubs Resource Manager-mallar. Genomflödesenheter gäller för alla händelsehubbar i ett namnområde för Händelsehubbar och varje genomflödesenhet kan namnområdet till följande funktioner:

* Upp till 1 MB per sekund för ingångshändelser (händelser skickas till en händelsehubb), men inga fler än 1000 ingångshändelser, hanteringsåtgärder eller kontrollen API-anrop per sekund.
* Upp till 2 MB per sekund av utgång händelser (händelser används från en händelsehubb).
* Upp till 84 GB händelselagring (tillräckligt för standardlagringstiden på 24 timmar).

Event debiteras Hubs varje timme, baserat på det maximala antalet enheter som är markerade under den angivna timmen. Du kan automatiskt [öka antalet genomflödesenheter](event-hubs-auto-inflate.md) som ökar din användning.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Hur tillämpas Händelsehubbar genomströmning enhet gränser?
Om det totala ingång genomflödet eller totala ingång händelsehastighet för alla händelsehubbar i ett namnområde överskrider sammanställda genomflöde enhet bidrag, avsändare har begränsats och felmeddelanden som anger att meddelanden om ingångs-kvoten har överskridits.

Om det totala utgång genomflödet eller den totala utgång hastigheten för alla händelsehubbar i ett namnområde överskrider sammanställda genomflöde enhet bidrag, mottagare har begränsats och felmeddelanden som anger att den utgående kvoten har överskridits. Ingång och utgång kvoter tillämpas separat, så att ingen avsändare kan orsaka händelsekonsumtion långsammare eller en mottagare förhindra händelser skickas i en händelsehubb.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Finns det en gräns för det antal Throughput Units som kan väljas?
Det finns en standardkvot på 20 genomflödesenheter per namnområde. Du kan begära en större kvot på enheter genom att arkivera ett supportärende. Den gräns på enhet 20 genomflödesenheter är paket tillgängliga i 20 och 100 genomflödesenheter. Observera att använda fler än 20 genomflödesenheter tar bort möjligheten att ändra antalet enheter utan att arkivera ett supportärende.

Med hjälp av den [automatiskt öka](event-hubs-auto-inflate.md) -funktionen kan du automatiskt öka antalet genomflödesenheter som ökar din användning.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan jag använda en AMQP-anslutning för att skicka och ta emot från flera händelsehubbar?
Ja, så länge som alla händelsehubbar finns i samma namnområde.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Vad är den högsta bevarandeperioden för händelser?
Event Hubs standardnivån stöder för närvarande en högsta loggperioden 7 dagar. Observera att händelsehubbar inte är avsedda för permanent datalagring. Mer än 24 timmar kvarhållningsperioder är avsedda för scenarier där det är praktiskt att spela upp en händelseström i samma system. till exempel för att träna eller kontrollera en ny maskininlärningsmodell på befintliga data. Om du behöver meddelandet kvarhållning utöver 7 dagar, så att [Event Hubs avbilda](event-hubs-capture-overview.md) på din händelse hubb hämtar data från din händelsehubb till lagringskonto eller Azure Data Lake-tjänstkontot du väljer. Aktivera avbilda ådrar sig en kostnad som baseras på dina inköpta genomflödesenheter.

### <a name="where-is-azure-event-hubs-available"></a>Där är Händelsehubbar i Azure?

Händelsehubbar i Azure är tillgänglig i alla regioner som stöds Azure. En lista över finns det [Azure-regioner](https://azure.microsoft.com/regions/) sidan.  

## <a name="best-practices"></a>Bästa praxis

### <a name="how-many-partitions-do-i-need"></a>Hur många partitioner behöver jag?

Observera att antalet partitioner i en händelsehubb inte kan ändras efter installationen. Det är viktigt att tänka om hur många partitioner som du behöver för att komma igång med detta i åtanke. 

Händelsehubbar är utformad för att tillåta en enskild partition läsare per konsumentgrupp. Standardinställningen för fyra partitioner är tillräcklig för de flesta användningsområden. Om du vill skala din händelsebearbetning kanske du vill överväga att lägga till ytterligare partitioner. Det finns ingen gräns för specifika genomströmning på en partition, men sammanställda genomflöde i namnområdet begränsas av antalet genomflödesenheter. Om du ökar antalet genomflödesenheter i namnområdet du ytterligare partitioner för att tillåta läsare att få sina egna maximalt dataflöde.

Men om du har en modell där programmet har en mappning till en viss partition, kanske ökar antalet partitioner inte av en förmån för dig. Mer information om detta finns [tillgänglighet och konsekvens](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-more-pricing-information"></a>Var hittar mer information om priser?
Fullständig information om priser för Händelsehubbar finns i [Händelsehubbar prisinformationen](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Finns det en avgift för att bevara Händelsehubbar händelser för mer än 24 timmar?
Standardnivån för Event Hubs tillåter meddelandet kvarhållning punkter som är längre än 24 timmar för 7 dagar. Om storleken på det totala antalet lagrade händelser överskrider tillåten lagring för antalet valda genomflödesenheter (84 GB per genomflödesenhet), debiteras som överskrider tillåtna storleken på den publicerade Azure Blob storage hastigheten. Tillåten lagring i varje genomflödesenhet omfattar alla lagringskostnader för kvarhållningsperioder 24 timmar (standard) även om genomflödesenhet används till maximalt ingång ersättning.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hur lagringsstorlek Händelsehubbar beräknas och debiteras?
Den totala storleken på alla lagrade händelser, inklusive alla interna kostnader för händelsen huvuden eller på disk lagring strukturer i alla händelsehubbar mäts under dagen. Vid slutet av dagen beräknas den högsta lagringsstorleken. Den dagliga lagringskvoten beräknas baserat på det minsta antal dataflödesenheten som valdes under dagen (varje dataflödesenhet ger en kvot på 84 GB). Om den totala storleken överskrider tillåten för beräknade dagliga lagring, överdriven lagringen debiteras med Azure Blob storage-priser (på den **lokalt Redundant lagring** hastighet).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hur beräknas Händelsehubbar ingångshändelser?
Varje händelse skickas till en händelsehubb räknas som ett fakturerbar meddelande. En *ingång händelse* definieras som en enhet som är mindre än eller lika med 64 KB. Alla händelser som är mindre än eller lika med 64 KB stort anses vara en fakturerbar händelse. Om händelsen är större än 64 KB, beräknas antalet händelser som fakturerbar enligt händelsestorleken i multiplar av 64 KB. Till exempel en 8 KB händelse skickas till händelsehubben faktureras som en händelse, men en 96 KB meddelandet som skickades till händelsehubben faktureras som två händelser.

Händelser som används från en händelsehubb, samt hanteringsåtgärder och kontroll anrop, till exempel kontrollpunkter räknas inte som en fakturerbar ingångshändelser men påförs upp till genomflödet enhet ersättningen.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gäller asynkrona anslutning avgifter för Händelsehubbar
Anslutningen avgifter gäller bara när protokollet AMQP används. Du debiteras inga anslutningsavgifter för att skicka händelser via HTTP, oavsett antalet sändande system eller enheter. Om du planerar att använda AMQP (till exempel för att uppnå effektivare händelse strömning eller för att aktivera dubbelriktad kommunikation i IoT-kommandot och kontrollera scenarier) finns på [Händelsehubbar prisinformation](https://azure.microsoft.com/pricing/details/event-hubs/) sidan för ytterligare information om hur många anslutningar ingår i varje tjänstnivå.

### <a name="how-is-event-hubs-capture-billed"></a>Hur faktureras Event Hubs Capture?
Avbilda aktiveras när alla händelsehubb i namnområdet har aktiverat alternativet avbildning. Event Hubs avbilda faktureras timvis per köpta genomflödesenhet. Som antalet genomströmning enhet ökas eller minskas, visar Event Hubs avbilda fakturering ändringarna i steg om hela timme. Mer information om händelsen hubbar avbilda fakturering finns [Händelsehubbar prisinformation](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Jag debiteras för lagringskontot det Markera för Event Hubs Capture?
Avbilda använder ett lagringskonto som du anger när aktiverad på en händelsehubb. Eftersom det är ditt lagringskonto faktureras ändringar för den här konfigurationen till Azure-prenumeration.

## <a name="quotas"></a>Kvoter

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Finns det kvoter som är associerade med Händelsehubbar?

En lista över alla Händelsehubbar kvoter finns [kvoter](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Vilka är några av de undantag som genereras av Händelsehubbar och föreslagna åtgärder?

En lista över möjliga Händelsehubbar undantag, se [undantag översikt](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostikloggar

Händelsehubbar stöder två typer av [diagnostik loggar](event-hubs-diagnostic-logs.md) -avbilda felloggarna och operativa loggar - som representeras i json och kan aktiveras via Azure-portalen.

### <a name="support-and-sla"></a>Support och servicenivåavtal

Teknisk support för Händelsehubbar är tillgänglig via den [community-forumen](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Support för fakturering och prenumerationshantering ges utan kostnad.

Läs mer om våra SLA i den [serviceavtal](https://azure.microsoft.com/support/legal/sla/) sidan.

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Event Hubs Auto-öka](event-hubs-auto-inflate.md)
