---
title: Använda händelsehubben från Apache Kafka app – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om Apache Kafka support av Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 733623895176bb3b573c2efcbda8a40b9e2d87c0
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85320536"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Använda Azure Event Hubs från Apache Kafka-program
Event Hubs tillhandahåller en Kafka-slutpunkt som kan användas av dina befintliga Kafka-baserade program som ett alternativ till att köra ditt eget Kafka-kluster. Event Hubs stöder [Apache Kafka protocol 1,0 och senare](https://kafka.apache.org/documentation/)och fungerar med dina befintliga Kafka-program, inklusive MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vad innebär Event Hubs för Kafka?

Funktionen Event Hubs för Kafka tillhandahåller ett protokoll huvud ovanpå Azure Event Hubs som är binärt kompatibelt med Kafka-versionerna 1,0 och senare för att både läsa från och skriva till Kafka ämnen. Du kan börja använda Kafka-slutpunkten från dina program utan kod ändring men en minimal konfigurations ändring. Du uppdaterar anslutnings strängen i konfigurationer för att peka på Kafka-slutpunkten som exponeras av händelsehubben i stället för att peka på ditt Kafka-kluster. Sedan kan du starta strömmande händelser från dina program som använder Kafka-protokollet i Event Hubs. Den här integrationen stöder också ramverk som [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), som för närvarande är en för hands version. 

Konceptuellt Kafka och Event Hubs är nästan identiska: de är både partitionerade loggar som skapats för strömmande data. Följande tabell mappar begrepp mellan Kafka och Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Konceptuell mappning för Kafka och Event Hub

| Kafka-koncept | Event Hubs koncept|
| --- | --- |
| Kluster | Namnområde |
| Ämne | Händelsehubb |
| Partition | Partition|
| Konsument grupp | Konsument grupp |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Viktiga skillnader mellan Kafka och Event Hubs

Även om [Apache Kafka](https://kafka.apache.org/) är program vara, som du kan köra var du än väljer, är Event Hubs en moln tjänst som liknar Azure Blob Storage. Det finns inga servrar eller nätverk att hantera och inga hanterare att konfigurera. Du skapar ett namn område, som är ett FQDN där dina ämnen är Live och sedan skapar Event Hubs eller ämnen inom det namn området. Mer information om Event Hubs och namn områden finns i [Event Hubs funktioner](event-hubs-features.md#namespace). Som en moln tjänst använder Event Hubs en enda stabil virtuell IP-adress som slut punkten, så att klienterna inte behöver känna till utlösarna eller datorerna i ett kluster. 

Skala i Event Hubs styrs av hur många data flödes enheter du köper, med varje data flödes enhet, vilket innebär att du får 1 MB per sekund eller 1000 händelser per sekund. Som standard skalar Event Hubs data flödes enheter när du når gränsen med funktionen för [Automatisk](event-hubs-auto-inflate.md) ökning. den här funktionen fungerar också med Event Hubs för Kafka-funktionen. 

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
En **själv studie kurs** med stegvisa instruktioner för att skapa en händelsehubben och komma åt den med SAS eller OAuth finns i [snabb start: Data strömning med Event Hubs med Kafka-protokollet](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Fler **exempel** som visar hur du använder OAuth med Event Hubs för Kafka finns i [exempel på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andra Event Hubs funktioner som är tillgängliga för Kafka

Med funktionen Event Hubs för Kafka kan du skriva med ett protokoll och läsa med ett annat, så att dina befintliga Kafka-producenter kan fortsätta att publicera via Kafka och du kan lägga till läsare med Event Hubs, till exempel Azure Stream Analytics eller Azure Functions. Dessutom fungerar Event Hubs funktioner som [fånga](event-hubs-capture-overview.md) och [geo haveri beredskap](event-hubs-geo-dr.md) med funktionen Event Hubs för Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funktioner som ännu inte stöds 

Här är en lista över Kafka-funktioner som ännu inte stöds:

*   Idempotenta-tillverkare
*   Transaktion
*   Komprimering
*   Storleks beroende kvarhållning
*   Logg komprimering
*   Lägga till partitioner i ett befintligt ämne
*   API-stöd för HTTP-Kafka
*   Kafka strömmar

## <a name="next-steps"></a>Nästa steg
I den här artikeln angav vi en introduktion till Event Hubs för Kafka. Mer information finns i [Apache Kafka Developer Guide for Azure Event Hubs](apache-kafka-developer-guide.md).


