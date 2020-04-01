---
title: Översikt över funktioner – Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller information om funktioner och terminologi för Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 76ab92285cace284c187109ca48c6634777ebbc0
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398322"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funktioner och terminologi i Azure Event Hubs

Azure Event Hubs är en skalbar händelsebearbetningstjänst som intar och bearbetar stora volymer av händelser och data, med låg latens och hög tillförlitlighet. Se [Vad är eventhubbar?](event-hubs-what-is-event-hubs.md)

Den här artikeln bygger på informationen i [översiktsartikeln](event-hubs-what-is-event-hubs.md)och innehåller teknisk information och implementeringsinformation om komponenter och funktioner för eventhubbar.

## <a name="namespace"></a>Namnområde
Ett namnområde för eventhubbar innehåller en unik omfångsbehållare, refererad av dess [fullständigt kvalificerade domännamn,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)där du skapar ett eller flera händelsehubbar eller Kafka-ämnen. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs för Apache Kafka

[Den här funktionen](event-hubs-for-kafka-ecosystem-overview.md) innehåller en slutpunkt som gör det möjligt för kunder att prata med eventhubbar med Kafka-protokollet. Den här integreringen ger kunderna en Kafka-slutpunkt. Detta gör det möjligt för kunder att konfigurera sina befintliga Kafka-program för att prata med Event Hubs, vilket ger ett alternativ till att köra sina egna Kafka-kluster. Event Hubs for Apache Kafka stöder Kafka protokoll 1.0 och senare. 

Med den här integreringen behöver du inte köra Kafka-kluster eller hantera dem med Zookeeper. På så sätt kan du också arbeta med några av de mest krävande funktionerna i Event Hubs som Capture, Auto-inflate och Geo-disaster Recovery.

Den här integreringen gör det också möjligt för program som Mirror Maker eller ramverk som Kafka Connect att fungera klusterlöst med bara konfigurationsändringar. 

## <a name="event-publishers"></a>Händelseutfärdare

Alla entiteter som skickar data till en händelsehubb är en händelseproducent eller *händelseutgivare*. Händelseutgivare kan publicera händelser med HTTPS eller AMQP 1.0 eller Kafka 1.0 och senare. Händelseutfärdare använder en SAS-token (signatur för delad åtkomst) för att identifiera sig mot en händelsehubb och kan ha en unik identitet eller använda en gemensam SAS-token.

### <a name="publishing-an-event"></a>Publicera en händelse

