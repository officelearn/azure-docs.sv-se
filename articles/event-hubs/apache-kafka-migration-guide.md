---
title: Migrera till Azure-händelsehubbar för Apache Kafka
description: Den här artikeln visar hur konsumenter och producenter som använder olika protokoll (AMQP, Apache Kafka och HTTPS) kan utbyta händelser när de använder Azure Event Hubs.
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
ms.openlocfilehash: 806a1f16327ad72a7f3527c813b355e1ba807dda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606761"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrera till Azure-händelsehubbar för Apache Kafka-ekosystem
Azure Event Hubs visar en Apache Kafka-slutpunkt, som gör att du kan ansluta till eventhubbar med Kafka-protokollet. Genom att göra minimala ändringar i ditt befintliga Kafka-program kan du ansluta till Azure Event Hubs och dra nytta av Azure-ekosystemet. Event Hubs for Kafka stöder [Apache Kafka version 1.0](https://kafka.apache.org/10/documentation.html) och senare.

## <a name="pre-migration"></a>Före migrering 

### <a name="create-an-azure-account"></a>Skapa ett Azure-konto
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.

### <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
Följ steg-för-steg-instruktioner i artikeln [Skapa en händelsehubb](event-hubs-create.md) för att skapa ett namnområde för händelsehubbar och en händelsehubb. 

### <a name="connection-string"></a>Anslutningssträng
Följ stegen från hämta [anslutningssträngen från portalartikeln.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) Och anteckna anslutningssträngen för senare användning. 

### <a name="fully-qualified-domain-name-fqdn"></a>Fullständigt kvalificerat domännamn (FQDN)
Du kan också behöva FQDN som pekar på namnområdet Event Hub. FQDN finns i anslutningssträngen enligt följande:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Om namnområdet Event Hubs distribueras i ett icke-offentligt moln kan \*domännamnet skilja \*sig åt (till \*exempel .servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net eller .servicebus.cloudapi.de).

## <a name="migration"></a>Migrering 

### <a name="update-your-kafka-client-configuration"></a>Uppdatera kafka-klientkonfigurationen

Om du vill ansluta till en Kafka-aktiverad eventhubb måste du uppdatera Kafka-klientkonfigurationerna. Om du har problem med att hitta `bootstrap.servers` din kan du prova att söka efter var som anges i programmet.

Infoga följande konfigurationer var det passar i ditt program. Se till att `bootstrap.servers` `sasl.jaas.config` uppdatera och värden för att dirigera klienten till din Event Hubs Kafka-slutpunkt med rätt autentisering. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Om `sasl.jaas.config` inte är en konfiguration som stöds i ramverket letar du reda på de konfigurationer som används för att ange SASL-användarnamnet och lösenordet och använda dem i stället. Ställ in `$ConnectionString` användarnamnet på och lösenordet till anslutningssträngen för eventhubbar.

## <a name="post-migration"></a>Efter migreringen
Kör ditt Kafka-program som skickar händelser till händelsehubben. Kontrollera sedan att händelsehubben tar emot händelserna med Azure-portalen. Växla till **meddelandevyn** i avsnittet **Mått** på sidan **Översikt** i namnområdet Händelserhubbar. Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har tagits emot. 

[![Kontrollera att händelsehubben har tagit emot meddelandena](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Nästa steg
Mer information om eventhubbar och händelsehubbar för Kafka finns i följande artiklar:  

- [Felsökningsguide för Apache Kafka för eventhubbar](apache-kafka-troubleshooting-guide.md)
- [Vanliga frågor och svar - Event Hubs för Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Utvecklarguide för Apache Kafka för Azure Event Hubs](apache-kafka-developer-guide.md)
- [Rekommenderade konfigurationer](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
- 