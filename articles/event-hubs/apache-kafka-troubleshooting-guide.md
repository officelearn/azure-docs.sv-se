---
title: Felsöka problem med Azure Event Hubs för Apache Kafka
description: Den här artikeln visar hur du felsöker problem med Azure Event Hubs för Apache Kafka
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606735"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Felsökningsguide för Apache Kafka för eventhubbar
Den här artikeln innehåller felsökningstips för problem som du kan stöta på när du använder Event Hubs för Apache Kafka. 

## <a name="server-busy-exception"></a>Undantag för server upptagen
Du kan få server busy undantag på grund av Kafka begränsning. Med AMQP-klienter returnerar Event Hubs omedelbart ett **serverupptaget** undantag vid servicebegränsning. Det motsvarar ett "försök igen senare"-meddelande. I Kafka försenas meddelanden innan de slutförs. Fördröjningslängden returneras i millisekunder `throttle_time_ms` som i produkten/hämtningssvaret. I de flesta fall loggas inte dessa försenade begäranden som ServerBusy-undantag på instrumentpaneler för händelsehubbar. I stället bör `throttle_time_ms` svarets värde användas som en indikator på att dataflödet har överskridit den etablerade kvoten.

Om trafiken är överdriven har tjänsten följande beteende:

- Om fördröjningen för begäran överskrider tidsgränsen för begäran returnerar eventhubbar **felkod för principöverträdelse.**
- Om fördröjningen för hämtning av begäran överskrider tidsgränsen för begäran loggar Event Hubs begäran som begränsad och svarar med tom uppsättning poster och ingen felkod.

[Dedikerade kluster](event-hubs-dedicated-overview.md) har inte begränsningsmekanismer. Du kan använda alla klusterresurser.

## <a name="no-records-received"></a>Inga mottagna poster
Du kan se konsumenterna inte får några poster och ständigt ombalansering. I det här fallet får konsumenterna inte några poster och ständigt balansera. Det finns inget undantag eller fel när det händer, men Kafka-loggarna visar att konsumenterna har fastnat försöker gå med i gruppen igen och tilldela partitioner. Det finns några möjliga orsaker:

- Se till `request.timeout.ms` att du är minst det rekommenderade värdet på `session.timeout.ms` 60000 och att du är minst det rekommenderade värdet på 30000. Med dessa inställningar för låg kan orsaka konsumenternas timeout, som sedan orsakar ombalanseringar (som sedan orsakar mer timeout, vilket orsakar mer ombalansering, och så vidare) 
- Om din konfiguration matchar dessa rekommenderade värden, och du fortfarande ser konstant ombalansering, är du välkommen att öppna ett problem (se till att inkludera hela din konfiguration i problemet så att vi kan hjälpa till att felsöka)!

## <a name="compressionmessage-format-version-issue"></a>Versionsproblem med komprimerings-/meddelandeformat
Kafka stöder komprimering och eventhubbar för Kafka gör det för närvarande inte. Fel som nämner en version i meddelandeformat (till `The message format version on the broker does not support the request.`exempel) uppstår när en klient försöker skicka komprimerade Kafka-meddelanden till våra mäklare.

Om komprimerade data är nödvändigt är det en giltig lösning att komprimera dina data innan du skickar dem till mäklare och expanderade efter att ha tagit emot. Meddelandetexten är bara en byte-matris till tjänsten, så komprimering/dekompression på klientsidan orsakar inga problem.

## <a name="unknownserverexception"></a>OkändServerUtställning
Du kan få en UnknownServerException från Kafka-klientbibliotek som liknar följande exempel: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Öppna en biljett med Microsoft-support.  Felsökningsnivå loggning och undantag tidsstämplar i UTC är till hjälp för felsökning av problemet. 

## <a name="other-issues"></a>Andra problem
Kontrollera följande objekt om du ser problem när du använder Kafka på Event Hubs.

- **Brandvägg blockerar trafik** - Kontrollera att port **9093** inte blockeras av brandväggen.
- **TopicAuthorizationException** - De vanligaste orsakerna till det här undantaget är:
    - Ett stavfel i anslutningssträngen i konfigurationsfilen, eller
    - Försöker använda eventhubbar för Kafka på en namnområde på basic-nivå. Event Hubs för Kafka [stöds endast för namnområden på standard och dedikerad nivå](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Kafka version mismatch** - Event Hubs for Kafka Ecosystems stöder Kafka version 1.0 och senare. Vissa program som använder Kafka version 0.10 och senare kan ibland fungera på grund av Kafka-protokollets bakåtkompatibilitet, men vi rekommenderar starkt mot att använda gamla API-versioner. Kafka versioner 0.9 och tidigare stöder inte de nödvändiga SASL-protokollen och kan inte ansluta till Event Hubs.
- **Konstiga kodningar på AMQP-huvuden när de konsumerar med Kafka** – när du skickar händelser till en händelsehubb över AMQP serialiseras alla AMQP-nyttolasthuvuden i AMQP-kodning. Kafka konsumenter inte deserialize rubrikerna från AMQP. Om du vill läsa rubrikvärden avkodar du AMQP-huvudena manuellt. Alternativt kan du undvika att använda AMQP-huvuden om du vet att du kommer att konsumera via Kafka-protokollet. Mer information finns i [det här problemet med GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **SASL-autentisering** – Det kan vara svårare att få ramverket att samarbeta med SASL-autentiseringsprotokollet som krävs av Event Hubs än vad ögat ser. Se om du kan felsöka konfigurationen med hjälp av ramverkets resurser för SASL-autentisering. 

## <a name="limits"></a>Begränsningar
Apache Kafka vs Event Hubs Kafka. För det mesta har eventhubbar för Kafka-ekosystem samma standardvärden, egenskaper, felkoder och allmänt beteende som Apache Kafka gör. De fall där dessa två uttryckligen skiljer sig åt (eller där Event Hubs inför en gräns som Kafka inte) visas nedan:

- Den maximala längden `group.id` på egenskapen är 256 tecken
- Den maximala `offset.metadata.max.bytes` storleken på är 1024 byte
- Förskjutningsantaganden begränsas med 4 anrop/sekund per partition med en maximal intern loggstorlek på 1 MB


## <a name="next-steps"></a>Nästa steg
Mer information om eventhubbar och händelsehubbar för Kafka finns i följande artiklar:  

- [Utvecklarguide för Apache Kafka för Event Hubs](apache-kafka-developer-guide.md)
- [Guiden Apache Kafka-migrering för eventhubbar](apache-kafka-migration-guide.md)
- [Vanliga frågor och svar - Event Hubs för Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Rekommenderade konfigurationer](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