Du kan publicera ett evenemang via AMQP 1.0, Kafka 1.0 (och senare) eller HTTPS. Event Hubs tillhandahåller [klientbibliotek och klasser](event-hubs-dotnet-framework-api-overview.md) för publicering av händelser till en händelsehubb från .NET-klienter. För andra körningar och plattformar kan du använda alla AMQP 1.0-klienter, t.ex. [Apache Qpid](https://qpid.apache.org/). Du kan publicera händelser individuellt eller i batchar. En enskild publikation (händelsedatainstans) har en gräns på 1 MB, oavsett om det är en enskild händelse eller en batch. Publicering av händelser som är större än det här tröskelvärdet resulterar i ett fel. Det är en bra idé för utgivare att vara omedvetna om partitioner i händelsehubben och bara ange en *partitionsnyckel* (som introduceras i nästa avsnitt) eller deras identitet via deras SAS-token.

Valet att använda AMQP eller HTTPS är specifikt för användningsscenariot. AMQP kräver en beständig dubbelriktad socket och dessutom säkerhet på transportnivå (TLS) eller SSL/TLS. AMQP har högre nätverkskostnader när sessionen initieras, men HTTPS kräver ytterligare SSL-kostnader för varje begäran. AMQP har högre prestanda för frekventa utfärdare.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Event Hubs garanterar att alla händelser som delar samma partitionsnyckelvärde levereras i rätt ordning och till samma partition. Om partitionsnycklar används med utfärdarprinciper måste utfärdarens identitet och partitionsnyckelns värde matcha varandra. Annars uppstår ett fel.

### <a name="publisher-policy"></a>Utgivarprincip

Med händelsehubbar får du granulär kontroll över utgivare via *utgivarprinciper*. Utfärdarprinciper är körningsfunktioner som utformats för att ge stöd för ett stort antal oberoende utfärdare. Med utgivarprinciper använder varje utgivare sin egen unika identifierare vid publicering av händelser på en händelsehubb med hjälp av följande mekanism:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Du behöver inte skapa utgivarnamnen i förväg, men de måste matcha SAS-token som används när du publicerar en händelse för att garantera oberoende utgivaridentiteter. När du använder utgivarprinciper ställs **PartitionKey**-värdet in på utgivarens namn. Dessa värden måste matcha för att fungera korrekt.

## <a name="capture"></a>Capture

[Med Event Hubs Capture](event-hubs-capture-overview.md) kan du automatiskt samla in strömmande data i Event Hubs och spara dem i ditt val av antingen ett Blob-lagringskonto eller ett Azure Data Lake Service-konto. Du kan aktivera Capture från Azure-portalen och ange ett minsta storleks- och tidsfönster för att utföra hämtningen. Med Event Hubs Capture anger du ditt eget Azure Blob Storage-konto och -behållare, eller Azure Data Lake Service-konto, varav ett används för att lagra de infångade data. Infångade data skrivs i Apache Avro-formatet.

## <a name="partitions"></a>Partitioner
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-token

Event Hubs använder *Signaturer för delad åtkomst*, som är tillgängliga på namnområdes- och händelsenav-nivå. En SAS-token genereras från en SAS-nyckel och är en SHA-hash för en URL som kodats i ett specifikt format. Med hjälp av namnet på nyckeln (principen) och token kan Event Hubs återgenerera hash-värdet och därmed autentisera avsändaren. Normalt skapas SAS-token för händelseutgivare med endast sändningsbehörighet på en viss händelsehubb. **send** Den här URL-mekanismen med SAS-token är den grund för utfärdaridentifiering som presenterades i principen för utfärdare. Mer information om hur du arbetar med SAS finns i [autentisering med signatur för delad åtkomst med Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Händelsekonsumenter

Alla entiteter som läser händelsedata från en händelsehubb är en *händelsekonsument*. Alla Event Hubs-konsumenter ansluter via AMQP 1.0-sessionen och händelser levereras via sessionen när de blir tillgängliga. Klienten behöver inte söka efter datatillgänglighet.

### <a name="consumer-groups"></a>Konsumentgrupper

Publicerings-/prenumerationsmekanismen för Event Hubs aktiveras via *konsumentgrupper*. En konsumentgrupp är en vy (tillstånd, position eller offset) av en hel händelsehubb. Konsumentgrupper gör det möjligt för flera användningsprogram att vart och ett ha en separat vy över händelseströmmen och att oberoende läsa strömmen i egen takt och med sina egna offset.

Inom en arkitektur för strömbearbetning utgör varje nedströms program en konsumentgrupp. Om du vill skriva händelsedata till långsiktig lagring utgör programmet för att skriva data till lagring en konsumentgrupp. Komplex händelsebearbetning kan sedan utföras av en annan, separat konsumentgrupp. Du får bara åtkomst till en partition via en konsumentgrupp. Det finns alltid en förinställd konsumentgrupp i en händelsehubb, och du kan skapa upp till 20 konsumentgrupper för en händelsehubb på standardnivå.

Det kan finnas högst 5 samtidiga läsare på en partition per konsumentgrupp; Det rekommenderas dock **att det bara finns en aktiv mottagare på en partition per konsumentgrupp**. Inom en enda partition tar varje läsare emot alla meddelanden. Om du har flera läsare på samma partition bearbetar du dubblettmeddelanden. Du måste hantera detta i din kod, vilket kanske inte är trivialt. Det är dock en giltig metod i vissa scenarier.


Följande är exempel på URI-konventionen för konsumentgrupper:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

Följande bild visar strömhanteringsarkitekturen i Event Hubs:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Offsets för strömmar

En *förskjutning* är positionen för en händelse i en partition. Föreställ dig en offset som en markör på klientsidan. Denna offset är en byte-numrering av händelsen. Med den så kan en händelsekonsument (läsare) ange vid vilken punkt i händelseströmmen som läsningen ska starta. Du kan ange offseten som en tidsstämpel eller ett offset-värde. Konsumenterna ansvarar för att lagra sina egna offset-värden utanför händelsehubbtjänsten. Inom en partition innehåller varje händelse en offset.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Kontrollpunkter

*Att skapa kontrollpunkter* är en process genom vilken läsare markerar eller sparar sin position inom en händelsesekvens i en partition. Att skapa kontrollpunkter är konsumentens ansvar och görs för varje partition i en konsumentgrupp. Det här ansvaret innebär att varje läsare i partitionen måste hålla reda på sin nuvarande position i händelseströmmen för varje konsumentgrupp. Läsaren kan sedan informera tjänsten när de anser att dataströmmen är klar.

Om en läsare kopplar från en partition och den sedan återansluts kan han börja läsa vid den kontrollpunkt som tidigare skickades in av den senaste läsaren i den aktuella partitionen inom just den konsumentgruppen. När läsaren ansluter skickas förskjutningen till händelsehubben för att ange den plats där avläsningen ska börja. På så sätt kan du använda kontrollpunkter både till att markera händelser som ”klara” i underordnade program och som skydd i händelse av en redundansväxling mellan läsare som körs på olika datorer. Du kan återgå till äldre data genom att ange en lägre offset i den här kontrollpunktsprocessen. Den här mekanismen möjliggör både återhämtning vid redundansväxlingar och återuppspelning av händelseströmmar.

> [!NOTE]
> Om du använder Azure Blob Storage som kontrollpunktslagring i en miljö som stöder en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga på Azure, måste du använda koden för att ändra API-versionen för lagringstjänsten till den specifika versionen som stöds av den miljön. Om du till exempel kör [Event Hubs på en Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagringstjänsten version 2017-11-09. I det här fallet måste du använda kod för att rikta api-versionen för lagringstjänsten till 2017-11-09. Ett exempel på hur du inriktar dig på en specifik Storage API-version finns i dessa exempel på GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) eller [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

### <a name="common-consumer-tasks"></a>Vanliga konsumentuppgifter

Alla Event Hubs-konsumenter ansluter via en AMQP 1.0-session, en tillståndsmedveten dubbelriktad kommunikationskanal. Varje partition har en AMQP 1.0-session som gör det lättare att flytta händelser som åtskiljs av partitioner.

#### <a name="connect-to-a-partition"></a>Ansluta till en partition

När du ansluter till partitioner är det vanligt att använda en leasingmekanism till att samordna läsaranslutningar till specifika partitioner. På det sättet är det möjligt för varje partition i en konsumentgrupp att bara ha en aktiv läsare. Hanteringen av kontrollpunkter, leasing och läsare blir enklare med klassen [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) för .NET-klienter. Värden för händelsebearbetning är en smart konsumentagent.

#### <a name="read-events"></a>Läsa händelser

När en AMQP 1.0-session och -länk har öppnats för en specifik partition, levereras händelser till AMQP 1.0-klienten av händelsehubbtjänsten. Den här leveransmekanismen gör det möjligt med ett högre genomflöde och kortare svarstid än i pull-baserade mekanismer som t.ex. HTTP GET. När händelser skickas till klienten innehåller varje instans av händelsedata viktiga metadata, till exempel offset- och sekvensnumret som används för att göra det lättare att skapa kontrollpunkter i händelsesekvensen.

Händelsedata:
* Offset
* Sekvensnummer
* Innehåll
* Användaregenskaper
* Systemegenskaper

Det är ditt ansvar att hantera positionen (offset).

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempel på händelsehubbar][]

[Exempel på händelsehubbar]: https://github.com/Azure/azure-event-hubs/tree/master/samples
