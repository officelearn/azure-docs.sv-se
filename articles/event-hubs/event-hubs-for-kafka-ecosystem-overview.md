---
title: Använd händelsehubb från Apache Kafka-appen - Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller information om Apache Kafka-stöd från Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 6dcbf0ad0f6678d892c5c02446cac09b4325384c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283658"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Använda Azure Event Hubs från Apache Kafka-program
Event Hubs tillhandahåller en Kafka-slutpunkt som kan användas av dina befintliga Kafka-baserade program som ett alternativ till att köra ditt eget Kafka-kluster. Event Hubs stöder [Apache Kafka protokoll 1.0 och senare](https://kafka.apache.org/documentation/)och fungerar med dina befintliga Kafka-program, inklusive MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vad tillhandahåller Event Hubs för Kafka?

Funktionen Event Hubs for Kafka tillhandahåller ett protokollhuvud ovanpå Azure Event Hubs som är binärt kompatibelt med Kafka version 1.0 och senare för både läsning från och skrivning till Kafka-ämnen. Du kan börja använda Kafka-slutpunkten från dina program utan kodändring men en minimal konfigurationsändring. Du uppdaterar anslutningssträngen i konfigurationer för att peka på Kafka-slutpunkten som visas av händelsehubben i stället för att peka på Kafka-klustret. Sedan kan du börja strömma händelser från dina program som använder Kafka-protokollet till Event Hubs. Den här integrationen stöder också ramverk som [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), som för närvarande är i förhandsversion. 

Begreppsmässigt Kafka och Event Hubs är nästan identiska: de är båda partitionerade loggar byggda för strömmande data. Följande tabell mappar begrepp mellan Kafka och Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka och händelsehubbens konceptuella mappning

| Kafka Koncept | Koncept för eventhubbar|
| --- | --- |
| Kluster | Namnområde |
| Hjälpavsnitt | Händelsehubb |
| Partition | Partition|
| Konsumentgruppen | Konsumentgruppen |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Viktiga skillnader mellan Kafka och Event Hubs

Medan [Apache Kafka](https://kafka.apache.org/) är programvara, som du kan köra var du än vill, är Event Hubs en molntjänst som liknar Azure Blob Storage. Det finns inga servrar eller nätverk att hantera och inga mäklare att konfigurera. Du skapar ett namnområde, som är ett FQDN där dina ämnen visas, och skapar sedan händelsehubbar eller ämnen i det namnområdet. Mer information om händelsehubbar och namnområden finns i [funktioner för händelsehubbar](event-hubs-features.md#namespace). Som en molntjänst använder Event Hubs en enda stabil virtuell IP-adress som slutpunkt, så klienter behöver inte känna till mäklare eller datorer i ett kluster. 

Skala i händelsehubbar styrs av hur många dataflödesenheter du köper, där varje dataflödesenhet berättigar till 1 MB per sekund eller 1 000 händelser per sekund av inträngning. Som standard skalar Event Hubs upp dataflödesenheter när du når din gräns med funktionen [Blås upp automatiskt.](event-hubs-auto-inflate.md) den här funktionen fungerar även med funktionen Event Hubs for Kafka. 

### <a name="security-and-authentication"></a>Säkerhet och autentisering
Varje gång du publicerar eller använder händelser från en eventhubbar för Kafka försöker klienten komma åt eventhubbarresurserna. Du vill vara säkra på att resurserna används med hjälp av en auktoriserad entitet. När du använder Apache Kafka-protokollet med dina klienter kan du ställa in konfigurationen för autentisering och kryptering med HJÄLP av SASL-mekanismerna. När du använder Event Hubs för Kafka krävs TLS-kryptering (eftersom alla data under överföring med Event Hubs är TLS-krypterade). Det kan göras med att ange alternativet SASL_SSL i konfigurationsfilen. 

Azure Event Hubs innehåller flera alternativ för att auktorisera åtkomst till dina säkra resurser. 

- Oauth
- Signatur för delad åtkomst (SAS)

#### <a name="oauth"></a>Oauth
Event Hubs integreras med Azure Active Directory (Azure AD), som tillhandahåller en **OAuth** 2.0-kompatibel centraliserad auktoriseringsserver. Med Azure AD kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja detaljerade behörigheter till dina klientidentiteter. Du kan använda den här funktionen med dina Kafka-klienter genom att ange **SASL_SSL** för protokollet och **OAUTHBEARER** för mekanismen. Mer information om RBAC-roller och nivåer för omfångsåtkomst finns i [Auktorisera åtkomst med Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Signatur för delad åtkomst (SAS)
Event Hubs tillhandahåller också **SAS (Shared Access Signatures)** för delegerad åtkomst till eventhubbar för Kafka-resurser. Att godkänna åtkomst med hjälp av OAuth 2.0 tokenbaserad mekanism ger överlägsen säkerhet och användarvänlighet via SAS. De inbyggda rollerna kan också eliminera behovet av ACL-baserad auktorisering, som måste underhållas och hanteras av användaren. Du kan använda den här funktionen med dina Kafka-klienter genom att ange **SASL_SSL** för protokollet och **PLAIN** för mekanismen. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Exempel 
En **självstudiekurs** med steg-för-steg-instruktioner för att skapa en händelsehubb och komma åt den med SAS eller OAuth finns i [Snabbstart: Datastreaming med Event Hubs med Kafka-protokollet](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Fler **exempel** som visar hur du använder OAuth med eventhubbar för Kafka finns [i exempel på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andra eventhubbar funktioner tillgängliga för Kafka

Med funktionen Event Hubs for Kafka kan du skriva med ett protokoll och läsa med ett annat, så att dina nuvarande Kafka-producenter kan fortsätta publicera via Kafka, och du kan lägga till läsare med eventhubbar, till exempel Azure Stream Analytics eller Azure Functions. Dessutom fungerar eventhubbar-funktioner som [Capture](event-hubs-capture-overview.md) och [Geo Disaster-Recovery](event-hubs-geo-dr.md) också med funktionen Event Hubs for Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funktioner som ännu inte stöds 

Här är listan över Kafka funktioner som ännu inte stöds:

*   Idempotent producent
*   Transaktion
*   Komprimering
*   Storleksbaserad kvarhållning
*   Loggkomprimering
*   Lägga till partitioner i ett befintligt ämne
*   STÖD för HTTP Kafka API
*   Kafka strömmar

## <a name="next-steps"></a>Nästa steg

Den här artikeln gav en introduktion till Event Hubs för Kafka. Mer information finns i följande länkar:

- [Så här skapar du en händelsehubb](event-hubs-create.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med ett händelsenav](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka-strömmar till ett händelsenav](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


