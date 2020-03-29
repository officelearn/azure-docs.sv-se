---
title: Jämför Azure Storage-köer och servicebussköer
description: Analyserar skillnader och likheter mellan två typer av köer som erbjuds av Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: 8379b7f48e7e494370f3fdba81676d34821d7b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563385"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage-köer och Service Bus-köer – jämförelser och skillnader
I den här artikeln analyseras skillnaderna och likheterna mellan de två typerna av köer som erbjuds av Microsoft Azure idag: Lagringsköer och servicebussköer. Genom att använda denna information kan du jämföra och kontrastera respektive teknik och kunna fatta ett mer välgrundat beslut om vilken lösning som bäst uppfyller dina behov.

## <a name="introduction"></a>Introduktion
Azure stöder två typer av kömekanismer: **Lagringsköer** och **servicebussköer**.

**Lagringsköer**, som ingår i [Azure-lagringsinfrastrukturen,](https://azure.microsoft.com/services/storage/) har ett enkelt REST-baserat GET/PUT/PEEK-gränssnitt som ger tillförlitliga, beständiga meddelanden inom och mellan tjänster.

**Service Bus-köer** är en del av en bredare [Azure-meddelandeinfrastruktur](https://azure.microsoft.com/services/service-bus/) som stöder kö samt publicera/prenumerera och mer avancerade integrationsmönster. Mer information om Service Bus-köer/ämnen/prenumerationer finns i [översikten över Service Bus](service-bus-messaging-overview.md).

Medan båda köteknikerna finns samtidigt introducerades lagringsköer först, som en dedikerad kölagringsmekanism som bygger ovanpå Azure Storage-tjänster. Service Bus-köer bygger ovanpå den bredare meddelandeinfrastruktur som utformats för att integrera program eller programkomponenter som kan sträcka sig över flera kommunikationsprotokoll, datakontrakt, förtroendedomäner och/eller nätverksmiljöer.

## <a name="technology-selection-considerations"></a>Hänsyn till teknikurval
Både lagringsköer och servicebussköer är implementeringar av den meddelandekötjänst som för närvarande erbjuds av Microsoft Azure. Var och en har en något annorlunda uppsättning funktioner, vilket innebär att du kan välja det ena eller det andra, eller använda båda, beroende på behoven hos just din lösning eller affärs / tekniska problem du löser.

Vid fastställandet av vilken köteknik som passar syftet med en viss lösning bör lösningsarkitekter och utvecklare överväga dessa rekommendationer. Mer information finns i nästa avsnitt.

Som lösningsarkitekt/utvecklare **bör du överväga att använda lagringsköer** när:

* Ditt program måste lagra över 80 GB meddelanden i en kö.
* Ditt program vill spåra förloppet för bearbetning av ett meddelande i kön. Detta är användbart om arbetaren som bearbetar ett meddelande kraschar. En efterföljande arbetare kan sedan använda den informationen för att fortsätta från där den tidigare arbetaren slutade.
* Du behöver serverloggar för alla transaktioner som utförs mot dina köer.

Som lösningsarkitekt/utvecklare **bör du överväga att använda Service Bus-köer** när:

* Din lösning måste kunna ta emot meddelanden utan att behöva avsöka kön. Med Service Bus kan detta uppnås med hjälp av den långsökta mottagningsåtgärden med hjälp av de TCP-baserade protokoll som Service Bus stöder.
* Din lösning kräver att kön ger en garanterad första-i-första-ut (FIFO) beställd leverans.
* Din lösning måste kunna stödja automatisk dubblettidentifiering.
* Du vill att ditt program ska bearbeta meddelanden som parallella tidskrävande strömmar (meddelanden associeras med en ström med egenskapen [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) i meddelandet). I den här modellen konkurrerar varje nod i det tidskrävande programmet om strömmar, i motsats till meddelanden. När en dataström ges till en tidskrävande nod kan noden undersöka tillståndet för programströmstillståndet med hjälp av transaktioner.
* Din lösning kräver transaktionsbeteende och atomicitet när du skickar eller tar emot flera meddelanden från en kö.
* Programmet hanterar meddelanden som kan överskrida 64 kB men som troligen inte närmar sig gränsen på 256 KB.
* Du hanterar ett krav på att tillhandahålla en rollbaserad åtkomstmodell till köerna och olika rättigheter/behörigheter för avsändare och mottagare. Mer information finns i följande artiklar:
    - [Autentisera med hanterade identiteter](service-bus-managed-service-identity.md)
    - [Autentisera från ett program](authenticate-application.md)
* Storleken på din kö inte kommer att bli större än 80 GB.
* Du vill använda standardbaserade meddelandeprotokoll för AMQP 1.0. Mer information om AMQP finns i [Översikt över servicebuss AMQP](service-bus-amqp-overview.md).
* Du kan föreställa dig en eventuell migrering från köbaserad punkt-till-punkt-kommunikation till ett meddelandeutbytesmönster som möjliggör sömlös integrering av ytterligare mottagare (prenumeranter), som var och en tar emot oberoende kopior av antingen vissa eller alla meddelanden som skickas till kön. Det senare avser den publicerings-/prenumerationsfunktion som tillhandahålls av Service Bus.
* Din meddelandelösning måste kunna stödja leveransgarantin "Högst en gång" utan att du behöver bygga ytterligare infrastrukturkomponenter.
* Du vill kunna publicera och använda batchar av meddelanden.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Jämföra lagringsköer och servicebussköer
Tabellerna i följande avsnitt innehåller en logisk gruppering av köfunktioner och låter dig snabbt jämföra de funktioner som är tillgängliga i både Azure Storage-köer och Service Bus-köer.

## <a name="foundational-capabilities"></a>Grundläggande kapacitet
I det här avsnittet jämförs några av de grundläggande köfunktionerna som tillhandahålls av lagringsköer och servicebussköer.

| Jämförelsekriterier | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Beställningsgaranti |**Nej** <br/><br>Mer information finns i den första anteckningen i avsnittet "Ytterligare information".</br> |**Ja - Första-i-första-ut (FIFO)**<br/><br>(med hjälp av meddelandesessioner) |
| Leveransgaranti |**Minst en gång** |**Minst en (med** PeekLock-mottagningsläge - detta är standard) <br/><br/>**Högst en gång** (med mottagningsläge för Mottagning ochdele) <br/> <br/> Läs mer om olika [mottagningslägen](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Stöd för atomdrift |**Nej** |**Ja**<br/><br/> |
| Ta emot beteende |**Icke-blockerande**<br/><br/>(slutförs omedelbart om inget nytt meddelande hittas) |**Blockering med/utan timeout**<br/><br/>(erbjuder långa opinionsundersökningar, eller ["Kometteknik")](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Icke-blockerande**<br/><br/>(endast med hjälp av .NET-hanterat API) |
| API i push-stil |**Nej** |**Ja**<br/><br/>[OnMessage-](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) och **OnMessage-sessioner** .NET API. |
| Ta emot läge |**Peek & Lease** |**Kika & lås**<br/><br/>**Ta emot & Ta bort** |
| Exklusivt åtkomstläge |**Leasing-baserade** |**Lås-baserade** |
| Lånet/låslängden |**30 sekunder (standard)**<br/><br/>**7 dagar (max)** (Du kan förnya eller släppa ett meddelandelån med hjälp av [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API.) |**60 sekunder (standard)**<br/><br/>Du kan förnya ett [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) meddelandelås med RenewLock-API:et. |
| Precision för leasing/lås |**Meddelandenivå**<br/><br/>(varje meddelande kan ha ett annat timeout-värde, som du sedan kan uppdatera vid behov när meddelandet bearbetas, med hjälp av [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API) |**Könivå**<br/><br/>(varje kö har en låsprecision som tillämpas på alla dess meddelanden, men du kan förnya låset med Hjälp av RenewLock-API:et.) [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Batchad mottagning |**Ja**<br/><br/>(uttryckligen ange antalet meddelanden vid hämtning av meddelanden, upp till högst 32 meddelanden) |**Ja**<br/><br/>(implicit aktivera en förhämtningsegenskap eller uttryckligen genom användning av transaktioner) |
| Batchad skicka |**Nej** |**Ja**<br/><br/>(genom användning av transaktioner eller batchbearbetning på kundsidan) |

### <a name="additional-information"></a>Ytterligare information
* Meddelanden i lagringsköer är vanligtvis först-i-första ut, men ibland kan de vara ur funktion. till exempel när meddelandets tidsgräns för synlighet upphör att gälla (till exempel som ett resultat av att ett klientprogram kraschar under bearbetningen). När tidsgränsen för synligheten går ut visas meddelandet igen i kön så att en annan arbetare kan avköna den. Då kan det nyligen synliga meddelandet placeras i kön (som ska uteslutas igen) efter ett meddelande som ursprungligen var enqueued efter det.
* Det garanterade FIFO-mönstret i Service Bus-köer kräver användning av meddelandesessioner. I händelse av att programmet kraschar när ett meddelande som tas emot i **peek-&-låsläge,** nästa gång en kömottagare accepterar en meddelandesession, börjar det med det misslyckade meddelandet när den har gått in i TTL-perioden (Time-to-Live) nästa gång en kömottagare accepterar en meddelandesession.
* Lagringsköer är utformade för att stödja standardköscenarier, till exempel frikoppling av programkomponenter för att öka skalbarheten och toleransen för fel, belastningsutjämning och arbetsflöden för byggprocesser.
* Inkonsekvenser när det gäller meddelandehantering i samband med Service Bus-sessioner kan undvikas genom att använda sessionstillstånd för att lagra programmets tillstånd i förhållande till förloppet för hantering av sessionens meddelandesekvens och genom att använda transaktioner runt lösa mottagna meddelanden och uppdatera sessionstillståndet. Denna typ av konsekvens funktion är ibland märkt *Exakt-Once Processing* i andra leverantörens produkter, men transaktionsfel kommer naturligtvis att orsaka meddelanden som ska levereras och därför termen är inte exakt tillräcklig.
* Lagringsköer ger en enhetlig och konsekvent programmeringsmodell över köer, tabeller och BLOBs – både för utvecklare och för driftteam.
* Service Bus-köer ger stöd för lokala transaktioner i samband med en enda kö.
* Läget **Ta emot och ta bort** som stöds av Service Bus ger möjlighet att minska antalet meddelanden (och tillhörande kostnader) i utbyte mot sänkt leveranssäkerhet.
* Lagringsköer ger lån möjlighet att förlänga lånen för meddelanden. Detta gör det möjligt för arbetarna att upprätthålla korta leasingavtal på meddelanden. Om en arbetare kraschar kan meddelandet bearbetas snabbt igen av en annan arbetare. Dessutom kan en anställd förlänga lånet på ett meddelande om den behöver bearbeta det längre än den aktuella lånetiden.
* Lagringsköer erbjuder en tidsgränsen för synlighet som du kan ange när ett meddelande ska köas eller köas. Dessutom kan du uppdatera ett meddelande med olika lånevärden vid körning och uppdatera olika värden mellan meddelanden i samma kö. Timeout för servicebusslås definieras i kömetadata. Du kan dock förnya låset genom att anropa [Metoden RenewLock.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock)
* Den maximala timeouten för en blockerande mottagningsåtgärd i Service Bus-köer är 24 dagar. REST-baserade timeout har dock ett maximalt värde på 55 sekunder.
* Batchbearbetning på klientsidan som tillhandahålls av Service Bus gör det möjligt för en köklient att batcha flera meddelanden till en enda skicka-åtgärd. Batchbearbetning är endast tillgänglig för asynkrona sändningsåtgärder.
* Funktioner som 200 TB tak lagringsköer (mer när du virtualiserar konton) och obegränsade köer gör det till en idealisk plattform för SaaS-leverantörer.
* Lagringsköer ger en flexibel och högpresterande delegerad åtkomstkontrollmekanism.

## <a name="advanced-capabilities"></a>Avancerade funktioner
I det här avsnittet jämförs avancerade funktioner som tillhandahålls av lagringsköer och servicebussköer.

| Jämförelsekriterier | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Schemalagd leverans |**Ja** |**Ja** |
| Automatisk död bokstäver |**Nej** |**Ja** |
| Öka kötid till live-värde |**Ja**<br/><br/>(via uppdatering på plats av tidsgränsen för synlighet) |**Ja**<br/><br/>(tillhandahålls via en dedikerad API-funktion) |
| Stöd för Poison-meddelande |**Ja** |**Ja** |
| Uppdatering på plats |**Ja** |**Ja** |
| Transaktionslogg på serversidan |**Ja** |**Nej** |
| Lagringsmått |**Ja**<br/><br/>**Minutmått:** ger mätvärden i realtid för tillgänglighet, TPS, ANTAL API-anrop, antal fel och mer, allt i realtid (aggregerade per minut och rapporteras inom några minuter från vad som just hände i produktionen. Mer information finns i [Om lagringsanalysmått](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ja**<br/><br/>(massfrågor genom att ringa [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Tillståndshantering |**Nej** |**Ja**<br/><br/>[Microsoft.serviceBus.messaging.entityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.serviceBus.Messaging.entityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.entityStatus.sendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.entityStatus.receiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatisk vidarebefordran av meddelande |**Nej** |**Ja** |
| Rensa köfunktion |**Ja** |**Nej** |
| Meddelandegrupper |**Nej** |**Ja**<br/><br/>(med hjälp av meddelandesessioner) |
| Programtillstånd per meddelandegrupp |**Nej** |**Ja** |
| Dubblettidentifiering |**Nej** |**Ja**<br/><br/>(kan konfigureras på avsändarens sida) |
| Bläddra i meddelandegrupper |**Nej** |**Ja** |
| Hämta meddelandesessioner efter ID |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Båda köteknikerna gör det möjligt att schemalägga ett meddelande för leverans vid ett senare tillfälle.
* Med automatisk vidarebefordran av köer kan tusentals köer vidarebefordra sina meddelanden automatiskt till en enda kö, från vilken det mottagande programmet använder meddelandet. Du kan använda den här mekanismen för att uppnå säkerhet, kontrollflöde och isolera lagring mellan varje meddelandeutgivare.
* Lagringsköer ger stöd för uppdatering av meddelandeinnehåll. Du kan använda den här funktionen för beständig tillståndsinformation och inkrementella statusuppdateringar i meddelandet så att det kan bearbetas från den senast kända kontrollpunkten, i stället för att börja från början. Med Service Bus-köer kan du aktivera samma scenario med hjälp av meddelandesessioner. Med sessioner kan du spara och hämta programbearbetningstillståndet (med [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Obeställd lettering](service-bus-dead-letter-queues.md), som bara stöds av Service Bus-köer, kan vara användbart för att isolera meddelanden som inte kan bearbetas av det mottagande programmet eller när meddelanden inte kan nå sin destination på grund av en utgånget time-to-live (TTL) egenskap. TTL-värdet anger hur länge ett meddelande finns kvar i kön. Med Service Bus flyttas meddelandet till en särskild kö som kallas $DeadLetterQueue när TTL-perioden går ut.
* Om du vill hitta "gift"-meddelanden i lagringsköer undersöker programmet egenskapen [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) för meddelandet när du avsöker ett meddelande. Om **DequeueCount** är större än ett givet tröskelvärde flyttas meddelandet till en programdefinierad kö för obeställbara meddelanden.
* Med lagringsköer kan du få en detaljerad logg över alla transaktioner som utförs mot kön samt aggregerade mått. Båda dessa alternativ är användbara för felsökning och förståelse för hur ditt program använder lagringsköer. De är också användbara för att justera ditt program och minska kostnaderna för att använda köer.
* Begreppet "meddelandesessioner" som stöds av Service Bus gör det möjligt för meddelanden som tillhör en viss logisk grupp att associeras med en viss mottagare, vilket i sin tur skapar en sessionsliknande tillhörighet mellan meddelanden och deras respektive mottagare. Du kan aktivera den här avancerade funktionen i Service Bus genom att ange egenskapen [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) i ett meddelande. Mottagare kan sedan lyssna på ett visst sessions-ID och ta emot meddelanden som delar den angivna sessionsidentifieraren.
* Dupliceringsidentifieringsfunktionen som stöds av Service Bus-köer tar automatiskt bort dubblettmeddelanden som skickas till en kö eller ett ämne, baserat på värdet för egenskapen [MessageId.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)

## <a name="capacity-and-quotas"></a>Kapacitet och kvoter
I det här avsnittet jämförs lagringsköer och servicebussköer utifrån [kapacitet och kvoter](service-bus-quotas.md) som kan gälla.

| Jämförelsekriterier | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Maximal köstorlek |**500 TB**<br/><br/>(begränsad till en [enda lagringskontokapacitet)](../storage/common/storage-introduction.md#queue-storage) |**1 GB till 80 GB**<br/><br/>(definieras när en kö skapas och [aktivering av partitionering](service-bus-partitioning.md) – se avsnittet "Ytterligare information") |
| Maximal meddelandestorlek |**64 kB**<br/><br/>(48 KB vid användning av **Base64-kodning)**<br/><br/>Azure stöder stora meddelanden genom att kombinera köer och blobbar – då kan du köa upp till 200 GB för ett enskilt objekt. |**256 KB** eller **1 MB**<br/><br/>(inklusive både huvud och brödtext, maximal huvudstorlek: 64 KB).<br/><br/>Beror på [tjänstnivån](service-bus-premium-messaging.md). |
| Maximalt meddelande TTL |**Oändlig** (från och med api-version 2017-07-27) |**Tidspan.Max** |
| Maximalt antal köer |**Unlimited** |**10 000**<br/><br/>(per tjänstnamnområde) |
| Maximalt antal samtidiga klienter |**Unlimited** |**Unlimited**<br/><br/>(100 samtidig anslutningsgräns gäller endast TCP-protokollbaserad kommunikation) |

### <a name="additional-information"></a>Ytterligare information
* Service Bus tillämpar köstorleksgränser. Den maximala köstorleken anges när kön skapas och kan ha ett värde mellan 1 och 80 GB. Om köstorleksvärdet som angetts när kön skapas kommer ytterligare inkommande meddelanden att avvisas och ett undantag tas emot av den anropande koden. Mer information om kvoter i Service Bus finns i [Service Bus-kvoter](service-bus-quotas.md).
* Partitionering stöds inte på [Premium-nivån](service-bus-premium-messaging.md). I standardnivån kan du skapa servicebussköer i storlekarna 1, 2, 3, 4 eller 5 GB (standardvärdet är 1 GB). På standardnivå, med partitionering aktiverad (vilket är standard), skapar Service Bus 16 partitioner för varje GB som du anger. Om du skapar en kö som är 5 GB stor, med 16 partitioner blir den maximala köstorleken (5 * 16) = 80 GB. Du kan se den maximala storleken på din partitionerade kö eller ämne genom att titta på dess post på [Azure-portalen][Azure portal].
* Med Lagringsköer, om innehållet i meddelandet inte är XML-säkert, måste det vara **Base64** kodat. Om du **Base64**-koda meddelandet kan användarens nyttolast vara upp till 48 KB i stället för 64 KB.
* Med Service Bus-köer består varje meddelande som lagras i en kö av två delar: ett sidhuvud och en brödtext. Meddelandets totala storlek får inte överstiga den maximala meddelandestorlek som stöds av tjänstnivån.
* När klienter kommunicerar med Service Bus-köer över TCP-protokollet är det maximala antalet samtidiga anslutningar till en enda Service Bus-kö begränsat till 100. Det här numret delas mellan avsändare och mottagare. Om den här kvoten nås kommer efterföljande begäranden om ytterligare anslutningar att avvisas och ett undantag tas emot av den anropande koden. Den här gränsen är inte att klienter som ansluter till köerna med REST-baserat API.
* Om du behöver mer än 10 000 köer i ett enda servicebussnamnområde kan du kontakta Azure-supportteamet och begära en ökning. Om du vill skala efter 10 000 köer med Service Bus kan du också skapa ytterligare namnområden med [Azure-portalen][Azure portal].

## <a name="management-and-operations"></a>Ledning och verksamhet
I det här avsnittet jämförs hanteringsfunktionerna som tillhandahålls av lagringsköer och servicebussköer.

| Jämförelsekriterier | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Förvaltningsprotokoll |**VILA över HTTP/HTTPS** |**VILA över HTTPS** |
| Runtime-protokoll |**VILA över HTTP/HTTPS** |**VILA över HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP med TLS)** |
| .NET-API |**Ja**<br/><br/>(.NET-api för lagringsklient) |**Ja**<br/><br/>(.NET-tjänstbuss-API) |
| Inbyggd C++ |**Ja** |**Ja** |
| Java-API |**Ja** |**Ja** |
| PHP API |**Ja** |**Ja** |
| Api för nod.js |**Ja** |**Ja** |
| Stöd för godtyckliga metadata |**Ja** |**Nej** |
| Namngivningsregler för kö |**Upp till 63 tecken långa**<br/><br/>(Bokstäver i ett könamn måste vara gemener.) |**Upp till 260 tecken långa**<br/><br/>(Kösökvägar och namn är skiftlägesokänsliga.) |
| Hämta funktionen För kölängd |**Ja**<br/><br/>(Ungefärligt värde om meddelanden upphör att gälla utöver TTL utan att tas bort.) |**Ja**<br/><br/>(Exakt punkt-i-tid-värde.) |
| Peek-funktion |**Ja** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Lagringsköer ger stöd för godtyckliga attribut som kan tillämpas på köbeskrivningen, i form av namn-/värdepar.
* Båda köteknikerna ger möjlighet att granska ett meddelande utan att behöva låsa det, vilket kan vara användbart när du implementerar ett köutforskare/webbläsarverktyg.
* Service Bus .NET-förmedlade meddelande-API:er utnyttjar TCP-anslutningar med full duplex för bättre prestanda jämfört med REST över HTTP, och de stöder standardprotokollet AMQP 1.0.
* Namn på lagringsköer kan vara 3-63 tecken långa, kan innehålla gemener, siffror och bindestreck. Mer information finns i [Namnge köer och metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Könamn för Service Bus kan vara upp till 260 tecken långa och har mindre restriktiva namngivningsregler. Könamn för Service Bus kan innehålla bokstäver, siffror, punkter, bindestreck och understreck.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
I det här avsnittet beskrivs autentiserings- och auktoriseringsfunktionerna som stöds av lagringsköer och servicebussköer.

| Jämförelsekriterier | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Autentisering |**Symmetrisk nyckel** |**Symmetrisk nyckel** |
| Säkerhetsmodell |Delegerad åtkomst via SAS-token. |Sas |
| Federation för identitetsprovider |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Varje begäran till någon av köteknikerna måste autentiseras. Offentliga köer med anonym åtkomst stöds inte. Med [SAS](service-bus-sas.md)kan du ta itu med det här scenariot genom att publicera en skrivskyddad SAS, skrivskyddad SAS eller till och med en sas med full åtkomst.
* Autentiseringsschemat som tillhandahålls av lagringsköer innebär användning av en symmetrisk nyckel, som är en hash-baserad meddelandeautentiseringskod (HMAC), beräknad med SHA-256-algoritmen och kodad som en **Base64-sträng.** Mer information om respektive protokoll finns i [Autentisering för Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus-köer stöder en liknande modell med hjälp av symmetriska nycklar. Mer information finns i [Autentisering av signaturer för delad](service-bus-sas.md)åtkomst med Service Bus .

## <a name="conclusion"></a>Slutsats
Genom att få en djupare förståelse för de två teknikerna, kommer du att kunna göra ett mer välgrundat beslut om vilken kö teknik att använda, och när. Beslutet om när lagringsköer eller Service Bus-köer ska användas beror helt klart på ett antal faktorer. Dessa faktorer kan vara starkt beroende av de individuella behoven hos ditt program och dess arkitektur. Om ditt program redan använder kärnfunktionerna i Microsoft Azure kanske du föredrar att välja Lagringsköer, särskilt om du behöver grundläggande kommunikation och meddelanden mellan tjänster eller behöver köer som kan vara större än 80 GB i storlek.

Eftersom Service Bus-köer innehåller ett antal avancerade funktioner, till exempel sessioner, transaktioner, dubblettidentifiering, automatisk dödbokstavning och varaktiga publicerings-/prenumerationsfunktioner, kan de vara ett föredraget val om du skapar ett hybridprogram eller om ditt program kräver dessa funktioner.

## <a name="next-steps"></a>Nästa steg
Följande artiklar innehåller mer vägledning och information om hur du använder lagringsköer eller servicebussköer.

* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Så här använder du tjänsten för kölagring](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Metodtips för prestandaförbättringar med servicebussmedlarmeddelanden](service-bus-performance-improvements.md)
* [Introduktion till köer och ämnen i Azure Service Bus (blogginlägg)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Utvecklarens guide till servicebuss](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Använda kötjänsten i Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

