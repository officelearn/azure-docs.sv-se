---
title: Med Azure Event Hubs Kafka MirrorMaker för Kafka ekosystem | Microsoft Docs
description: Använd Kafka MirrorMaker för spegling av ett Kafka kluster i Händelsehubbar.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 819071321d5609728e7c62abb5b25bf354107850
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Med Händelsehubbar Kafka MirrorMaker för Kafka ekosystem

> [!NOTE]
> Det här exemplet är tillgängligt på [GitHub](https://github.com/Azure/azure-event-hubs)

En större för moderna molnappar för skalan är det en möjlighet att uppdatera, förbättra och ändra infrastruktur utan att avbryta tjänsten. Den här kursen visar hur en Kafka aktiverat Event Hub och Kafka MirrorMaker kan integrera en befintlig Kafka pipeline i Azure ”mirroring” Kafka Indataströmmen i tjänsten Event Hub. 

En Azure Event Hubs Kafka slutpunkt kan du ansluta till Azure Event Hubs genom att använda protokollet Kafka (d.v.s. Kafka klienter). Du kan ansluta till Azure Event Hubs och dra nytta av fördelarna med Azure-ekosystemet genom att göra minimala ändringar till en App som Kafka. Kafka aktiverat Händelsehubbar stöder för närvarande Kafka version 1.0 och senare.

Det här exemplet visar hur du spegling förhandlare Kafka i en Kafka aktiverade händelsehubb med hjälp av Kafka MirrorMaker.

   ![Kafka MirrorMaker med Händelsehubbar](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har den här kursen:

* En Azure-prenumeration. Om du inte har någon, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Hämta](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett binärt Maven-Arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett namnområde för Händelsehubbar

Ett namnområde för Händelsehubbar krävs för att skicka och ta emot från alla händelsehubbtjänsten. Se [skapar en Kafka aktiverat Event Hub](event-hubs-create.md) anvisningar om hur du hämtar en Event Hubs Kafka slutpunkt. Se till att kopiera anslutningssträngen Händelsehubbar för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har aktiverat en Kafka Händelsehubbar anslutningssträngen, klona lagringsplatsen för Azure Event Hubs och navigera till den `mirror-maker` undermapp:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurera ett Kafka kluster

Använd den [Kafka Snabbstartsguide](https://kafka.apache.org/quickstart) att konfigurera ett kluster med de önskade inställningarna (eller använda ett befintligt kluster Kafka).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker kan ”databasspegling” för en dataström. Angivna käll- och Kafka, kluster, MirrorMaker säkerställer att alla meddelanden som skickas till källklustret tas emot av både käll- och kluster. Det här exemplet visar hur du spegling källa Kafka kluster med ett mål Kafka aktiverat Event Hub. Det här scenariot kan användas för att skicka data från en befintlig Kafka pipeline till Händelsehubbar utan att avbryta flödet av data. 

Mer detaljerad information om Kafka MirrorMaker finns i [Kafka spegling/MirrorMaker guiden](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Konfiguration

Konfigurera Kafka MirrorMaker genom att ge den ett Kafka kluster som konsumenter/källa och en aktiverad händelsehubb Kafka som producenten/mål.

#### <a name="consumer-configuration"></a>Konsumenten konfiguration

Uppdatera konfigurationsfilen konsumenten `source-kafka.config`, som talar om MirrorMaker egenskaper för datakälla Kafka klustret.

##### <a name="source-kafkaconfig"></a>käll-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producenten konfiguration

Uppdatera nu producenten konfigurationsfilen `mirror-eventhub.config`, som anger MirrorMaker skicka duplicerade (eller ”speglade”) data till händelsehubbtjänsten. Mer specifikt kan ändra `bootstrap.servers` och `sasl.jaas.config` att peka till din Event Hubs Kafka slutpunkt. Händelsehubbtjänsten kräver säker (SASL)-kommunikation som uppnås genom att ange de sista tre egenskaperna i följande konfiguration: 

##### <a name="mirror-eventhubconfig"></a>spegling eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>Kör MirrorMaker

Kör skriptet Kafka MirrorMaker från roten Kafka katalogen med hjälp av de nyligen uppdaterad konfigurationsfilerna. Se till att kopiera config-filer till roten Kafka directory eller uppdatera sökvägarna i följande kommando.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Du kontrollerar att händelser ansluter till händelsehubben Kafka aktiverad i ingång statistik i den [Azure-portalen](https://azure.microsoft.com/features/azure-portal/), eller kör en konsument mot händelsehubben.

Eventuella händelser som skickats till källan Kafka klustret tas emot av både Kafka klustret med MirrorMaker körs, och speglade Kafka aktiverat event hub-tjänsten. Med hjälp av MirrorMaker och en slutpunkt för Event Hubs Kafka, kan du migrera en befintlig Kafka pipeline till hanterade Händelsehubbar i Azure-tjänsten utan att ändra det befintliga klustret eller att avbryta alla pågående dataflöde.

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om Händelsehubbar](event-hubs-what-is-event-hubs.md)
* [Läs mer om Händelsehubbar för Kafka ekosystem](event-hubs-for-kafka-ecosystem-overview.md)
* Lär dig mer om [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) till dataströmmen händelser från Kafka lokalt till Kafka aktiverat händelsehubbar i molnet.
