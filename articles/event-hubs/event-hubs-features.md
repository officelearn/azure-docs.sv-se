---
title: Översikt över funktioner – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om funktioner och terminologi i Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: db7c0244fd4e9e04f9cfbcbba8748ec8190fc5c5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96007449"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funktioner och terminologi i Azure Event Hubs

Azure Event Hubs är en skalbar tjänst för händelse bearbetning som matar in och bearbetar stora mängder händelser och data, med låg latens och hög tillförlitlighet. Se [Vad är Event Hubs?](./event-hubs-about.md) för en översikt på hög nivå.

Den här artikeln bygger på informationen i [översikts artikeln](./event-hubs-about.md)och ger teknisk och implementerings information om Event Hubs-komponenter och-funktioner.

## <a name="namespace"></a>Namnområde
Ett Event Hubs-namnområde innehåller en unik omfattnings behållare som refereras till av det [fullständigt kvalificerade domän namnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), där du kan skapa ett eller flera Event Hub-eller Kafka-ämnen. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs för Apache Kafka

[Den här funktionen](event-hubs-for-kafka-ecosystem-overview.md) ger en slut punkt som gör det möjligt för kunder att kommunicera med Event Hubs med Kafka-protokollet. Den här integrationen ger kunderna en Kafka-slutpunkt. Detta gör det möjligt för kunderna att konfigurera befintliga Kafka-program för att prata med Event Hubs, vilket ger ett alternativ till att köra egna Kafka-kluster. Event Hubs för Apache Kafka stöder Kafka-protokoll 1,0 och senare. 

Med den här integrationen behöver du inte köra Kafka-kluster eller hantera dem med Zookeeper. Detta gör det också möjligt att arbeta med några av de mest krävande funktionerna i Event Hubs som hämtning, automatisk ökning och geo-katastrof återställning.

Den här integrationen gör det också möjligt att använda program som speglings tillverkare eller ramverk som Kafka Anslut till arbets kluster med bara konfigurations ändringar. 

## <a name="event-publishers"></a>Händelseutfärdare

En entitet som skickar data till en Event Hub är en händelse producent eller *händelse utgivare*. Händelse utgivare kan publicera händelser med hjälp av HTTPS eller AMQP 1,0 eller Kafka 1,0 och senare. Händelseutfärdare använder en SAS-token (signatur för delad åtkomst) för att identifiera sig mot en händelsehubb och kan ha en unik identitet eller använda en gemensam SAS-token.

### <a name="publishing-an-event"></a>Publicera en händelse

