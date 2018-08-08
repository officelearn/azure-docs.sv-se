---
title: Bearbeta händelser från Event Hubs med Storm - Azure HDInsight
description: Lär dig mer om att bearbeta data från Azure Event Hubs med en C# Storm-topologi som skapats i Visual Studio genom att använda HDInsight tools för Visual Studio.
services: hdinsight,notification hubs
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 24bcc33570a5e62769223c5827556bab1967ae5a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620390"
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Bearbeta händelser från Azure Event Hubs med Storm på HDInsight (C#)

Lär dig hur du arbetar med Azure Event Hubs från Apache Storm på HDInsight. Det här dokumentet använder en C# Storm-topologi för att läsa och skriva data från Event Hubs

> [!NOTE]
> En Java-version av det här projektet finns [bearbeta händelser från Azure Event Hubs med Storm på HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Stegen i det här dokumentet använder SCP.NET, ett NuGet-paket som gör det enkelt att skapa C#-topologier och komponenter för användning med Storm på HDInsight.

> [!IMPORTANT]
> När stegen i det här dokumentet är beroende av en Windows-utvecklingsmiljö med Visual Studio, kan kompilerade projektet skickas till ett Storm på HDInsight-kluster som använder Linux. Linux-baserade kluster som skapats efter den 28 oktober 2016 stöder endast SCP.NET topologier.

HDInsight 3.4 och större använda Mono för att köra C#-topologier. Exemplet i det här dokumentet fungerar med HDInsight 3.6. Om du vill skapa en egen .NET-lösningar för HDInsight, kontrollerar du den [Mono-kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentet för potentiella kompatibilitetsproblem.

### <a name="cluster-versioning"></a>Kluster-versionshantering

Microsoft.SCP.Net.SDK NuGet-paketet som du använder för ditt projekt måste matcha huvudversionen av Storm som är installerad på HDInsight. Använda Storm för HDInsight version 3.5 och 3.6 1.x, så du måste använda SCP.NET version 1.0.x.x med dessa kluster.

> [!IMPORTANT]
> I exemplet i det här dokumentet förväntar sig en HDInsight 3.5 eller 3.6-klustret.
>
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

C#-topologier måste också ha .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Hur du arbetar med Event Hubs

Microsoft tillhandahåller en uppsättning med Java-komponenter som kan användas för att kommunicera med Event Hubs från en Storm-topologi. Du hittar den Java-arkivfil (JAR) som innehåller en HDInsight 3.6 kompatibel version av dessa komponenter på [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Även om komponenterna är skrivna i Java, kan du enkelt använda dem från en C#-topologi.

I det här exemplet används följande komponenter:

* __EventHubSpout__: läser data från Händelsehubbar.
* __EventHubBolt__: skriver data till Event Hubs.
* __EventHubSpoutConfig__: används för att konfigurera EventHubSpout.
* __EventHubBoltConfig__: används för att konfigurera EventHubBolt.

### <a name="example-spout-usage"></a>Exempel på användning kanal

SCP.NET tillhandahåller metoder för att lägga till en EventHubSpout topologin. De här metoderna gör det enklare att lägga till en kanal än att använda allmänna metoder för att lägga till en Java-komponent. I följande exempel visar hur du skapar en kanal med hjälp av den __SetEventHubSpout__ och **EventHubSpoutConfig** metoder som tillhandahålls av SCP.NET:

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

I föregående exempel skapas en ny kanal komponent med namnet __EventHubSpout__, och konfigurerar den att kommunicera med en händelsehubb. Parallellitet-tipset för komponenten har angetts till antalet partitioner i hubben. Den här inställningen Storm att skapa en instans av komponenten för varje partition.

### <a name="example-bolt-usage"></a>Exempel på bult användning

Använd den **JavaComponmentConstructor** metod för att skapa en instans av bulten. I följande exempel visar hur du skapar och konfigurerar en ny instans av den **EventHubBolt**:

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
> Det här exemplet används en Clojure-uttryck som skickas som en sträng istället för att använda **JavaComponentConstructor** att skapa en **EventHubBoltConfig**, som exemplet spout gjorde. Antingen metoden fungerar. Använd den metod som fungerar bäst för dig.

## <a name="download-the-completed-project"></a>Ladda ned det slutförda projektet

Du kan hämta en fullständig version av projektet har skapats i den här självstudien från [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Du behöver dock fortfarande att tillhandahålla konfigurationsinställningar genom att följa stegen i den här självstudien.

### <a name="prerequisites"></a>Förutsättningar

* En [Apache Storm på HDInsight-kluster av version 3.5 eller 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > Exemplet i det här dokumentet kräver Storm på HDInsight version 3.5 eller 3.6. Detta fungerar inte med äldre versioner av HDInsight, på grund av större ändringar för klassen namn. En version av det här exemplet fungerar med äldre kluster finns [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* En [Azure-händelsehubb](../../event-hubs/event-hubs-create.md).

* Den [Azure .NET SDK](http://azure.microsoft.com/downloads/).

* Den [HDInsight tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 eller senare på din utvecklingsmiljö. JDK hämtas från [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * Den **JAVA_HOME** miljövariabeln måste peka på katalogen som innehåller Java.
  * Den **%JAVA_HOME%/bin** katalogen måste vara i sökvägen.

## <a name="download-the-event-hubs-components"></a>Hämta Event Hubs-komponenter

Hämta Event Hubs-kanal och bultar komponenten från [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Skapa en katalog med namnet `eventhubspout`, och spara filen i katalogen.

## <a name="configure-event-hubs"></a>Konfiguration av Event Hubs

Händelsehubbar är datakällan för det här exemplet. Använd informationen i avsnittet ”Skapa en event hub” i [Kom igång med Event Hubs](../../event-hubs/event-hubs-create.md).

1. När du har skapat event hub, visa den **EventHub** inställningar i Azure-portalen och välj **principer för delad åtkomst**. Välj **+ Lägg till** att lägga till följande principer:

   | Namn | Behörigheter |
   | --- | --- |
   | författare |Skicka |
   | läsare |Lyssna |

    ![Skärmbild av resursen åtkomstfönstret principer](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Välj den **läsare** och **skrivaren** principer. Kopiera och spara värdet för primära nyckeln för båda principerna, eftersom dessa värden används senare.

## <a name="configure-the-eventhubwriter"></a>Konfigurera EventHubWriter

1. Om du inte redan har installerat den senaste versionen av HDInsight-verktyg för Visual Studio finns i [kommer igång med HDInsight tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Ladda ned lösningen från [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. I den **EventHubWriter** projektet öppnar den **App.config** fil. Använd informationen från den händelsehubb som du konfigurerade tidigare när du ska fylla i värdet för följande nycklar:

   | Nyckel | Värde |
   | --- | --- |
   | EventHubPolicyName |skrivare (om du använde ett annat namn för principen med *skicka* behörighet, Använd istället.) |
   | EventHubPolicyKey |Nyckel för principen skrivaren. |
   | EventHubNamespace |Det namnområde som innehåller din event hub. |
   | EventHubName |Din event hub-namn. |
   | EventHubPartitionCount |Antalet partitioner i event hub. |

4. Spara och Stäng den **App.config** fil.

## <a name="configure-the-eventhubreader"></a>Konfigurera EventHubReader

1. Öppna den **EventHubReader** projekt.

2. Öppna den **App.config** för den **EventHubReader**. Använd informationen från den händelsehubb som du konfigurerade tidigare när du ska fylla i värdet för följande nycklar:

   | Nyckel | Värde |
   | --- | --- |
   | EventHubPolicyName |läsare (om du använde ett annat namn för principen med *lyssna* behörighet, Använd istället.) |
   | EventHubPolicyKey |Nyckel för principen läsare. |
   | EventHubNamespace |Det namnområde som innehåller din event hub. |
   | EventHubName |Din event hub-namn. |
   | EventHubPartitionCount |Antalet partitioner i event hub. |

3. Spara och Stäng den **App.config** fil.

## <a name="deploy-the-topologies"></a>Distribuera topologierna

1. Från **Solution Explorer**, högerklicka på den **EventHubReader** projektet och välj **skicka till Storm på HDInsight**.

    ![Skärmbild av Solution Explorer med Skicka till Storm på HDInsight markerat](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. På den **skicka topologi** dialogrutan din **Storm-kluster**. Expandera **ytterligare konfigurationer**väljer **Java sökvägar**väljer **...** , och välj den katalog som innehåller JAR-filen som du hämtade tidigare. Klicka slutligen på **skicka**.

    ![Skärmbild av skicka topologi dialogrutan](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. När topologin har skickats, den **Storm-topologier Viewer** visas. Om du vill visa information om topologin, Välj den **EventHubReader** topologi i den vänstra rutan.

    ![Skärmbild av visningsprogrammet för Storm-topologier](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Från **Solution Explorer**, högerklicka på den **EventHubWriter** projektet och välj **skicka till Storm på HDInsight**.

5. På den **skicka topologi** dialogrutan din **Storm-kluster**. Expandera **ytterligare konfigurationer**väljer **Java sökvägar**väljer **...** , och välj den katalog som innehåller JAR-filen som du hämtade tidigare. Klicka slutligen på **skicka**.

6. När topologin har skickats, uppdatera listan med topologi i den **Storm-topologier Viewer** att kontrollera att båda topologierna körs i klustret.

7. I **Storm-topologier Viewer**väljer den **EventHubReader** topologi.

8. Öppna komponenten sammanfattning för bulten genom att dubbelklicka på den **LogBolt** komponenten i diagrammet.

9. I den **Executors** väljer du någon av länkarna på den **Port** kolumn. Då visas information som loggas av komponenten. Loggade informationen liknar följande text:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Stoppa topologierna

Om du vill stoppa topologierna, väljer du varje topologi i den **Storm-topologi Viewer**, klicka sedan på **Kill**.

![Skärmbild av Storm-topologi Viewer, med Kill knappen markerad](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Ta bort ditt kluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Java Event Hubs-kanal och bultar från en C#-topologi att arbeta med data i Azure Event Hubs. Mer information om hur du skapar C#-topologier finns följande:

* [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Programmeringsguide för SCP](apache-storm-scp-programming-guide.md)
* [Exempeltopologier för Storm på HDInsight](apache-storm-example-topology.md)
