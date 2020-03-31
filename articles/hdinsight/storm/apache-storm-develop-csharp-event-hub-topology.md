---
title: Bearbeta händelser från eventhubbar med storm – Azure HDInsight
description: Lär dig hur du bearbetar data från Azure Event Hubs med en C# Storm-topologi som skapats i Visual Studio med hjälp av HDInsight-verktygen för Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 85b7093df99127b690c51e8f2f28d18e3f5f3c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981627"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)

Lär dig hur du arbetar med Azure Event Hubs från [Apache Storm](https://storm.apache.org/) på HDInsight. Det här dokumentet använder en C# Storm-topologi för att läsa och skriva data från Event Hubs

> [!NOTE]  
> En Java-version av det här projektet finns i [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (Java).](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

## <a name="scpnet"></a>SCP.NET

Stegen i det här dokumentet använder SCP.NET, ett NuGet-paket som gör det enkelt att skapa C# topologier och komponenter för användning med Storm på HDInsight.

HDInsight 3.4 och större användning Mono för att köra C # topologier. Exemplet som används i det här dokumentet fungerar med HDInsight 3.6. Om du planerar att skapa egna .NET-lösningar för HDInsight kontrollerar du om det finns potentiella inkompatibiliteter i dokumentet Mono.If you plan on creating your own .NET solutions for HDInsight, check the [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) document for potential incompatibilities.

### <a name="cluster-versioning"></a>Klusterversion

Microsoft.SCP.Net.SDK NuGet-paketet som du använder för projektet måste matcha huvudversionen av Storm som är installerad på HDInsight. HDInsight version 3.5 och 3.6 använder Storm 1.x, så du måste använda SCP.NET version 1.0.x.x med dessa kluster.

C# topologier måste också rikta .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Så här arbetar du med eventhubbar

Microsoft tillhandahåller en uppsättning Java-komponenter som kan användas för att kommunicera med eventhubbar från en Storm-topologi. Du hittar java-arkivfilen (JAR) som innehåller en HDInsight 3.6-kompatibel version av dessa komponenter på [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]  
> Medan komponenterna är skrivna i Java, kan du enkelt använda dem från en C # topologi.

Följande komponenter används i det här exemplet:

* __EventHubSpout__: Läser data från eventhubbar.
* __EventHubBolt__: Skriver data till eventhubbar.
* __EventHubSpoutConfig__: Används för att konfigurera EventHubSpout.
* __EventHubBoltConfig__: Används för att konfigurera EventHubBolt.

### <a name="example-spout-usage"></a>Exempel på pipanvändning

SCP.NET innehåller metoder för att lägga till en EventHubSpout i din topologi. Dessa metoder gör det enklare att lägga till en pip än att använda de allmänna metoderna för att lägga till en Java-komponent. I följande exempel visas hur du skapar en pip med hjälp av metoderna __SetEventHubSpout__ och **EventHubSpoutConfig** som tillhandahålls av SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

I föregående exempel skapas en ny pipkomponent med namnet __EventHubSpout__och den konfigureras för att kommunicera med en händelsehubb. Parallellitetstipset för komponenten är inställt på antalet partitioner i händelsehubben. Med den här inställningen kan Storm skapa en instans av komponenten för varje partition.

### <a name="example-bolt-usage"></a>Exempel på bultanvändning

Använd metoden **JavaComponmentConstructor** för att skapa en instans av bulten. I följande exempel visas hur du skapar och konfigurerar en ny instans av **EventHubBolt:**

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> I det här exemplet används ett Clojure-uttryck som skickas som en sträng i stället för att använda **JavaComponentConstructor** för att skapa en **EventHubBoltConfig**, som pipexemplet gjorde. Båda metoderna fungerar. Använd den metod som känns bäst för dig.

## <a name="download-the-completed-project"></a>Ladda ner det slutförda projektet

Du kan hämta en fullständig version av projektet som skapats i den här artikeln från [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Du måste dock fortfarande ange konfigurationsinställningar genom att följa stegen i den här artikeln.

### <a name="prerequisites"></a>Krav

* Ett Apache Storm-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **klustertyp**.

* En [Azure-händelsehubb](../../event-hubs/event-hubs-create.md).

* [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* [HDInsight-verktygen för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 eller senare på din utvecklingsmiljö. JDK nedladdningar finns tillgängliga från [Oracle](https://aka.ms/azure-jdks).

  * Variabeln **JAVA_HOME** miljö måste peka på katalogen som innehåller Java.
  * **Katalogen %JAVA_HOME%/bin** måste finnas i sökvägen.

## <a name="download-the-event-hubs-components"></a>Ladda ned komponenterna Event Hubs

Hämta pip- och bultkomponenten [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)Event Hubs från .

Skapa en `eventhubspout`katalog med namnet och spara filen i katalogen.

## <a name="configure-event-hubs"></a>Konfigurera händelsehubbar

Event Hubs är datakällan för det här exemplet. Använd informationen i avsnittet "Skapa en händelsehubb" i [Kom igång med händelsehubbar](../../event-hubs/event-hubs-create.md).

1. När händelsehubben har skapats visar du **EventHub-inställningarna** i Azure-portalen och väljer **Principer för delad åtkomst**. Välj **+ Lägg till** om du vill skapa följande principer:

   | Namn | Behörigheter |
   | --- | --- |
   | Författare |Skicka |
   | Läsare |Lyssna |

    ![Skärmbild av fönstret Dela åtkomstprinciper](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Välj **läsar-** och **skrivpolicy.** Kopiera och spara primärnyckelvärdet för båda principerna, eftersom dessa värden används senare.

## <a name="configure-the-eventhubwriter"></a>Konfigurera EventHubWriter

1. Om du inte redan har installerat den senaste versionen av HDInsight-verktygen för Visual Studio läser [du Komma igång med HDInsight-verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Ladda ner lösningen från [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Öppna **EventHubExample.sln**. Öppna **filen App.config** i **EventHubWriter-projektet.** Använd informationen från händelsehubben som du konfigurerat tidigare för att fylla i värdet för följande nycklar:

   | Nyckel | Värde |
   | --- | --- |
   | EventHubPolicyName |författare (Om du använde ett annat namn för principen med *skicka* behörighet, använd den i stället.) |
   | EventHubPolicyKey |Nyckeln till skribentens policy. |
   | EventHubNamespace |Namnområdet som innehåller händelsehubben. |
   | EventHubName |Namnet på händelsehubben. |
   | EventHubPartitionCount |Antalet partitioner i händelsehubben. |

4. Spara och stäng **filen App.config.**

## <a name="configure-the-eventhubreader"></a>Konfigurera EventHubReader

1. Öppna **EventHubReader-projektet.**

2. Öppna **filen App.config** för **EventHubReader**. Använd informationen från händelsehubben som du konfigurerat tidigare för att fylla i värdet för följande nycklar:

   | Nyckel | Värde |
   | --- | --- |
   | EventHubPolicyName |(Om du använde ett annat namn *listen* för principen med lyssningsbehörighet använder du det i stället.) |
   | EventHubPolicyKey |Nyckeln till läsarpolicyn. |
   | EventHubNamespace |Namnområdet som innehåller händelsehubben. |
   | EventHubName |Namnet på händelsehubben. |
   | EventHubPartitionCount |Antalet partitioner i händelsehubben. |

3. Spara och stäng **filen App.config.**

## <a name="deploy-the-topologies"></a>Distribuera topologier

1. Högerklicka på **EventHubReader-projektet** från **Solution Explorer**och välj Skicka till Storm **på HDInsight**.

    ![Skärmbild av Solution Explorer, med Skicka till Storm på HDInsight markerat](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. Markera **stormklustret**i dialogrutan **Skicka topologi** . Expandera **ytterligare konfigurationer,** välj **Java-filsökvägar**, välj **...** och välj den katalog som innehåller JAR-filen som du hämtade tidigare. Klicka slutligen på **Skicka**.

    ![Skärmbild av dialogrutan Skicka topologi](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. När topologin har skickats visas **Storm Topologies Viewer.** Om du vill visa information om topologin väljer du Topologin **EventHubReader** i den vänstra rutan.

    ![Skärmbild av Storm Topologies Viewer](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. Högerklicka på **EventHubWriter-projektet** från **Solution Explorer**och välj Skicka till Storm **på HDInsight**.

5. Markera **stormklustret**i dialogrutan **Skicka topologi** . Expandera **ytterligare konfigurationer,** välj **Java-filsökvägar**, välj **...** och välj den katalog som innehåller JAR-filen som du hämtade tidigare. Klicka slutligen på **Skicka**.

6. När topologin har skickats uppdaterar du topologilistan i **Storm Topologies Viewer** för att kontrollera att båda topologierna körs i klustret.

7. I **Storm Topologies Viewer**väljer du Topologin **EventHubReader.**

8. Om du vill öppna komponentsammanfattningen för bulten dubbelklickar du på **LogBolt-komponenten** i diagrammet.

9. I avsnittet **Utförare** väljer du en av länkarna i kolumnen **Port.** Då visas information som loggats av komponenten. Den loggade informationen liknar följande text:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Stoppa topologierna

Om du vill stoppa topologierna väljer du varje topologi i **Storm-topologivisaren**och klickar sedan på **Döda**.

![Skärmbild av Storm Topologi Viewer, med killknappen markerad](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Pipen för Java-händelsehubbar och bult från en C#-topologi för att arbeta med data i Azure Event Hubs. Mer information om hur du skapar C#-topologier finns i följande:

* [Utveckla C# topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Programmeringsguide för SCP](apache-storm-scp-programming-guide.md)
* [Exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md)
