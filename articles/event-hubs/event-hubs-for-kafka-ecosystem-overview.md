---
title: Använd event hub från Apache Kafka - app i Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om stöd för Apache Kafka med Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: fc81226e754178ad0edfff96a494dd7522662261
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368515"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Använd Azure Event Hubs från Apache Kafka-program
Event Hubs ger en Kafka-slutpunkt som kan användas av din befintliga Kafka-baserade program som ett alternativ till att köra dina egna Kafka-kluster. Event Hubs stöder [Apache Kafka protocol 1,0 och senare](https://kafka.apache.org/documentation/)och fungerar med dina befintliga Kafka-program, inklusive MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vad tillhandahåller Händelsehubbar för Kafka?

Event Hubs för Kafka-funktionen innehåller en protokollet head ovanpå Azure Event Hubs som är binär kompatibel med Kafka versioner 1.0 och senare för att både läsa från och skriva till Kafka-avsnitt. Du kan börja använda Kafka-slutpunkten från dina program med några ändringar i koden, men en minimal konfigurationsändring. Du kan uppdatera anslutningssträngen i konfigurationer för att peka på Kafka-slutpunkt som exponeras av din händelsehubb i stället för att peka på Kafka-kluster. Du kan sedan starta direktuppspelning av händelser från dina program som använder Kafka-protokollet till Event Hubs. Den här integrationen stöder också ramverk som [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), som för närvarande är en för hands version. 

Konceptuellt Kafka och Event Hubs är nästan identiska: de är både partitionerade loggar som skapats för strömmande data. I följande tabell visas begrepp mellan Kafka och Händelsehubbar.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka och Event Hub konceptuella mappning

| Kafka-koncept | Event Hubs-koncept|
| --- | --- |
| Kluster | Namnområde |
| Avsnitt | Händelsehubb |
| Partition | Partition|
| Konsumentgrupp | Konsumentgrupp |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Viktiga skillnader mellan Kafka och Event Hubs

Även om [Apache Kafka](https://kafka.apache.org/) är program vara, som du kan köra var du än väljer, är Event Hubs en moln tjänst som liknar Azure Blob Storage. Det finns inga servrar eller nätverk för att hantera och inga asynkrona meddelandeköer för att konfigurera. Du skapar ett namnområde som är ett FQDN som där dina ämnen bor. och sedan skapa Event Hubs och ämnen inom namnområdet. Mer information om Event Hubs och namn områden finns i [Event Hubs funktioner](event-hubs-features.md#namespace). Som en moln tjänst använder Event Hubs en enda stabil virtuell IP-adress som slut punkten, så att klienterna inte behöver känna till utlösarna eller datorerna i ett kluster. 

Skala i Händelsehubbar styrs av hur många genomflödesenheter du har köpt får med varje throughput unit ger rätt till 1 MB per sekund eller 1000 händelser per sekund för ingångshändelser. Som standard skalar Event Hubs data flödes enheter när du når gränsen med funktionen för [Automatisk](event-hubs-auto-inflate.md) ökning. den här funktionen fungerar också med Event Hubs för Kafka-funktionen. 

### <a name="security-and-authentication"></a>Säkerhet och autentisering
Varje gång du publicerar eller använder händelser från en Event Hubs för Kafka försöker klienten komma åt Event Hubs resurserna. Du vill se till att resurserna nås med hjälp av en auktoriserad entitet. När du använder Apache Kafka protokoll med dina klienter kan du ange konfigurationen för autentisering och kryptering med hjälp av SASL-mekanismer. När du använder Event Hubs för Kafka kräver TLS-kryptering (som alla data i överföring med Event Hubs är TLS-krypterat). Det kan du göra genom att ange SASL_SSL alternativet i konfigurations filen. 

Azure Event Hubs tillhandahåller flera alternativ för att ge åtkomst till dina säkra resurser. 

- Arbeta
- Signatur för delad åtkomst (SAS)

#### <a name="oauth"></a>Arbeta
Event Hubs integreras med Azure Active Directory (Azure AD), vilket ger en **OAuth** 2,0-kompatibel Central Authorization-Server. Med Azure AD kan du använda rollbaserad åtkomst kontroll (RBAC) för att ge detaljerade behörigheter till dina klient identiteter. Du kan använda den här funktionen med Kafka-klienter genom att ange **SASL_SSL** för protokollet och **OAUTHBEARER** för mekanismen. Mer information om RBAC-roller och nivåer för omfångs åtkomst finns i [ge åtkomst till Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Signatur för delad åtkomst (SAS)
Event Hubs tillhandahåller även **signaturer för delad åtkomst (SAS)** för delegerad åtkomst till Event Hubs för Kafka-resurser. Att auktorisera åtkomst med OAuth 2,0-tokenbaserad mekanism ger överlägsen säkerhet och enkel användning över SAS. De inbyggda rollerna kan också eliminera behovet av ACL-baserad auktorisering, som måste underhållas och hanteras av användaren. Du kan använda den här funktionen med Kafka-klienter genom att ange **SASL_SSL** för protokollet och **plait** för mekanismen. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Exempel 
En **själv studie kurs** med stegvisa instruktioner för att skapa en Kafka aktive rad händelsehubben och komma åt den med SAS eller OAuth finns i [snabb start: Data strömning med Event Hubs med hjälp av Kafka-protokollet](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Fler **exempel** som visar hur du använder OAuth med Event Hubs för Kafka finns i [exempel på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andra Event Hubs-funktioner som är tillgängliga för Kafka

Event Hubs för Kafka-funktionen kan du skriva med ett protokoll och läsa med en annan, så att din aktuella Kafka producenter kan fortsätta publicera via Kafka, och du kan lägga till läsare med Event Hubs, till exempel Azure Stream Analytics eller Azure Functions. Dessutom fungerar Event Hubs funktioner som [fånga](event-hubs-capture-overview.md) och [geo haveri beredskap](event-hubs-geo-dr.md) med funktionen Event Hubs för Kafka.

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


