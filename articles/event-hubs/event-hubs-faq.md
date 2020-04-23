---
title: Vanliga frågor och svar – Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller en lista över vanliga frågor och svar (FAQ) för Azure Event Hubs och deras svar.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 7f6e1896c97c96cd484d15fb9e6a3056e5c5d6b2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086376"
---
# <a name="event-hubs-frequently-asked-questions"></a>Evenemangshubbar ställde vanliga frågor

## <a name="general"></a>Allmänt

### <a name="what-is-an-event-hubs-namespace"></a>Vad är ett namnområde för händelsehubbar?
Ett namnområde är en omfångsbehållare för Event Hub/Kafka-ämnen. Det ger dig en unik [FQDN.](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) Ett namnområde fungerar som en programbehållare som kan innehålla flera Event Hub/Kafka-ämnen. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>När skapar jag ett nytt namnområde jämfört med att använda ett befintligt namnområde?
Kapacitetsallokering[(dataflödesenheter )](#throughput-units)faktureras på namnområdesnivå. Ett namnområde är också associerat med en region.

Du kanske vill skapa ett nytt namnområde i stället för att använda ett befintligt i något av följande scenarion: 

- Du behöver en händelsehubb som är associerad med en ny region.
- Du behöver en eventnav som är associerad med en annan prenumeration.
- Du behöver en eventnav med en distinkt kapacitetsallokering (det vill säga kapacitetsbehovet för namnområdet med den tillagda händelsehubben skulle överskrida tröskelvärdet 40 TU och du vill inte satsa på det dedikerade klustret)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Vad är skillnaden mellan basic-nivåerna event hubs och standardnivåer?

Standardnivån för Azure Event Hubs innehåller funktioner utöver vad som är tillgängligt på basic-nivån. Följande funktioner ingår i Standard:

* Längre bevarande av händelser
* Ytterligare förmedlade anslutningar, med en överkörningsavgift för mer än det antal som ingår
* Mer än en enda [konsumentgrupp](event-hubs-features.md#consumer-groups)
* [Fånga](event-hubs-capture-overview.md)
* [Kafka integration](event-hubs-for-kafka-ecosystem-overview.md)

Mer information om prisnivåer, inklusive dedikerade eventhubbar finns i [prisinformationen för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Var är Azure Event Hubs tillgängliga?

Azure Event Hubs är tillgängligt i alla Azure-regioner som stöds. En lista finns på sidan [Azure-regioner.](https://azure.microsoft.com/regions/)  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan jag använda en enda AMQP-anslutning för att skicka och ta emot från flera händelsehubbar?

Ja, så länge alla händelsehubbar finns i samma namnområde.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Vilken är den maximala kvarhållningsperioden för händelser?

Event Hubs Standard-nivån stöder för närvarande en maximal kvarhållningsperiod på sju dagar. Händelsehubbar är inte avsedda som ett permanent datalager. Kvarhållningsperioder som är längre än 24 timmar är avsedda för scenarier där det är praktiskt att spela upp en händelseström till samma system. till exempel för att träna eller verifiera en ny maskininlärningsmodell på befintliga data. Om du behöver lagring av meddelanden efter sju dagar hämtar [du](event-hubs-capture-overview.md) data från händelsehubben till det lagringskonto eller Azure Data Lake Service-konto som du väljer. Aktivera Capture medför en avgift baserat på dina inköpta dataflödesenheter.

Du kan konfigurera lagringsperioden för de infångade data på ditt lagringskonto. **Livscykelhanteringsfunktionen** i Azure Storage erbjuder en omfattande, regelbaserad princip för allmänna v2- och bloblagringskonton. Använd principen för att överföra dina data till lämpliga åtkomstnivåer eller upphör att gälla i slutet av datans livscykel. Mer information finns [i Hantera livscykeln för Azure Blob-lagring](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Hur övervakar jag mina eventhubbar?
Event Hubs avger uttömmande mått som ger tillståndet för dina resurser till [Azure Monitor](../azure-monitor/overview.md). De låter dig också bedöma den övergripande hälsan för eventhubbar-tjänsten inte bara på namnområdesnivå utan även på entitetsnivå. Läs mer om vilken övervakning som erbjuds för [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Vilka portar behöver jag för att öppna i brandväggen? 
Du kan använda följande protokoll med Azure Service Bus för att skicka och ta emot meddelanden:

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Se följande tabell för de utgående portar som du behöver öppna för att använda dessa protokoll för att kommunicera med Azure Event Hubs. 

| Protokoll | Portar | Information | 
| -------- | ----- | ------- | 
| AMQP | 5671 och 5672 | Se [AMQP-protokollguide](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Se [Använda händelsehubbar från Kafka-program](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Vilka IP-adresser behöver jag för att vitlista?
Så här hittar du rätt IP-adresser till den vita listan för dina anslutningar:

1. Kör följande kommando från en kommandotolk: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Notera den IP-adress `Non-authoritative answer`som returneras i . Den enda gången det skulle ändras är om du återställer namnområdet till ett annat kluster.

Om du använder zonredundansen för ditt namnområde måste du göra några ytterligare steg: 

1. Först kör du nslookup på namnområdet.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anteckna namnet i avsnittet **icke-auktoritärt svar,** som är i något av följande format: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Kör nslookup för var och en med suffix s1, s2 och s3 för att hämta IP-adresserna för alla tre instanser som körs i tre tillgänglighetszoner, 

## <a name="apache-kafka-integration"></a>Apache Kafka integration

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hur integrerar jag mitt befintliga Kafka-program med Event Hubs?
Event Hubs innehåller en Kafka-slutpunkt som kan användas av dina befintliga Apache Kafka-baserade program. En konfigurationsändring är allt som krävs för att ha PaaS Kafka-upplevelsen. Det ger ett alternativ till att köra din egen Kafka kluster. Event Hubs stöder Apache Kafka 1.0 och nyare klientversioner och fungerar med dina befintliga Kafka-program, verktyg och ramverk. Mer information finns i [Event Hubs for Kafka repo](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Vilka konfigurationsändringar måste göras för att mitt befintliga program ska kunna prata med Event Hubs?
Om du vill ansluta till en händelsehubb måste du uppdatera Kafka-klientkonfigurationerna. Det görs genom att skapa ett namnområde för eventhubbar och hämta [anslutningssträngen](event-hubs-get-connection-string.md). Ändra bootstrap.servers för att peka på Event Hubs FQDN och porten till 9093. Uppdatera sasl.jaas.config för att dirigera Kafka-klienten till slutpunkten Event Hubs (vilket är den anslutningssträng du har fått), med korrekt autentisering som visas nedan:

bootstrap.servers={YOUR. EVENTHUBS. FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule krävs användarnamn="$ConnectionString" password="{YOUR. EVENTHUBS. Anslutning. STRING}";

Exempel:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule krävs användarnamn="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName=DummyAccessKeyName; SharedAccessKeyName=DummyAccessKeyName; SharedAccessKeyName=DummyAccessKeyName; SharedAccess SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Om sasl.jaas.config inte är en konfiguration som stöds i ramverket letar du reda på de konfigurationer som används för att ange SASL-användarnamnet och lösenordet och använda dem i stället. Ställ in användarnamnet på $ConnectionString och lösenordet till anslutningssträngen för eventhubbar.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Vad är meddelande-/händelsestorleken för eventhubbar?
Den maximala meddelandestorleken för eventhubbar är 1 MB.

## <a name="throughput-units"></a>Genomflödesenheter

### <a name="what-are-event-hubs-throughput-units"></a>Vad är dataflödesenheter för eventhubbar?
Dataflöde i händelsehubbar definierar mängden data i megabyte eller antalet (i tusental) 1 KB-händelser som går in och utgår via eventhubbar. Det här dataflödet mäts i dataflödesenheter (TUs). Köp TUs innan du kan börja använda tjänsten Event Hubs. Du kan uttryckligen välja T:er för eventhubbar antingen genom att använda mallar för resurshanteraren för portaler eller eventhubbar. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Gäller dataflödesenheter för alla händelsehubbar i ett namnområde?
Ja, dataflödesenheter (TUs) gäller för alla händelsehubbar i ett namnområde för händelsehubbar. Det innebär att du köper RU:er på namnområdesnivå och delas mellan händelsehubbar under det namnområdet. Varje TU berättigar namnområdet till följande funktioner:

- Upp till 1 MB per sekund av inträngningshändelser (händelser som skickas till en händelsehubb), men inte mer än 1 000 ingående händelser, hanteringsåtgärder eller kontroll API-anrop per sekund.
- Upp till 2 MB per sekund av utgående händelser (händelser som förbrukas från en händelsehubb), men inte mer än 4096 utgående händelser.
- Upp till 84 GB händelselagring (tillräckligt för standardlagringsperioden på 24 timmar).

### <a name="how-are-throughput-units-billed"></a>Hur faktureras dataflödesenheter?
Dataflödesenheter faktureras per timme. Faktureringen baseras på det maximala antalet enheter som har valts under den angivna timmen. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hur kan jag optimera användningen på mina dataflödesenheter?
Du kan börja så lågt som en dataflödesenhet (TU) och aktivera [automatisk uppblåsning](event-hubs-auto-inflate.md). Med funktionen för automatisk uppblåsning kan du utöka dina T:er när trafiken/nyttolasten ökar. Du kan också ange en övre gräns för antalet RU:er.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hur fungerar funktionen För automatisk uppblåsning i Event Hubs?
Med funktionen för automatisk uppblåsning kan du skala upp dina dataflödesenheter . Det innebär att du kan börja med att köpa låga RU: er och automatisk blåsa skala upp dina TUs som din inträngning ökar. Det ger dig ett kostnadseffektivt alternativ och fullständig kontroll över antalet TUs att hantera. Den här funktionen är en **skala upp-funktion** och du kan helt styra nedskalningen av antalet ru:er genom att uppdatera den. 

Du kanske vill börja med låg dataflödesenheter (TUs), till exempel 2 RU: er. Om du förutspår att trafiken kan växa till 15 RU: er, aktivera funktionen för automatisk uppblåsning på ditt namnområde och ställ in maxgränsen till 15 RU:er. Du kan nu utöka dina RU:er automatiskt när trafiken växer.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Finns det en kostnad i samband med när jag slår på funktionen för automatisk uppblåsning?
Det kostar **inget** med den här funktionen. 

### <a name="how-are-throughput-limits-enforced"></a>Hur tillämpas dataflödesbegränsningar?
Om det totala ingressflödet eller den totala ingresshändelsefrekvensen för alla händelsehubbar i ett namnområde överskrider de sammanlagda dataflödesenhetsesättningarna begränsas avsändare och får fel som anger att ingående kvoten har överskridits.

Om det totala utgående dataflödet eller den totala utgående händelsefrekvensen för alla händelsehubbar i ett namnområde överskrider de sammanlagda dataflödesenhetsesättningarna begränsas mottagarna och får fel som anger att utgående kvoten har överskridits. Ingående och utgående kvoter tillämpas separat, så att ingen avsändare kan orsaka att händelseförbrukningen saktar ned, och inte heller kan en mottagare förhindra att händelser skickas till en händelsehubb.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Finns det en gräns för hur många dataflödesenheter som kan reserveras/väljas?
På ett erbjudande med flera innehavare kan dataflödesenheter växa upp till 40 RU:er (du kan välja upp till 20 RU:er i portalen och höja en supportbiljett för att höja den till 40 RU:er på samma namnområde). Utöver 40 RU:er erbjuder eventhubbar den resurs-/kapacitetsbaserade modell som kallas **Event Hubs Dedicated clusters**. Dedikerade kluster säljs i kapacitetsenheter (CUs).

## <a name="dedicated-clusters"></a>Dedikerade kluster

### <a name="what-are-event-hubs-dedicated-clusters"></a>Vad är Event Hubs dedikerade kluster?
Event Hubs Dedikerade kluster erbjuder distributioner med en enda klient för kunder med de mest krävande kraven. Det här erbjudandet bygger ett kapacitetsbaserat kluster som inte är bundet av dataflödesenheter. Det innebär att du kan använda klustret för att inta och strömma dina data enligt klustrets cpu och minnesanvändning. Mer information finns i [Dedikerade kluster för händelsehubbar](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Hur mycket tillåter en enda kapacitetsenhet mig att uppnå?
För ett dedikerat kluster beror hur mycket du kan inta och strömma på olika faktorer som dina producenter, konsumenter, i vilken takt du intag och bearbetning, och mycket mer. 

Följande tabell visar de benchmark-resultat som vi uppnådde under vår testning:

| Nyttolastform | Mottagare | Bandbredd för inträngning| Inkommande meddelanden | Bandbredd för utgående inträngning | Utgående meddelanden | Totalt antal ru: er | TUs per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partier av 100x1KB | 2 | 400 MB/sek | 400k meddelanden/sek | 800 MB/sek | 800k meddelanden/sek | 400 TUs | 100 TUs | 
| Partier av 10x10KB | 2 | 666 MB/sek | 66.6k meddelanden/sek | 1,33 GB/sek | 133k meddelanden per sekund | 666 TUs | 166 TUs |
| Partier av 6x32KB | 1 | 1,05 GB/sek | 34k meddelanden / sek | 1,05 GB/sek | 34k meddelanden per sekund | 1000 TUs | 250 TUs |

I testet användes följande kriterier:

- Ett dedikerat Event Hubs-kluster med fyra kapacitetsenheter (CUs) användes. 
- Händelsehubben som användes för inmatning hade 200 partitioner. 
- De data som intas togs emot av två mottagarprogram som tog emot från alla partitioner.

Resultaten ger dig en uppfattning om vad som kan uppnås med ett dedikerat Event Hubs-kluster. Dessutom levereras ett dedikeringskluster med händelsehubbar capture aktiverat för dina mikrobatch- och långsiktiga lagringsscenarier.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hur skapar jag ett dedikerat kluster för händelsehubbar?
Du skapar ett dedikerat kluster för händelsehubbar genom att skicka en [supportbegäran för kvotökning](https://portal.azure.com/#create/Microsoft.Support) eller genom att kontakta [eventhubs-teamet](mailto:askeventhubs@microsoft.com). Det tar vanligtvis ungefär två veckor att få klustret distribuerat och överlämnat för att användas av dig. Den här processen är tillfällig tills en fullständig självbetjäning görs tillgänglig via Azure-portalen.

## <a name="best-practices"></a>Bästa praxis

### <a name="how-many-partitions-do-i-need"></a>Hur många partitioner behöver jag?
Antalet partitioner anges när de skapas och måste vara mellan 2 och 32. Partitionsantalet kan inte ändras, så du bör överväga långsiktig skala när du ställer in partitionsantal. Partitioner är en mekanism för organisering av data som har att göra med vilken underordnad parallellitet som krävs i de program som används. Antalet partitioner i en händelsehubb är direkt kopplat till antalet samtidiga läsare som du förväntar dig. Mer information om partitioner finns i [Partitioner](event-hubs-features.md#partitions).

Du kanske vill ställa in det så att det är det högsta möjliga värdet, vilket är 32, vid tidpunkten för skapandet. Kom ihåg att om du har mer än en partition kommer att resultera i händelser som skickas till flera partitioner utan att behålla ordningen, såvida du inte konfigurerar avsändare för att bara skicka till en enda partition av de 32 lämnar de återstående 31 partitionerna överflödiga. I det förra fallet måste du läsa händelser över alla 32 partitioner. I det senare fallet finns det ingen uppenbar extra kostnad förutom den extra konfiguration du måste göra på Event Processor Host.

Event Hubs är utformat för att tillåta en enskild partitionsläsare per konsumentgrupp. I de flesta användningsfall är standardinställningen för fyra partitioner tillräcklig. Om du vill skala din händelsebearbetning kan du överväga att lägga till ytterligare partitioner. Det finns ingen specifik dataflödesgräns på en partition, men det sammanlagda dataflödet i namnområdet begränsas av antalet dataflödesenheter. När du ökar antalet dataflödesenheter i namnområdet kanske du vill ha ytterligare partitioner så att samtidiga läsare kan uppnå sitt eget maximala dataflöde.

Men om du har en modell där ditt program har en tillhörighet till en viss partition, kan det hända att öka antalet partitioner inte vara till någon nytta för dig. Mer information finns i [tillgänglighet och konsekvens](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-more-pricing-information"></a>Var hittar jag mer prisinformation?

Fullständig information om priser för eventhubbar finns i [prisinformationen för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Finns det en avgift för att behålla eventhubbar i mer än 24 timmar?

Standardnivån Event Hubs tillåter lagringsperioder för meddelanden som är längre än 24 timmar, i högst sju dagar. Om storleken på det totala antalet lagrade händelser överstiger lagringsersättningen för antalet valda dataflödesenheter (84 GB per dataflödesenhet), debiteras storleken som överskrider ersättningen enligt den publicerade Azure Blob-lagringshastigheten. Lagringsavdraget i varje dataflödesenhet täcker alla lagringskostnader för lagringsperioder på 24 timmar (standard) även om dataflödesenheten används upp till det maximala inträngningsbidraget.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hur beräknas och debiteras lagringsstorleken för eventhubbar?

Den totala storleken på alla lagrade händelser, inklusive interna omkostnader för händelsehuvuden eller disklagringsstrukturer i alla händelsehubbar, mäts under hela dagen. I slutet av dagen beräknas den högsta lagringsstorleken. Daglagringstillägget beräknas baserat på det minsta antalet dataflödesenheter som valdes under dagen (varje dataflödesenhet ger en ersättning på 84 GB). Om den totala storleken överstiger det beräknade dagliga lagringstillägget debiteras det överskjutande lagringsutrymmet med azure blob-lagringshastigheter (med den **lokalt redundanta lagringshastigheten).**

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hur beräknas händelsehubbar ingresshändelser?

Varje händelse som skickas till en händelsehubb räknas som ett fakturerbart meddelande. En *ingresshändelse* definieras som en dataenhet som är mindre än eller lika med 64 KB. Alla händelser som är mindre än eller lika med 64 KB i storlek anses vara en fakturerbar händelse. Om händelsen är större än 64 kB beräknas antalet fakturerbara händelser enligt händelsestorleken, i multiplar på 64 kB. Till exempel faktureras en 8 KB-händelse som skickas till händelsehubben som en händelse, men ett 96 KB-meddelande som skickas till händelsehubben faktureras som två händelser.

Händelser som förbrukas från en händelsenav, samt hanteringsåtgärder och kontrollanrop som kontrollpunkter, räknas inte som fakturerbara ingresshändelser, utan tillfaller upp till dataflödesenhetserkmånen.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gäller mäklererade anslutningsavgifter för eventhubbar?

Anslutningsavgifter gäller endast när AMQP-protokollet används. Det finns inga anslutningsavgifter för att skicka händelser med HTTP, oavsett antalet sändande system eller enheter. Om du planerar att använda AMQP (till exempel för att uppnå effektivare händelseströmning eller för att aktivera dubbelriktad kommunikation i IoT-kommando- och kontrollscenarier) läser du [prisinformationssidan](https://azure.microsoft.com/pricing/details/event-hubs/) för Event Hubs för mer information om hur många anslutningar som ingår i varje tjänstnivå.

### <a name="how-is-event-hubs-capture-billed"></a>Hur faktureras Event Hubs Capture?

Hämtning aktiveras när en händelsehubb i namnområdet har alternativet Capture aktiverat. Event Hubs Capture faktureras per timme per köpt dataflödesenhet. När antalet dataflödesenheter ökas eller minskas, återspeglar Event Hubs Capture-fakturering dessa ändringar i hela timstegen. Mer information om eventhubbar fånga fakturering finns i [prisinformation för eventhubbar](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Faktureras jag för det lagringskonto som jag väljer för Fånga händelserbubbar?

Capture använder ett lagringskonto som du anger när det är aktiverat på en händelsehubb. Eftersom det är ditt lagringskonto faktureras alla ändringar för den här konfigurationen till din Azure-prenumeration.

## <a name="quotas"></a>Kvoter

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Finns det några kvoter som är associerade med eventhubbar?

En lista över alla kvoter för händelsehubbar finns i [kvoter](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Varför kan jag inte skapa ett namnområde när jag har tagit bort det från en annan prenumeration? 
När du tar bort ett namnområde från en prenumeration väntar du i 4 timmar innan du återskapar det med samma namn i en annan prenumeration. Annars kan följande felmeddelande visas: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Vilka är några av de undantag som genereras av Event Hubs och deras föreslagna åtgärder?

En lista över möjliga undantag för händelsehubbar finns i [översikt över undantag](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostikloggar

Event Hubs stöder två typer av [diagnostikloggar](event-hubs-diagnostic-logs.md) - Capture felloggar och driftloggar - som båda representeras i json och kan aktiveras via Azure-portalen.

### <a name="support-and-sla"></a>Support och SLA

Teknisk support för Event Hubs är tillgänglig via [community-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Support för fakturerings- och prenumerationshantering tillhandahålls utan kostnad.

Mer information om vårt serviceavtal finns på sidan [Servicenivåavtal.](https://azure.microsoft.com/support/legal/sla/)

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Händelserhubbar automatiskt blås upp](event-hubs-auto-inflate.md)
