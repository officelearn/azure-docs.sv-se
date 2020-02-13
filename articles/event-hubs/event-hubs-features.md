---
title: Översikt över funktioner – Azure Event Hubs | Microsoft Docs
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
ms.openlocfilehash: 568a21cee5b50a8914c603976f5951d0235dbff7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157184"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funktionerna och terminologin i Azure Event Hubs

Azure Event Hubs är en skalbar händelsebearbetningstjänst som matar in och bearbetar stora mängder händelser och data med låg fördröjning och hög tillförlitlighet. Se [Vad är Event Hubs?](event-hubs-what-is-event-hubs.md) för en översikt på hög nivå.

Den här artikeln bygger på informationen i [översikts artikeln](event-hubs-what-is-event-hubs.md)och ger teknisk och implementerings information om Event Hubs-komponenter och-funktioner.

## <a name="namespace"></a>Namnrymd
Ett Event Hubs-namnområde innehåller en unik omfattnings behållare som refereras till av det [fullständigt kvalificerade domän namnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), där du kan skapa ett eller flera Event Hub-eller Kafka-ämnen. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs för Apache Kafka

[Den här funktionen](event-hubs-for-kafka-ecosystem-overview.md) ger en slut punkt som gör det möjligt för kunder att kommunicera med Event Hubs med Kafka-protokollet. Den här integreringen ger kunderna en Kafka-slutpunkt. Detta gör det möjligt för kunder att konfigurera sina befintliga Kafka-program att kommunicera med Event Hubs, vilket ger ett alternativ till att köra sina egna Kafka-kluster. Event Hubs för Apache Kafka har stöd för Kafka-protokollet 1.0 och senare. 

Med den här integrationen behöver du inte köra Kafka-kluster eller hantera dem med Zookeeper. Detta kan du också att arbeta med några av de mest krävande funktionerna i Event Hubs som att samla in, automatisk ökning och Geo-Haveriberedskap.

Den här integrationen kan också program, t.ex. spegling Maker eller ramverk som Kafka ansluta ska fungera clusterless bara konfigurationsändringar. 

## <a name="event-publishers"></a>Händelseutfärdare

En entitet som skickar data till en Event Hub är en händelse producent eller *händelse utgivare*. Händelseutfärdare kan utfärda händelser med hjälp av HTTPS eller AMQP 1.0 eller Kafka 1.0 och senare. Händelseutfärdare använder en SAS-token (signatur för delad åtkomst) för att identifiera sig mot en händelsehubb och kan ha en unik identitet eller använda en gemensam SAS-token.

### <a name="publishing-an-event"></a>Publicera en händelse