Du kan publicera en händelse via AMQP 1,0, Kafka 1,0 (och senare) eller HTTPS. Event Hubs tjänsten tillhandahåller klient biblioteken [REST API](https://docs.microsoft.com/rest/api/eventhub/) och [.net](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [python](event-hubs-python-get-started-send.md), [Java Script](event-hubs-node-get-started-send.md)och [Go](event-hubs-go-get-started-send.md) för att publicera händelser till en Event Hub. För andra körningar och plattformar kan du använda alla AMQP 1.0-klienter, t.ex. [Apache Qpid](https://qpid.apache.org/). 

Du kan publicera händelser individuellt eller i batchar. En enskild publikation (händelse data instans) har en gräns på 1 MB, oavsett om det är en enskild händelse eller en batch. Om du publicerar händelser som är större än det här tröskelvärdet uppstår ett fel. Det är en bra idé för utgivare att vara medveten om partitioner i händelsehubben och bara ange en *partitionsnyckel* (som introduceras i nästa avsnitt) eller deras identitet via sin SAS-token.

Valet att använda AMQP eller HTTPS är specifikt för användningsscenariot. AMQP kräver en beständig dubbelriktad socket och dessutom säkerhet på transportnivå (TLS) eller SSL/TLS. AMQP har högre nätverks kostnader när sessionen initieras, men HTTPS kräver ytterligare TLS-kostnader för varje begäran. AMQP har högre prestanda för frekventa utfärdare.

![Partitionsnyckel](./media/event-hubs-features/partition_keys.png)

Event Hubs garanterar att alla händelser som delar samma partitionsnyckelvärde levereras i rätt ordning och till samma partition. Om partitionsnycklar används med utfärdarprinciper måste utfärdarens identitet och partitionsnyckelns värde matcha varandra. Annars uppstår ett fel.

### <a name="publisher-policy"></a>Utgivarprincip

Med händelsehubbar får du granulär kontroll över utgivare via *utgivarprinciper*. Utfärdarprinciper är körningsfunktioner som utformats för att ge stöd för ett stort antal oberoende utfärdare. Med utgivarprinciper använder varje utgivare sin egen unika identifierare vid publicering av händelser på en händelsehubb med hjälp av följande mekanism:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Du behöver inte skapa utgivarnamnen i förväg, men de måste matcha SAS-token som används när du publicerar en händelse för att garantera oberoende utgivaridentiteter. När du använder utgivarprinciper ställs **PartitionKey**-värdet in på utgivarens namn. Dessa värden måste matcha för att fungera korrekt.

## <a name="capture"></a>Capture

Med [Event Hubs Capture](event-hubs-capture-overview.md) kan du automatiskt samla in strömmande data i Event Hubs och spara dem på ditt val av antingen ett Blob Storage-konto eller ett Azure Data Lake tjänst konto. Du kan aktivera avbildning från Azure Portal och ange en minimi storlek och tids period för att utföra avbildningen. Med Event Hubs avbildningen anger du ditt eget Azure Blob Storage-konto och-behållare, eller Azure Data Lake tjänst konto som används för att lagra insamlade data. Insamlade data skrivs i formatet Apache Avro.

## <a name="partitions"></a>Partitioner
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-token

Event Hubs använder *signaturer för delad åtkomst*, som är tillgängliga i namn området och händelse Hub-nivån. En SAS-token genereras från en SAS-nyckel och är en SHA-hash för en URL som kodats i ett specifikt format. Med hjälp av namnet på nyckeln (principen) och token kan Event Hubs återgenerera hash-värdet och därmed autentisera avsändaren. Normalt skapas SAS-token för händelse utgivare med endast **Skicka** -privilegier för en speciell händelsehubben. Den här URL-mekanismen med SAS-token är den grund för utfärdaridentifiering som presenterades i principen för utfärdare. Mer information om hur du arbetar med SAS finns i [autentisering med signatur för delad åtkomst med Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Händelsekonsumenter

En entitet som läser händelse data från en Event Hub är en *händelse konsument*. Alla Event Hubs-konsumenter ansluter via AMQP 1.0-sessionen och händelser levereras via sessionen när de blir tillgängliga. Klienten behöver inte söka efter datatillgänglighet.

### <a name="consumer-groups"></a>Konsumentgrupper

Publicerings-/prenumerationsmekanismen för Event Hubs aktiveras via *konsumentgrupper*. En konsumentgrupp är en vy (tillstånd, position eller offset) av en hel händelsehubb. Konsumentgrupper gör det möjligt för flera användningsprogram att vart och ett ha en separat vy över händelseströmmen och att oberoende läsa strömmen i egen takt och med sina egna offset.

Inom en arkitektur för strömbearbetning utgör varje nedströms program en konsumentgrupp. Om du vill skriva händelsedata till långsiktig lagring utgör programmet för att skriva data till lagring en konsumentgrupp. Komplex händelsebearbetning kan sedan utföras av en annan, separat konsumentgrupp. Du får bara åtkomst till en partition via en konsumentgrupp. Det finns alltid en förinställd konsumentgrupp i en händelsehubb, och du kan skapa upp till 20 konsumentgrupper för en händelsehubb på standardnivå.

Det får finnas högst 5 samtidiga läsare på en partition per konsument grupp. **vi rekommenderar dock att det bara finns en aktiv mottagare på en partition per konsument grupp**. Varje läsare tar emot alla meddelanden inom en enda partition. Om du har flera läsare på samma partition behandlar du duplicerade meddelanden. Du måste hantera detta i din kod, vilket inte kan vara trivialt. Det är dock en giltig metod i vissa scenarier.

Vissa klienter som erbjuds av Azure SDK: er är intelligenta konsument agenter som automatiskt hanterar information om att se till att varje partition har en enda läsare och att alla partitioner för en Event Hub läses från. På så sätt kan din kod fokusera på bearbetning av de händelser som läses från händelsehubben så att det går att ignorera många av information om partitionerna. Mer information finns i [ansluta till en partition](#connect-to-a-partition).

I följande exempel visas URL-konventionen för konsument gruppen:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

Följande bild visar strömhanteringsarkitekturen i Event Hubs:

![Event Hubs arkitektur](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Offsets för strömmar

En *förskjutning* är en händelses position i en partition. Föreställ dig en offset som en markör på klientsidan. Denna offset är en byte-numrering av händelsen. Med den så kan en händelsekonsument (läsare) ange vid vilken punkt i händelseströmmen som läsningen ska starta. Du kan ange offseten som en tidsstämpel eller ett offset-värde. Konsumenterna ansvarar för att lagra sina egna offset-värden utanför händelsehubbtjänsten. Inom en partition innehåller varje händelse en offset.

![Förskjutning av partition](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Kontrollpunkter

*Att skapa kontrollpunkter* är en process genom vilken läsare markerar eller sparar sin position inom en händelsesekvens i en partition. Att skapa kontrollpunkter är konsumentens ansvar och görs för varje partition i en konsumentgrupp. Det här ansvaret innebär att varje läsare i partitionen måste hålla reda på sin nuvarande position i händelseströmmen för varje konsumentgrupp. Läsaren kan sedan informera tjänsten när de anser att dataströmmen är klar.

Om en läsare kopplar från en partition och den sedan återansluts kan han börja läsa vid den kontrollpunkt som tidigare skickades in av den senaste läsaren i den aktuella partitionen inom just den konsumentgruppen. När läsaren ansluter skickar den förskjutningen till händelsehubben för att ange den plats där du vill börja läsa. På så sätt kan du använda kontrollpunkter både till att markera händelser som ”klara” i underordnade program och som skydd i händelse av en redundansväxling mellan läsare som körs på olika datorer. Du kan återgå till äldre data genom att ange en lägre offset i den här kontrollpunktsprocessen. Den här mekanismen möjliggör både återhämtning vid redundansväxlingar och återuppspelning av händelseströmmar.

> [!NOTE]
> Om du använder Azure Blob Storage som kontroll punkts Arkiv i en miljö som har stöd för en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure, måste du använda kod för att ändra Storage Service API-versionen till den version som stöds av den aktuella miljön. Om du till exempel kör [Event Hubs på en Azure Stack hubb version 2002](/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagrings tjänsten version 2017-11-09. I så fall måste du använda kod för att rikta Storage Service API-versionen till 2017-11-09. Ett exempel på hur du riktar in en speciell Storage API-version finns i följande exempel på GitHub: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) eller  [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Vanliga konsumentuppgifter

Alla Event Hubs konsumenter ansluter via en AMQP 1,0-session, en tillstånds medveten dubbelriktad kommunikations kanal. Varje partition har en AMQP 1.0-session som gör det lättare att flytta händelser som åtskiljs av partitioner.

#### <a name="connect-to-a-partition"></a>Ansluta till en partition

När du ansluter till partitioner är det vanligt att använda en operationell mekanism för att koordinera läsar anslutningar till vissa partitioner. På så sätt är det möjligt att varje partition i en konsument grupp bara har en aktiv läsare. Kontroll punkter, leasing och hantering av läsare förenklas med hjälp av klienterna i Event Hubs SDK: er som fungerar som intelligenta konsument agenter. Dessa är:

- [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) för .net
- [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) för Java
- [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) för python
- [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) för Java Script/typescript

#### <a name="read-events"></a>Läsa händelser

När en AMQP 1.0-session och -länk har öppnats för en specifik partition, levereras händelser till AMQP 1.0-klienten av händelsehubbtjänsten. Den här leveransmekanismen gör det möjligt med ett högre genomflöde och kortare svarstid än i pull-baserade mekanismer som t.ex. HTTP GET. När händelser skickas till klienten innehåller varje instans av händelsedata viktiga metadata, till exempel offset- och sekvensnumret som används för att göra det lättare att skapa kontrollpunkter i händelsesekvensen.

Händelsedata:
* Offset
* Sekvensnummer
* Brödtext
* Användaregenskaper
* Systemegenskaper

Det är ditt ansvar att hantera positionen (offset).

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med händelsehubbar
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Programmerings guide för Event Hubs](event-hubs-programming-guide.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Event Hubs exempel](event-hubs-samples.md)
