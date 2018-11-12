---
title: Azure Event Hubs vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor (och svar FAQ) om Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 10/07/2018
ms.author: shvija
ms.openlocfilehash: 9bd1649c0ec89abf660865971314becb6580852f
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300683"
---
# <a name="event-hubs-frequently-asked-questions"></a>Vanliga och frågor svar om Event Hubs

## <a name="general"></a>Allmänt

### <a name="what-is-an-event-hubs-namespace"></a>Vad är ett namnområde för Event Hubs?
Ett namnområde är en gemensam behållare för Event Hub/Kafka-ämnen. Då får du ett unikt [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Ett namnområde fungerar som en programbehållare som kan innehålla flera Event Hub/Kafka-ämnen. 

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Vad är skillnaden mellan Event Hubs Basic och Standard-nivån?

Standardnivån för Azure Event Hubs tillhandahåller funktioner utöver vad som är tillgängligt i Basic-nivån. Följande funktioner ingår i Standard:

* Längre kvarhållning för händelse
* Fler asynkrona anslutningar med en extra avgift för mer än det antal som ingår
* Mer än en enda [konsumentgrupp](event-hubs-features.md#consumer-groups)
* [samla in](event-hubs-capture-overview.md)
* [Kafka-integrering](event-hubs-for-kafka-ecosystem-overview.md)

Läs mer om prissättning för nivåerna, inklusive Event Hubs Dedicated den [Händelsehubbar prisinformation](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Var finns Azure Event Hubs?

Händelsehubbar i Azure är tillgängligt i alla regioner som stöds Azure. En lista på den [Azure-regioner](https://azure.microsoft.com/regions/) sidan.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan jag använda en enkel AMQP-anslutning för att skicka och ta emot från flera event hubs?

Ja, så länge alla event hubs finns i samma namnområde.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Vad är den högsta bevarandeperioden för händelser?

Event Hubs Standard-nivån stöder för närvarande en högsta kvarhållningsperiod på sju dagar. Händelsehubbar är inte avsedda för permanent datalagring. Lagringstider som är mer än 24 timmar avses för scenarier där det är lämpligt att spela upp en händelseström till samma system; till exempel att finjustera eller verifiera en ny maskininlärningsmodell på befintliga data. Om du behöver kvarhållning av meddelanden efter sju dagar, så att [Event Hubs Capture](event-hubs-capture-overview.md) på din event hub hämtar data från din event hub i Storage-konto eller Azure Data Lake-tjänstkonto valfritt. Avbildningsfunktionen utgår en avgift baserat på dina köpta genomflödesenheterna.

### <a name="how-do-i-monitor-my-event-hubs"></a>Hur övervakar jag min Event Hubs?
Händelsehubbar genererar uttömmande mått som anger tillståndet för dina resurser till [Azure Monitor](../azure-monitor/overview.md). De också kan du utvärdera den övergripande hälsan för Event Hubs-tjänsten inte bara på namnområdesnivå utan även på enhetsnivå. Läs om vilka övervakning erbjuds för [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

## <a name="apache-kafka-integration"></a>Apache Kafka-integrering

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hur integrerar jag mina befintliga Kafka-program med Event Hubs?
Event Hubs ger en Kafka-slutpunkt som kan användas av befintliga Apache Kafka-baserade program. En konfigurationsändring är allt som krävs för att ha PaaS Kafka-upplevelse. Det är ett alternativ till att köra dina egna Kafka-kluster. Händelsehubbar har stöd för Apache Kafka 1.0 och nyare klientversioner och fungerar med din befintliga Kafka program, verktyg och ramverk. Mer information finns i [Händelsehubbar för Kafka-lagringsplatsen](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Konfigurationsändringar behöver göras för mitt befintliga program att kommunicera med Event Hubs?
För att ansluta till en Händelsehubb för Kafka-aktiverad, behöver du uppdatera på konfigurationerna för Kafka-klienten. Detta görs genom att skapa ett namnområde för Event Hubs och hämta den [anslutningssträngen](event-hubs-get-connection-string.md). Ändra bootstrap.servers genom att peka 9093 Event Hubs-FQDN och port. Uppdatera sasl.jaas.config för att dirigera Kafka-klient till Kafka-aktiverade Event Hubs-slutpunkten (vilket är den sträng som du har fått), med rätt autentisering enligt nedan:

bootstrap.Servers={Your. EVENTHUBS. FQDN}: 9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule krävs användarnamn = ”$ConnectionString” lösenord = ”{YOUR. EVENTHUBS. ANSLUTNING. STRÄNGEN} ”;

Exempel:

bootstrap.Servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule krävs användarnamn = ”$ ConnectionString ”password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ”;

Obs: Om sasl.jaas.config inte är en konfiguration som stöds i ditt ramverk kan hitta de konfigurationer som används för att ange SASL-användarnamn och lösenord och använda dem i stället. Ange användarnamnet till $ConnectionString och lösenord för att anslutningssträngen för Event Hubs.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Vad är meddelandehändelse/storleken för Kafka-aktiverade Event Hubs?
Den maximala tillåtna storleken för Kafka-aktiverade Händelsehubbar är 1MB.

## <a name="throughput-units"></a>Genomflödesenheter

### <a name="what-are-event-hubs-throughput-units"></a>Vad är throughput units för Event Hubs?
Dataflöde i Event Hubs definierar hur mycket data i megabyte eller antalet händelser som 1 KB (i tusental) den ingående och utgående via Event Hubs. Den här dataflödet mäts i dataflödesenheter (Dataflödesenheter). Köpa Dataflödesenheter innan du kan börja använda Event Hubs-tjänsten. Du kan uttryckligen välja Event Hubs-Dataflödesenheter antingen med hjälp av portalen eller Event Hubs Resource Manager-mallar. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Throughput units gäller för alla händelsehubbar i ett namnområde?
Ja, throughput units (Dataflödesenheter som) gäller för alla händelsehubbar i ett namnområde för Event Hubs. Det innebär att du köper Dataflödesenheter på namnområdesnivå och delas med event hubs under det här namnområdet. Varje Dataflödesenheter berättigar namnrymden till följande funktioner:

- Upp till 1 MB per sekund för ingångshändelser (händelser skickas till ett händelsenav), men inga fler än 1000 ingångshändelser, hanteringsåtgärder eller kontrollen API-anrop per sekund.
- Upp till 2 MB per sekund för utgångshändelser (händelser som konsumeras från ett händelsenav), men inte mer än 4096 utgångshändelser.
- Upp till 84 GB händelselagring (tillräckligt för standardlagringstid 24-timmarsformat).

### <a name="how-are-throughput-units-billed"></a>Hur faktureras enheter?
Dataflödesenheter (Dataflödesenheter), faktureras per timme. Faktureringen baseras på det maximala antalet enheter som valdes under timmen. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hur kan jag optimera användningen på min dataflödesenheter?
Du kan starta så lite som en genomflödesenhet (Dataflödesenheter) och aktivera [automatisk ökning](event-hubs-auto-inflate.md). Den automatisk ökning funktionen kan du utöka din Dataflödesenheter när din trafik/nyttolast ökar. Du kan också ange en övre gräns för antalet Dataflödesenheter.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hur fungerar automatisk ökning-funktionen i Event Hubs?
Den automatisk ökning av funktionen kan du skala upp dina dataflödesenheter (Dataflödesenheter). Det innebär att du kan börja genom att köpa låg Dataflödesenheter och automatisk ökning skalar upp dina Dataflödesenheter när dina ingångshändelser ökar. Det ger dig ett kostnadseffektivt alternativ och fullständig kontroll över antalet Dataflödesenheter som du hanterar. Den här funktionen är en **skala upp endast** funktionen och du kan helt styra skala ned antalet Dataflödesenheter genom att uppdatera den. 

Du kanske vill börja med låg dataflödesenheter (Dataflödesenheter), till exempel 2 Dataflödesenheter. Om du förutsäga att trafiken kan växa till 15 Dataflödesenheter, aktivera den automatisk ökning av funktionen på ditt namnområde och ange maxgränsen till 15 Dataflödesenheter. Du kan nu utöka dina Dataflödesenheter automatiskt allteftersom trafiken ökar.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Kostar det när du aktiverar den automatisk ökning av funktionen?
Det finns **utan kostnad** som är associerade med den här funktionen. 

### <a name="how-are-throughput-limits-enforced"></a>Hur övervakas dataflödesbegränsningar?
Om den totala ingångar eller den sammanlagda takten för ingångshändelser på alla händelsehubbar i ett namnområde överskrider den sammanlagda dataflödet enhet, avsändare begränsas och få felmeddelanden som anger att ingående kvoten har överskridits.

Om den totala genomströmningen av utgångar eller den sammanlagda takten för utgångshändelser på alla händelsehubbar i ett namnområde överskrider den sammanlagda dataflödet enhet mottagare begränsas och få felmeddelanden som anger att den utgående kvoten har överskridits. In- och utgångskvoterna upprätthålls separat, så att ingen avsändare kan orsaka att händelseförbrukningen, inte heller kan någon mottagare hindra att händelser skickas in till en händelsehubb.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Finns det en gräns för antal throughput units (Dataflödesenheter) som kan vara reserverade/valt?
Genomflödesenheter kan växa upp till 40 Dataflödesenheter (du kan välja upp till 20 Dataflödesenheter i portalen, och generera ett supportärende för att höja den till 40 Dataflödesenheter på samma namnområde) på flera innehavare som erbjuder. Utöver 40 Dataflödesenheter Händelsehubbar erbjuder resurs/kapacitet-baserade modellen kallas den **Event Hubs Dedicated-kluster**. Dedikerade kluster säljs i kapacitetsenheter (Cu: er).

## <a name="dedicated-clusters"></a>Dedikerade kluster

### <a name="what-are-event-hubs-dedicated-clusters"></a>Vad är Event Hubs Dedicated kluster?
Event Hubs Dedicated kluster erbjuder en enda klient distributioner för kunder med svåraste krav. Det här erbjudandet skapar ett kluster med kapacitet som inte är bunden av genomflödesenheter. Det innebär att du kan använda klustret för att mata in och strömma dina data enligt processor- och minnesanvändning i klustret. Mer information finns i [Event Hubs Dedicated-kluster](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Hur mycket tillåter en enda Kapacitetsenhet mig uppnå?
För ett dedikerat kluster beror hur mycket du kan mata in och strömma på olika faktorer, till exempel din producenter, konsumenter, den hastighet med vilken du mata in och bearbetning av och mycket mer. 

Följande tabell visas resultaten att vi har uppnått under våra tester:

| Nyttolasten form | Mottagare | Ingående bandbredd| Inkommande meddelanden | Utgående bandbredd | Utgående meddelanden | Totalt antal Dataflödesenheter | Dataflödesenheter per Kapacitetsenhet |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batchar med 100x1KB | 2 | 400 MB/sek | 400 k meddelanden/sek | 800 MB/sek | 800 k meddelanden/sek | 400 Dataflödesenheter | 100 Dataflödesenheter | 
| Batchar med 10x10KB | 2 | 666 MB/sek | 66.6 k meddelanden/sek | 1,33 GB/sek | 133 k meddelanden/sek | 666 Dataflödesenheter | 166 Dataflödesenheter |
| Batchar med 6x32KB | 1 | 1,05 GB/sek | 34 k meddelanden / sek | 1,05 GB/sek | 34 k meddelanden/sek | 1000 Dataflödesenheter | 250 Dataflödesenheter |

I testet användes följande kriterier:

- En dedikerad Event Hubs-kluster med fyra kapacitetsenheter (Cu: er) har använts. 
- Den händelsehubb som används för datainmatning hade 200 partitioner. 
- De data som har samlats in togs emot av två mottagare program tar emot från alla partitioner.

Resultaten ger dig en uppfattning om vad som kan uppnås med en dedikerad Event Hubs-kluster. Dessutom kan levereras ett tilldela-kluster med Event Hubs Capture aktiveras för dina scenarier i mindre batchar och långsiktig kvarhållning.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hur gör jag för att skapa ett Event Hubs Dedicated-kluster?
Du skapar en Event Hubs dedicated-kluster genom att skicka in en [supportförfrågan om kvot ökning](https://portal.azure.com/#create/Microsoft.Support) eller genom att kontakta den [Event Hubs-teamet](mailto:askeventhubs@microsoft.com). Det tar normalt två veckor för att hämta klustret distribueras och överlämnas till användas av dig. Den här processen är tillfällig tills en fullständig självbetjäning är tillgänglig via Azure portal eller Azure Resource Manager-mallar som tar ungefär två timmar för att distribuera klustret.

## <a name="best-practices"></a>Bästa praxis

### <a name="how-many-partitions-do-i-need"></a>Hur många partitioner behöver jag?

Antalet partitioner i en händelsehubb kan inte ändras efter installationen. Det är viktigt att tänka om hur många partitioner som du behöver för att komma igång med det i åtanke. 

Event Hubs har utformats så att en enskild partition läsare per konsumentgrupp. I de flesta användningar räcker standardinställningen för fyra partitioner. Om du vill skala ditt händelsebearbetning är kan du överväga att lägga till ytterligare partitioner. Det finns ingen dataflödesgräns för specifika på en partition, men den sammanlagda genomströmningen i namnområdet begränsas av antalet dataflödesenheter. När du ökar antalet dataflödesenheter i namnområdet, kan du ytterligare partitioner så att samtidiga läsare att uppnå sina egna maximalt dataflöde.

Men om du har en modell där programmet har en mappning till en viss partition kan öka antalet partitioner inte av en förmån för dig. Mer information finns i [tillgänglighet och konsekvens](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-more-pricing-information"></a>Var hittar jag mer information om priser?

Fullständig information om priser för Event Hubs finns i den [Händelsehubbar prisinformation](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Finns det en kostnad för att behålla Event Hubs-händelser i mer än 24 timmar?

Event Hubs Standard-nivån tillåter meddelandelagring punkter som är längre än 24 timmar innan högst sju dagar. Om storleken på det totala antalet lagrade händelser överstiger lagringskvoten för antalet dataflödesenheter som valts (84 GB per dataflödesenhet), debiteras den storlek som överstiger kvoten enligt taxan för publicerade Azure Blob-lagring. Lagringskvoten i varje throughput unit täcker alla lagringskostnader för lagringstider på 24 timmar (standard) även om dataflödesenheterna används till den maximala kvoten för ingångshändelser.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hur lagringsstorlek Händelsehubbar beräknas och debiteras?

Den totala storleken för alla lagrade händelser, inklusive eventuell intern tillhörande information för händelserubriker eller strukturer för lagring av disk på i alla händelsehubbar mäts under hela dagen. Vid slutet av dagen beräknas den högsta lagringsstorleken. Den dagliga lagringskvoten beräknas baserat på det minsta antal dataflödesenheten som valdes under dagen (varje dataflödesenhet ger en kvot på 84 GB). Om den totala storleken överstiger den dagliga lagringskvoten beräknade, debiteras lagringen som använder Azure Blob-lagringstaxor (vid den **lokalt Redundant lagring** pris).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hur beräknas ingångshändelser i Event Hubs?

Varje händelse som skickas till ett händelsenav räknas som ett faktureringsbart meddelande. En *ingångshändelse* definieras som en enhet med data som är mindre än eller lika med 64 KB. Varje händelse som är mindre än eller lika med 64 KB i storlek anses vara en fakturerbar händelse. Om händelsen är större än 64 KB beräknas antalet faktureringsbara händelser enligt händelsestorleken i multipler av 64 KB. Exempelvis kan en händelse på 8 KB som skickas till event hub faktureras som en händelse, men ett meddelande på 96 KB som skickas till event hub faktureras som två händelser.

Händelser som konsumeras från ett händelsenav, samt hanteringsåtgärder och kontrollen anropar som t.ex kontrollpunkter, räknas inte som fakturerbara ingångshändelser, men nivån upp till throughput unit-kvoten.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gäller brokered connection-avgifter för Event Hubs?

Connection-avgifter gäller endast när AMQP-protokollet används. Du debiteras inga anslutningsavgifter för att skicka händelser via HTTP, oavsett antalet sändande system eller enheter. Om du planerar att använda AMQP (t.ex, för att få effektivare händelseströmning eller för att aktivera tvåvägskommunikation i IoT-kommando och kontroll scenarier) kan du läsa den [information om priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/) för information om hur många anslutningar som ingår i varje tjänstnivå.

### <a name="how-is-event-hubs-capture-billed"></a>Hur faktureras Event Hubs Capture?

Capture aktiveras när en händelsehubb i namnområdet har avbildningsalternativet aktiverat. Event Hubs Capture faktureras per timme per köpt dataflödesenhet. När antalet dataflödesenheter ökar eller minskar, återspeglar Event Hubs Capture fakturering ändringarna i hela timmar. Läs mer om Event Hubs Capture fakturering [information om priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Faktureras jag för det lagringskonto jag väljer för Event Hubs Capture?

Capture använder ett lagringskonto som du anger när du aktiverat på en händelsehubb. Eftersom det är ditt lagringskonto faktureras ändringar för den här konfigurationen på Azure-prenumerationen.

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
