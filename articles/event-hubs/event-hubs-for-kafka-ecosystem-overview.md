---
title: Använd Azure Event Hubs från Apache Kafka program | Microsoft Docs
description: Översikt och introduktion till Kafka aktiverat Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 4f1cb4e3e8572ee5edbadfb7d2a1771ae9a80341
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014692"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Använd Azure Event Hubs från Apache Kafka-program
Event Hubs ger en Kafka-slutpunkt som kan användas av din befintliga Kafka-baserade program som ett alternativ till att köra dina egna Kafka-kluster. Har stöd för Händelsehubbar [Apache Kafka-protokollet 1.0 och senare](https://kafka.apache.org/documentation/), och fungerar med dina befintliga Kafka-program, inklusive MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vad tillhandahåller Händelsehubbar för Kafka?

Event Hubs för Kafka-funktionen innehåller en protokollet head ovanpå Azure Event Hubs som är binär kompatibel med Kafka versioner 1.0 och senare för att både läsa från och skriva till Kafka-avsnitt. Du kan börja använda Kafka-slutpunkten från dina program med några ändringar i koden, men en minimal konfigurationsändring. Du kan uppdatera anslutningssträngen i konfigurationer för att peka på Kafka-slutpunkt som exponeras av din händelsehubb i stället för att peka på Kafka-kluster. Du kan sedan starta direktuppspelning av händelser från dina program som använder Kafka-protokollet till Event Hubs. Den här integreringen också stöder ramverk som [Kafka ansluta](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), vilket är för närvarande i förhandsversion. 

Begreppsmässigt Kafka och Event Hubs är nästan identiska: de är båda partitionerade loggar som skapats för strömmande data. I följande tabell visas begrepp mellan Kafka och Händelsehubbar.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka och Event Hub konceptuella mappning

| Kafka-koncept | Event Hubs-koncept|
| --- | --- |
| Kluster | Namnrymd |
| Avsnitt | Event Hubs |
| Partition | Partition|
| Konsumentgrupp | Konsumentgrupp |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Viktiga skillnader mellan Kafka och Event Hubs

Medan [Apache Kafka](https://kafka.apache.org/) är programvara som du kan köra oavsett var du väljer, Event Hubs är en molntjänst som liknar Azure Blob Storage. Det finns inga servrar eller nätverk för att hantera och inga asynkrona meddelandeköer för att konfigurera. Du skapar ett namnområde som är ett FQDN som där dina ämnen bor. och sedan skapa Event Hubs och ämnen inom namnområdet. Läs mer om Event Hubs och namnområden [Event Hubs-funktioner](event-hubs-features.md#namespace). Som en molntjänst använder Händelsehubbar en stabil virtuella IP-adress som slutpunkt, så att klienter inte behöver veta om mäklare eller datorer inom ett kluster. 

Skala i Händelsehubbar styrs av hur många genomflödesenheter du har köpt får med varje throughput unit ger rätt till 1 MB per sekund eller 1000 händelser per sekund för ingångshändelser. Som standard Event Hubs kan skalas upp genomflödesenheter när du når gränsen med den [automatisk ökning](event-hubs-auto-inflate.md) funktionen; detta även funktionen fungerar med Event Hubs för Kafka-funktionen. 

### <a name="security-and-authentication"></a>Säkerhet och autentisering

Händelsehubbar i Azure kräver SSL eller TLS för all kommunikation och använder signaturer för delad åtkomst (SAS) för autentisering. Det här kravet gäller även för en Kafka-slutpunkt i Event Hubs. För kompatibilitet med Kafka använder Event Hubs OFORMATERAD SASL för autentisering och SASL SSL för transportsäkerhet. Mer information om säkerhet i Händelsehubbar finns i [Händelsehubbar autentisering och säkerhet](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andra Event Hubs-funktioner som är tillgängliga för Kafka

Event Hubs för Kafka-funktionen kan du skriva med ett protokoll och läsa med en annan, så att din aktuella Kafka producenter kan fortsätta publicera via Kafka, och du kan lägga till läsare med Event Hubs, till exempel Azure Stream Analytics eller Azure Functions. Dessutom Event Hubs-funktioner som [avbilda](event-hubs-capture-overview.md) och [Geo-Haveriberedskap](event-hubs-geo-dr.md) också arbeta med Event Hubs för Kafka-funktionen.

## <a name="features-that-are-not-yet-supported"></a>Funktioner som ännu inte stöds 

Här är listan med Kafka-funktioner som ännu inte stöds:

*   Idempotent producent
*   Transaktionen
*   Komprimering
*   Storlek-baserade kvarhållning
*   Logga komprimering
*   Att lägga till partitioner i ett befintligt ämne
*   HTTP-Kafka API-stöd
*   Kafka Streams

## <a name="next-steps"></a>Nästa steg

Den här artikeln kan du tillhandahålla en introduktion till Event Hubs för Kafka. Mer information finns i följande länkar:

- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create-kafka-enabled.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en asynkron Kafka-meddelandekö i en Kafka-aktiverad händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en Kafka-aktiverad händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en Kafka-aktiverad händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med en Kafka-aktiverad händelsehubb](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka Streams till en Kafka-aktiverad händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)

 
 