Du kan publicera en händelse via AMQP 1.0, Kafka 1.0 (och senare) eller HTTPS. Event Hubs tillhandahåller [klient bibliotek och klasser](event-hubs-dotnet-framework-api-overview.md) för att publicera händelser till en händelsehubben från .net-klienter. För andra körningar och plattformar kan du använda alla AMQP 1.0-klienter, t.ex. [Apache Qpid](https://qpid.apache.org/). Du kan publicera händelser individuellt eller i batchar. En enstaka publikation (en instans av händelsedata) har en gräns på 1 MB, oavsett om det är en enskild händelse eller en batch. Publicering av händelser som är större än det här tröskelvärdet resultat i ett fel. Bästa metoden för en utfärdare är att vara ovetande om partitioner i händelsehubben och att bara ange en *partitionsnyckel* (presenteras i nästa avsnitt) eller sin identitet via sin SAS-token.

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

Med [Event Hubs Capture](event-hubs-capture-overview.md) kan du automatiskt samla in strömmande data i Event Hubs och spara dem på ditt val av antingen ett Blob Storage-konto eller ett Azure Data Lake tjänst konto. Du kan aktivera avbildning från Azure-portalen och ange en minsta storlek och tidsfönster att utföra insamlingen. Med Event Hubs Capture kan ange du din egen Azure Blob Storage-konto och en behållare eller ett konto för Azure Data Lake-tjänsten, av vilka används för att lagra insamlade data. Inlästa data skrivs i Apache Avro-format.

## <a name="partitions"></a>Partitioner
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-token

Event Hubs använder *signaturer för delad åtkomst* (SAS, Shared Access Signatures) som är tillgängliga på namnområdes- och händelsehubbnivå. En SAS-token genereras från en SAS-nyckel och är en SHA-hash för en URL som kodats i ett specifikt format. Med hjälp av namnet på nyckeln (principen) och token kan Event Hubs återgenerera hash-värdet och därmed autentisera avsändaren. SAS-token för händelseutfärdare skapas vanligtvis med enbart behörighet för att **skicka** i en specifik händelsehubb. Den här URL-mekanismen med SAS-token är den grund för utfärdaridentifiering som presenterades i principen för utfärdare. Mer information om hur du arbetar med SAS finns i [autentisering med signatur för delad åtkomst med Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Händelsekonsumenter

En enhet som läser händelsedata från en händelsehubb är en *händelsekonsument*. Alla Event Hubs-konsumenter ansluter via AMQP 1.0-sessionen och händelser levereras via sessionen när de blir tillgängliga. Klienten behöver inte söka efter datatillgänglighet.

### <a name="consumer-groups"></a>Konsumentgrupper

Publicerings-/prenumerationsmekanismen för Event Hubs aktiveras via *konsumentgrupper*. En konsumentgrupp är en vy (tillstånd, position eller offset) av en hel händelsehubb. Konsumentgrupper gör det möjligt för flera användningsprogram att vart och ett ha en separat vy över händelseströmmen och att oberoende läsa strömmen i egen takt och med sina egna offset.

Inom en arkitektur för strömbearbetning utgör varje nedströms program en konsumentgrupp. Om du vill skriva händelsedata till långsiktig lagring utgör programmet för att skriva data till lagring en konsumentgrupp. Komplex händelsebearbetning kan sedan utföras av en annan, separat konsumentgrupp. Du får bara åtkomst till en partition via en konsumentgrupp. Det finns alltid en förinställd konsumentgrupp i en händelsehubb, och du kan skapa upp till 20 konsumentgrupper för en händelsehubb på standardnivå.

Det får finnas högst 5 samtidiga läsare på en partition per konsument grupp. **vi rekommenderar dock att det bara finns en aktiv mottagare på en partition per konsument grupp**. Inom en enda partition får varje läsare alla meddelanden. Om du har flera läsare på samma partition kan bearbeta du dubbletter av meddelanden. Du måste hantera det i din kod, vilket inte kanske är enkelt. Men är det en giltig metod i vissa situationer.


Följande är exempel på URI-konventionen för konsumentgrupper:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

Följande bild visar strömhanteringsarkitekturen för händelsehubbar:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Offsets för strömmar

En *offset* är en händelses position inom en partition. Föreställ dig en offset som en markör på klientsidan. Denna offset är en byte-numrering av händelsen. Med den så kan en händelsekonsument (läsare) ange vid vilken punkt i händelseströmmen som läsningen ska starta. Du kan ange offseten som en tidsstämpel eller ett offset-värde. Konsumenterna ansvarar för att lagra sina egna offset-värden utanför händelsehubbtjänsten. Inom en partition innehåller varje händelse en offset.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Kontrollpunkter

*Att skapa kontrollpunkter* är en process genom vilken läsare markerar eller sparar sin position inom en händelsesekvens i en partition. Att skapa kontrollpunkter är konsumentens ansvar och görs för varje partition i en konsumentgrupp. Det här ansvaret innebär att varje läsare i partitionen måste hålla reda på sin nuvarande position i händelseströmmen för varje konsumentgrupp. Läsaren kan sedan informera tjänsten när de anser att dataströmmen är klar.

Om en läsare kopplar från en partition och den sedan återansluts kan han börja läsa vid den kontrollpunkt som tidigare skickades in av den senaste läsaren i den aktuella partitionen inom just den konsumentgruppen. När läsaren ansluter skickar offset till händelsehubben för att ange den plats som du vill börja läsa. På så sätt kan du använda kontrollpunkter både till att markera händelser som ”klara” i underordnade program och som skydd i händelse av en redundansväxling mellan läsare som körs på olika datorer. Du kan återgå till äldre data genom att ange en lägre offset i den här kontrollpunktsprocessen. Den här mekanismen möjliggör både återhämtning vid redundansväxlingar och återuppspelning av händelseströmmar.

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
* [Event Hubs exempel][]

[Event Hubs exempel]: https://github.com/Azure/azure-event-hubs/tree/master/samples
