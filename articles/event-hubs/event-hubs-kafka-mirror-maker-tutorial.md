---
title: Använd MirrorMaker för Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Kafka MirrorMaker för spegling av ett Kafka-kluster i AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: a7271eb6b8cbc8a117b5a8e75edfe02985ec3452
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821538"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Använd MirrorMaker för Kafka med Event Hubs för Apache Kafka

Den här självstudien visar hur du speglar en Kafka-meddelandeköer i en Kafka aktiverade händelsehubb med hjälp av Kafka MirrorMaker.

   ![Kafka MirrorMaker med Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Konfigurera ett Kafka-kluster
> * Konfigurera Kafka MirrorMaker
> * Kör Kafka MirrorMaker

## <a name="introduction"></a>Introduktion
En är större skala för moderna molnappar möjligheten att uppdatera, förbättra och ändra infrastrukturen utan att avbryta tjänsten. Den här kursen visar hur en Kafka-aktiverade event hub och Kafka MirrorMaker kan integrera en befintlig pipeline för Kafka i Azure genom att ”spegla” Kafka Indataströmmen i Event Hubs-tjänsten. 

En Kafka för Azure Event Hubs-slutpunkt kan du ansluta till Azure Event Hubs med Kafka-protokollet (det vill säga Kafka-klienter). Genom att göra minimala ändringar till ett Kafka-program, kan du ansluta till Azure Event Hubs och dra nytta av fördelarna med Azure-ekosystemet. Kafka aktiverat Event Hubs stöder för närvarande Kafka version 1.0 och senare.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

En Event Hubs-namnrymd krävs för att skicka och ta emot från Event Hubs-tjänster. Instruktioner om hur du skaffar en Event Hubs Kafka-slutpunkt finns i avsnittet om att [skapa en Kafka-aktiverad händelsehubb](event-hubs-create.md). Se till att kopiera anslutningssträngen Event Hubs för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har aktiverat en Kafka Event Hubs anslutningssträng, klona Azure Event Hubs för Kafka-lagringsplats och navigera till den `mirror-maker` undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurera ett Kafka-kluster

Använd den [Kafka Snabbstartsguide](https://kafka.apache.org/quickstart) att konfigurera ett kluster med de önskade inställningarna (eller använda ett befintligt Kafka-kluster).

## <a name="configure-kafka-mirrormaker"></a>Konfigurera Kafka MirrorMaker

Kafka MirrorMaker kan ”databasspegling” av en dataström. Angivna käll- och Kafka-kluster, MirrorMaker säkerställer att alla meddelanden som skickas till källklustret tas emot av både käll- och mål-kluster. Det här exemplet visar hur du speglar källdatorn Kafka-kluster med ett mål Kafka-aktiverade händelsehubb. Det här scenariot kan användas för att skicka data från en befintlig Kafka-pipeline till Event Hubs utan att avbryta flödet av data. 

Mer detaljerad information om Kafka MirrorMaker finns i den [Kafka spegling/MirrorMaker guide](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Om du vill konfigurera Kafka MirrorMaker, ge den ett Kafka-kluster som konsument/källa och en Kafka-aktiverade händelsehubb som producent/mål.

#### <a name="consumer-configuration"></a>Konsument-konfiguration

Uppdatera konfigurationsfilen för konsument `source-kafka.config`, som anger MirrorMaker egenskaperna för källan Kafka-kluster.

##### <a name="source-kafkaconfig"></a>käll-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producent-konfiguration

Nu uppdatera konfigurationsfilen producent `mirror-eventhub.config`, som anger MirrorMaker för att skicka duplicerade (eller ”speglade”) data till Event Hubs-tjänsten. Mer specifikt kan ändra `bootstrap.servers` och `sasl.jaas.config` så att den pekar till Event Hubs Kafka-slutpunkten. Event Hubs-tjänsten kräver säker (SASL)-kommunikation som uppnås genom att ange de senaste tre egenskaperna i följande konfiguration: 

##### <a name="mirror-eventhubconfig"></a>spegling eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Kör Kafka MirrorMaker

Kör skriptet Kafka MirrorMaker från roten Kafka-katalog med hjälp av nya uppdaterade filerna. Se till att kopiera config-filer till roten Kafka directory eller uppdatera sökvägarna i följande kommando.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Om du vill kontrollera att händelser ansluter till Kafka-aktiverade event hub, se inkommande statistik i den [Azure-portalen](https://azure.microsoft.com/features/azure-portal/), eller kör en konsument mot event hub.

Eventuella händelser som skickats till källan Kafka-kluster tas emot av både Kafka-klustret med MirrorMaker som körs, och den speglade Kafka aktiverat event hub-tjänsten. Genom att använda MirrorMaker och en Event Hubs Kafka-slutpunkt kan migrera du en befintlig Kafka-pipeline till hanterade Azure Event Hubs-tjänsten utan att ändra det befintliga klustret eller att avbryta alla pågående dataflöde.

## <a name="samples"></a>Exempel
Se följande exempel på GitHub:

- [Exempelkod för den här självstudiekursen på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Hubs Kafka av Azure Event-MirrorMaker som körs på en Azure Container Instance](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Konfigurera ett Kafka-kluster
> * Konfigurera Kafka MirrorMaker
> * Kör Kafka MirrorMaker

Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create-kafka-enabled.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Ansluta Apache Spark till en Kafka-aktiverad händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en Kafka-aktiverad händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med en Kafka-aktiverad händelsehubb](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka Streams till en Kafka-aktiverad händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
