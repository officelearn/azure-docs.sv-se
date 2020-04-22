---
title: Utvecklarguide för Apache Kafka för Event Hubs
description: Den här artikeln innehåller länkar till artiklar som beskriver hur du integrerar dina Kafka-program med Azure Event Hubs.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: b4d9dc51451a06b2c87ddce78547655b51c6fabd
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729631"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Utvecklarguide för Apache Kafka för Azure Event Hubs
Den här artikeln innehåller länkar till artiklar som beskriver hur du integrerar dina Apache Kafka-program med Azure Event Hubs. 

## <a name="overview"></a>Översikt
Event Hubs tillhandahåller en Kafka-slutpunkt som kan användas av dina befintliga Kafka-baserade program som ett alternativ till att köra ditt eget Kafka-kluster. Event Hubs stöder Apache Kafka protokoll 1.0 och senare, och fungerar med dina befintliga Kafka-program, inklusive MirrorMaker. Mer information finns i [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Snabbstarter
Du hittar snabbstarter i GitHub och i den här innehållsuppsättningen som hjälper dig att snabbt rampa upp på Event Hubs för Kafka.

### <a name="quickstarts-in-github"></a>Snabbstarter i GitHub
Se följande snabbstarter i **azure-event-hubs-for-kafka** repo: 

| Klientspråk/ramverk | Beskrivning | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Den här snabbstarten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av en exempelproducent och konsument skriven i C# med .NET Core 2.0.</p><p>Det här exemplet är baserat på [Confluents Apache Kafka .NET-klient](https://github.com/confluentinc/confluent-kafka-dotnet), modifierad för användning med Event Hubs för Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Den här snabbstarten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av en exempelproducent och konsument skriven i Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Den här snabbstarten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av en exempelproducent och konsument skriven i Nod.</p><p>I det här exemplet används [nod-rdkafka-biblioteket.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Den här snabbstarten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av en exempelproducent och konsument skriven i python.</p><p>Det här exemplet är baserat på [Confluents Apache Kafka Python-klient](https://github.com/confluentinc/confluent-kafka-python), modifierad för användning med Event Hubs för Kafka.</p>|
| [Kör](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Den här snabbstarten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av en exempelproducent och konsument skriven i Go.</p><p>Det här exemplet är baserat på [Confluents Apache Kafka Golang-klient](https://github.com/confluentinc/confluent-kafka-go), modifierad för användning med Event Hubs för Kafka.</p>| 
| [Sarama kafka Gå](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Den här snabbstarten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av en exempelproducent och konsument skriven i Go med hjälp av [Sarama Kafka-klientbiblioteket.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Den här snabbstarten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av CLI som levereras med Apache Kafka-distributionen.| 
| [Kafkacat (kafkacat)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat är en icke-JVM command-line konsument och producent baserad på librdkafka, populär på grund av sin hastighet och små fotavtryck. Den här snabbstarten innehåller en exempelkonfiguration och flera enkla exempelkafkacatkommandon. | 
 
### <a name="quickstarts-in-docs"></a>Snabbstarter i DOCS
Se snabbstarten: [Datastreaming med Event Hubs med Kafka-protokollet](event-hubs-quickstart-kafka-enabled-event-hubs.md) i den här innehållsuppsättningen, som innehåller steg-för-steg-instruktioner om hur du streamar till eventhubbar. Du lär dig hur du använder dina producenter och konsumenter för att prata med Event Hubs med bara en konfigurationsändring i dina program. 


## <a name="tutorials"></a>Självstudier 

### <a name="tutorials-in-github"></a>Självstudier i GitHub
Se följande självstudier på GitHub:

| Självstudier | Beskrivning | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Den här självstudien visar hur du ansluter Akka-strömmar till Kafka-aktiverade eventhubbar utan att ändra dina protokollklienter eller köra egna kluster. Det finns två separata självstudier med **programmeringsspråken Java** och **Scala.** | 
| [Anslut](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Det här dokumentet hjälper dig att integrera Kafka Connect med Azure Event Hubs och distribuera grundläggande FileStreamSource- och FileStreamSink-kopplingar. Även om dessa kopplingar inte är avsedda för produktionsanvändning, visar de ett heltäckande Kafka Connect-scenario där Azure Event Hubs maskerar som en Kafka-mäklare.| 
| [Filebeat (filslag)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Detta dokument kommer att gå igenom integrera Filebeat och Event Hubs via Filebeat's Kafka utgång. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Den här självstudien visar hur du ansluter Apache Flink till Kafka-aktiverade eventhubbar utan att ändra dina protokollklienter eller köra egna kluster. | 
| [Flytande](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Detta dokument kommer att gå igenom integrera Fluentd `out_kafka` och Event Hubs med hjälp av produktionen plugin för Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Den här självstudien visar hur du utbyter händelser mellan konsumenter och producenter med hjälp av olika protokoll. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Denna handledning kommer att gå igenom integrera Logstash med Kafka-aktiverade Event Hubs med Logstash Kafka input / output plugins. | 
| [Spegelmakare](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Den här självstudien visar hur en händelsehubb och Kafka MirrorMaker kan integrera en befintlig Kafka-pipeline i Azure genom att spegla Kafka-indataströmmen i tjänsten Event Hubs. |
| [NiFi (na)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Den här självstudien visar hur du ansluter Apache NiFi till ett namnområde för eventhubbar. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Snabbstarter visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av en exempelproducent och konsument skriven i programmeringsspråken Go och Java. |
| [Confluents schemaregister](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Den här självstudien hjälper dig att integrera schemaregister och händelsehubbar för Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Den här självstudien visar hur du ansluter Spark-programmet till en händelsehubb utan att ändra dina protokollklienter eller köra dina egna Kafka-kluster. | 

### <a name="tutorials-in-docs"></a>Självstudier i DOCS
Se även självstudien: [Bearbeta Apache Kafka för händelsehubbar händelser med hjälp av Stream analytics](event-hubs-kafka-stream-analytics.md) i den här innehållsuppsättningen, som visar hur du streamar data till eventhubbar och bearbetar den med Azure Stream Analytics.

## <a name="how-to-guides"></a>Anvisningar
Se följande how-to-guider i vår dokumentation:

| Artikel | Beskrivning | 
| ------- | ----------- | 
| [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md) | Visar hur du speglar en Kafka-mäklare i ett händelsenav med Kafka MirrorMaker. |
| [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md) | Guidar dig genom att ansluta ditt Spark-program till Event Hubs för direktuppspelning i realtid. |
| [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md) | Visar hur du ansluter Apache Flink till en händelsehubb utan att ändra dina protokollklienter eller köra egna kluster. |
| [Integrera Apache Kafka Connect med ett händelsenav (förhandsversion)](event-hubs-kafka-connect-tutorial.md) | Här går du igenom integreringen av Kafka Connect med en händelsehubb och distribuerar grundläggande FileStreamSource- och FileStreamSink-kopplingar. |
| [Ansluta Akka-dataströmmar till en händelsehubb](event-hubs-kafka-akka-streams-tutorial.md) | Visar hur du ansluter Akka Streams till en händelsehubb utan att ändra dina protokollklienter eller köra egna kluster. |
| [Använda Spring Boot Starter för Apache Kafka med Azure Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Visar hur du konfigurerar en Java-baserad Spring Cloud Stream Binder som skapats med Spring Boot Initializer för att använda Apache Kafka med Azure Event Hubs. |

## <a name="next-steps"></a>Nästa steg
Granska exempel i mapparna GitHub repo [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) under snabbstart och självstudier.

Se även följande artiklar:

- [Felsökningsguide för Apache Kafka för eventhubbar](apache-kafka-troubleshooting-guide.md)
- [Vanliga frågor och svar - Event Hubs för Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guiden Apache Kafka-migrering för eventhubbar](apache-kafka-migration-guide.md)



