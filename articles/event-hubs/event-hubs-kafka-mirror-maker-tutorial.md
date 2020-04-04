---
title: Använd Apache Kafka MirrorMaker - Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller information om hur du använder Kafka MirrorMaker för att spegla ett Kafka-kluster i AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: fb041ec0d3cd474cca12d5ad55b733337566b9cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632789"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Använd Kafka MirrorMaker med eventhubbar för Apache Kafka

Den här självstudien visar hur du speglar en Kafka-mäklare i ett händelsenav med Kafka MirrorMaker.

   ![Kafka MirrorMaker med eventhubbar](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Konfigurera ett Kafka-kluster
> * Konfigurera Kafka MirrorMaker
> * Kör Kafka MirrorMaker

## <a name="introduction"></a>Introduktion
En viktig faktor för moderna molnskalaappar är möjligheten att uppdatera, förbättra och ändra infrastruktur utan att avbryta tjänsten. Den här självstudien visar hur en händelsehubb och Kafka MirrorMaker kan integrera en befintlig Kafka-pipeline i Azure genom att "spegla" Kafka-indataströmmen i eventhubs-tjänsten. 

Med en Azure Event Hubs Kafka-slutpunkt kan du ansluta till Azure Event Hubs med Kafka-protokollet (det vill säga Kafka-klienter). Genom att göra minimala ändringar i ett Kafka-program kan du ansluta till Azure Event Hubs och dra nytta av fördelarna med Azure-ekosystemet. Event Hubs stöder för närvarande Kafka version 1.0 och senare.

## <a name="prerequisites"></a>Krav

För att kunna följa den här självstudien måste du ha:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1,7+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ner](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven binärt arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

En Event Hubs-namnrymd krävs för att skicka och ta emot från Event Hubs-tjänster. Se [Skapa en händelsehubb](event-hubs-create.md) för instruktioner om hur du skapar ett namnområde och en händelsehubb. Kontrollera att du kopierar anslutningssträngen för händelsehubbar för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har en anslutningssträng för eventhubner klonar du Azure `mirror-maker` Event Hubs för Kafka-databasen och navigerar till undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurera ett Kafka-kluster

Använd [snabbstartsguiden för Kafka](https://kafka.apache.org/quickstart) för att konfigurera ett kluster med önskade inställningar (eller använd ett befintligt Kafka-kluster).

## <a name="configure-kafka-mirrormaker"></a>Konfigurera Kafka MirrorMaker

Kafka MirrorMaker möjliggör "spegling" av en ström. Med tanke på kafka-kluster för källa och mål säkerställer MirrorMaker att alla meddelanden som skickas till källklustret tas emot av både käll- och målkluster. Det här exemplet visar hur du speglar ett kafka-källkluster med en målhändelsenav. Det här scenariot kan användas för att skicka data från en befintlig Kafka-pipeline till eventhubbar utan att avbryta dataflödet. 

För mer detaljerad information om Kafka MirrorMaker, se [Kafka Spegling / MirrorMaker guide](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Om du vill konfigurera Kafka MirrorMaker ger du det ett Kafka-kluster som konsument-/käll- och händelsenav som producent/mål.

#### <a name="consumer-configuration"></a>Konsumentkonfiguration

Uppdatera konsumentkonfigurationsfilen `source-kafka.config`, som talar om för MirrorMaker egenskaperna för källkafka-klustret.

##### <a name="source-kafkaconfig"></a>källa-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producentkonfiguration

Nu uppdatera producentkonfigurationsfilen `mirror-eventhub.config`, som talar om för MirrorMaker att skicka dubblettdata (eller "speglade") till tjänsten Event Hubs. Ändra `bootstrap.servers` och `sasl.jaas.config` peka på slutpunkten För eventhubbar Kafka. Event Hubs-tjänsten kräver säker (SASL) kommunikation, vilket uppnås genom att ange de tre sista egenskaperna i följande konfiguration: 

##### <a name="mirror-eventhubconfig"></a>spegel-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Kör Kafka MirrorMaker

Kör Kafka MirrorMaker-skriptet från rotkafkakatalogen med hjälp av de nyligen uppdaterade konfigurationsfilerna. Se till att antingen kopiera config-filerna till rotkafkakatalogen eller uppdatera deras sökvägar i följande kommando.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Information om hur du kontrollerar att händelser når händelsehubben läser du ingressstatistiken i [Azure-portalen](https://azure.microsoft.com/features/azure-portal/)eller kör en konsument mot händelsehubben.

När MirrorMaker körs tas alla händelser som skickas till kafkaklustret emot av både Kafka-klustret och den speglade händelsehubben. Genom att använda MirrorMaker och en Event Hubs Kafka-slutpunkt kan du migrera en befintlig Kafka-pipeline till den hanterade Azure Event Hubs-tjänsten utan att ändra det befintliga klustret eller avbryta något pågående dataflöde.

## <a name="samples"></a>Exempel
Se följande exempel på GitHub:

- [Exempelkod för den här självstudien på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker körs på en Azure Container-instans](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Nästa steg
Mer information om eventhubbar för Kafka finns i följande artiklar:  

- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med ett händelsenav](event-hubs-kafka-connect-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Ansluta Akka-strömmar till ett händelsenav](event-hubs-kafka-akka-streams-tutorial.md)
- [Utvecklarguide för Apache Kafka för Azure Event Hubs](apache-kafka-developer-guide.md)