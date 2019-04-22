---
title: Vad är Azure Event Hubs? – en tjänst för inmatning av stordata | Microsoft Docs
description: Lär dig mer om Azure Event Hubs, en strömningstjänst för stordata som matar in flera miljoner händelser per sekund.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 80c98bea08018780ba1d439e369ab42343d1e615
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680340"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs – en stordataströmningsplattform och händelseinmatningstjänst
Azure Event Hubs är en stordataströmningsplattform och händelseinmatningstjänst. Den kan ta emot och behandla miljoner händelser per sekund. Data som skickas till en händelsehubb kan omvandlas och lagras med hjälp av valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar.

Här följer några exempel på hur du kan använda Event Hubs:

- Avvikelseidentifiering (bedrägeri/avvikare)
- Programloggning
- Analyspipelines, till exempel klickströmmar
- Instrumentpaneler i realtid
- Dataarkivering
- Transaktionsbearbetning
- Bearbetning av användartelemetri
- Strömning av enhetstelemetri 

## <a name="why-use-event-hubs"></a>Varför ska jag använda Event Hubs?

Data är bara värdefulla om det finns ett enkelt sätt att bearbeta och få snabba insikter från datakällor. Event Hubs är en distribuerad plattform för strömningsbearbetning med låg fördröjning och smidig integrering, som har data- och analystjänster inuti och utanför Azure för att skapa en fullständig stordatapipeline.

Event Hubs utgör ”dörren” för en händelsepipeline, ofta kallad en *händelseinmatare* i lösningsarkitekturer. En händelseinmatare är en komponent eller tjänst som placeras mellan händelseutgivare och -konsumenter och frikopplar produktion av en händelseström från användningen av de händelserna. Event Hubs ger en enhetlig strömningsplattform med tidkvarhållningsbuffert vilket frikopplar händelseproducenter från händelsekonsumenter. 

I följande avsnitt beskrivs viktiga funktioner i Azure Event Hubs-tjänsten: 

## <a name="fully-managed-paas"></a>Fullständigt hanterad PaaS 

Event Hubs är en fullständigt hanterad PaaS-lösning (Platform-as-a-Service) med låga omkostnader för konfiguration och hantering så att du kan fokusera på affärslösningarna. Med [Event Hubs för Apache Kafka-ekosystem](event-hubs-for-kafka-ecosystem-overview.md) får du PaaS Kafka-funktionerna utan att behöva hantera, konfigurera eller köra dina kluster.

## <a name="support-for-real-time-and-batch-processing"></a>Stöd för realtidsbearbetning och satsvis bearbetning

Mata in, buffra, lagra och bearbeta din dataström i realtid för att få användbara insikter. Event Hubs använder en [partitionerad konsumentmodell](event-hubs-features.md#partitions) som gör att flera program kan bearbeta dataströmmen samtidigt och att du får kontroll över bearbetningens hastighet.

[Samla in](event-hubs-capture-overview.md) data i nära realtid i en [Azure Blob-lagring](https://azure.microsoft.com/services/storage/blobs/) eller [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)  för långsiktig kvarhållning eller bearbetning av mindre batchar. Du kan åstadkomma detta beteende i samma dataström som du använder för att få fram analys i realtid. Det går snabbt att konfigurera insamling av händelsedata. Det finns inga administrativa kostnader för att köra den, och den skalar automatiskt med Event Hubs  [genomflödesenheter](event-hubs-features.md#throughput-units). Med Event Hubs kan du rikta in dig på databearbetning i stället för på datainsamling.

Azure Event Hubs är även integrerat med [Azure Functions](/azure/azure-functions/) för att ge en serverlös arkitektur.

## <a name="scalable"></a>Skalbar 

Med Event Hubs kan du börja med dataströmmar i megabytestorlek och växa till gigabyte eller terabyte. Funktionen [Automatisk ökning](event-hubs-auto-inflate.md) är en av de många tillgängliga alternativ för att skala antalet dataflödesenheter som uppfyller dina användningsbehov. 

## <a name="rich-ecosystem"></a>Stort ekosystem

Med [Event Hubs för Apache Kafka-ekosystem](event-hubs-for-kafka-ecosystem-overview.md) kan [Apache Kafka-klienter och -program (1.0 och senare)](https://kafka.apache.org/) kommunicera med Event Hubs. Du behöver inte installera, konfigurera eller hantera några Kafka-kluster.
 
Med ett brett ekosystem som är tillgängligt i olika [språk (.NET, Java, Python, Go och Node.js)](https://github.com/Azure/azure-event-hubs) kan du enkelt börjar bearbeta dataströmmarna från Event Hubs. Alla klientspråk som stöds ger lågnivåintegrering. Ekosystemet innehåller också sömlös integrering med Azure-tjänster som Azure Stream Analytics och Azure Functions så att du kan skapa arkitekturer utan server.

## <a name="key-architecture-components"></a>Nyckelkomponenter i arkitektur
Event Hubs innehåller följande [viktiga element](event-hubs-features.md):

- **Händelseutfärdare**: En entitet som skickar data till en händelsehubb. Händelseutfärdare kan utfärda händelser med hjälp av HTTPS, AMQP 1.0 eller Apache Kafka (1.0 och senare)
- **Partitioner**: Varje konsument läser endast en specifik delmängd, eller partition, av meddelandeströmmen.
- **Konsumentgrupper**: En vy (tillstånd, position eller offset) av en hel händelsehubb. Konsumentgrupper gör att flera konsumerande program kan ha en separat vy över händelseströmmen. De läser strömmen oberoende av varandra i egen takt och med sina egna offset.
- **Genomflödesenheter**: Förköpta kapacitetsenheter som styr genomflödeskapaciteten i Event Hubs.
- **Händelsemottagare**: Alla entiteter som läser händelsedata från en händelsehubb. Alla Event Hubs-konsumenter ansluter via AMQP 1.0-session. Event Hubs-tjänsten levererar händelserna via en session när de blir tillgängliga. Alla Kafka-konsumenter ansluter via protokollet Kafka 1.0 och senare.

Följande bild visar strömhanteringsarkitekturen i Event Hubs:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Nästa steg

Kom igång med Event Hubs, se den **skicka och ta emot händelser** Självstudier: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (skicka)](event-hubs-c-getstarted-send.md)
- [Apache Storm (endast reecive)](event-hubs-storm-getstarted-receive.md)

Mer information om Event Hubs finns i följande artiklar:

- [Översikt över Event Hubs-funktioner](event-hubs-features.md)
- [Vanliga frågor och svar](event-hubs-faq.md).


