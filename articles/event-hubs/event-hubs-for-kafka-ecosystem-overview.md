---
title: Använda händelsehubben från Apache Kafka app – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om Apache Kafka support av Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d3271d6e8cb7d423e1dccd235b244688e7ab5683
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555785"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Använda Azure Event Hubs från Apache Kafka-program
Event Hubs tillhandahåller en Kafka-slutpunkt som kan användas av dina befintliga Kafka-baserade program som ett alternativ till att köra ditt eget Kafka-kluster. Event Hubs stöder [Apache Kafka protocol 1,0 och senare](https://kafka.apache.org/documentation/)och fungerar med dina befintliga Kafka-program, inklusive MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vad innebär Event Hubs för Kafka?

Funktionen Event Hubs för Kafka tillhandahåller ett protokoll huvud ovanpå Azure Event Hubs som är binärt kompatibelt med Kafka-versionerna 1,0 och senare för att både läsa från och skriva till Kafka ämnen. Du kan börja använda Kafka-slutpunkten från dina program utan kod ändring men en minimal konfigurations ändring. Du uppdaterar anslutnings strängen i konfigurationer för att peka på Kafka-slutpunkten som exponeras av händelsehubben i stället för att peka på ditt Kafka-kluster. Sedan kan du starta strömmande händelser från dina program som använder Kafka-protokollet i Event Hubs. Den här integrationen stöder också ramverk som [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), som för närvarande är en för hands version. 

Konceptuellt Kafka och Event Hubs är nästan identiska: de har båda partitionerade loggar som skapats för strömmande data. Följande tabell mappar begrepp mellan Kafka och Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Konceptuell mappning för Kafka och Event Hub

| Kafka-koncept | Event Hubs koncept|
| --- | --- |
| Kluster | Namnområde |
| Ämne | Event Hub |
| Partition | Partition|
| Konsument grupp | Konsument grupp |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Viktiga skillnader mellan Kafka och Event Hubs

Även om [Apache Kafka](https://kafka.apache.org/) är program vara, som du kan köra var du än väljer, är Event Hubs en moln tjänst som liknar Azure Blob Storage. Det finns inga servrar eller nätverk att hantera och inga hanterare att konfigurera. Du skapar ett namn område, som är ett FQDN där dina ämnen är Live och sedan skapar Event Hubs eller ämnen inom det namn området. Mer information om Event Hubs och namn områden finns i [Event Hubs funktioner](event-hubs-features.md#namespace). Som en moln tjänst använder Event Hubs en enda stabil virtuell IP-adress som slut punkten, så att klienterna inte behöver känna till utlösarna eller datorerna i ett kluster. 

Skala i Event Hubs styrs av hur många data flödes enheter du köper, med varje data flödes enhet, vilket innebär att du får 1 MB per sekund eller 1000 händelser per sekund. Som standard skalar Event Hubs data flödes enheter när du når gränsen med funktionen för [Automatisk](event-hubs-auto-inflate.md) ökning. den här funktionen fungerar också med Event Hubs för Kafka-funktionen. 

### <a name="security-and-authentication"></a>Säkerhet och autentisering

Azure Event Hubs kräver SSL eller TLS för all kommunikation och använder signaturer för delad åtkomst (SAS) för autentisering. Detta krav gäller också för en Kafka-slutpunkt i Event Hubs. För kompatibilitet med Kafka använder Event Hubs SASL PLAIN för autentisering och SASL SSL för transport säkerhet. Mer information om säkerhet i Event Hubs finns [Event Hubs autentisering och säkerhet](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andra Event Hubs funktioner som är tillgängliga för Kafka

Med funktionen Event Hubs för Kafka kan du skriva med ett protokoll och läsa med ett annat, så att dina befintliga Kafka-producenter kan fortsätta att publicera via Kafka och du kan lägga till läsare med Event Hubs, till exempel Azure Stream Analytics eller Azure Functions. Dessutom fungerar Event Hubs funktioner som [fånga](event-hubs-capture-overview.md) och [geo haveri beredskap](event-hubs-geo-dr.md) med funktionen Event Hubs för Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funktioner som ännu inte stöds 

Här är en lista över Kafka-funktioner som ännu inte stöds:

*   Idempotenta-tillverkare
*   transaktionen
*   Komprimering
*   Storleks beroende kvarhållning
*   Logg komprimering
*   Lägga till partitioner i ett befintligt ämne
*   API-stöd för HTTP-Kafka
*   Kafka strömmar

## <a name="next-steps"></a>Nästa steg

I den här artikeln angav vi en introduktion till Event Hubs för Kafka. Mer information finns i följande länkar:

- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create-kafka-enabled.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en asynkron Kafka-meddelandekö i en Kafka-aktiverad händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en Kafka-aktiverad händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en Kafka-aktiverad händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med en Kafka-aktiverad händelsehubb](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka Streams till en Kafka-aktiverad händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


