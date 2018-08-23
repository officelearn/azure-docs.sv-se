---
title: Azure Event Hubs för Apache Kafka | Microsoft Docs
description: Översikt och introduktion till Kafka aktiverat Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/16/2018
ms.author: bahariri
ms.openlocfilehash: 16c101068be48ba1435ef230b29c679fcef17d08
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42061697"
---
# <a name="azure-event-hubs-for-apache-kafka-preview"></a>Händelsehubbar i Azure för Apache Kafka (förhandsgranskning)

Event Hubs ger en Kafka-slutpunkt som kan användas av din befintliga Kafka-baserade program som ett alternativ till att köra dina egna Kafka-kluster. Har stöd för Händelsehubbar [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) och nyare-klientversioner och fungerar med dina befintliga Kafka-program, inklusive MirrorMaker. 

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vad tillhandahåller Händelsehubbar för Kafka?

Event Hubs för Kafka-funktionen innehåller en protokollet head ovanpå Azure Event Hubs som är binär kompatibel med Kafka versioner 1.0 och senare för att både läsa från och skriva till Kafka-avsnitt. Du kan börja använda Kafka-slutpunkten från dina program med några ändringar i koden, men en minimal konfigurationsändring. Du kan uppdatera anslutningssträngen i konfigurationer för att peka på Kafka-slutpunkt som exponeras av din händelsehubb i stället för att peka på Kafka-kluster. Du kan sedan starta direktuppspelning av händelser från dina program som använder Kafka-protokollet till Event Hubs. 

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

## <a name="features-that-are-not-supported-in-the-preview"></a>Funktioner som inte stöds i förhandsversionen

Följande Kafka-funktioner stöds inte för den offentliga förhandsversionen av Event Hubs för Kafka-integrering:

*   Idempotent producent
*   Transaktionen
*   Komprimering
*   Storlek-baserade kvarhållning
*   Logga komprimering
*   Att lägga till partitioner i ett befintligt ämne
*   HTTP-Kafka API-stöd
*   Kafka ansluta
*   Kafka Streams

## <a name="next-steps"></a>Nästa steg

Den här artikeln kan du tillhandahålla en introduktion till Event Hubs för Kafka. Mer information finns i följande länkar:

* [Så här skapar du Kafka aktiverat Event Hubs](event-hubs-create-kafka-enabled.md)
* [Stream till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

 
 

