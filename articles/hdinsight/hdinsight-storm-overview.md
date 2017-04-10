---
title: "Introduktion till Apache Storm på HDInsight | Microsoft Docs"
description: "Få en introduktion till Apache Storm på HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introduktion till Apache Storm på HDInsight: analys i realtid för Hadoop

Apache Storm på HDInsight gör att du kan skapa distribuerade analyslösningar i realtid i Azure.

Apache Storm är ett distribuerat, feltolerant, öppet system för källkodsberäkning som gör det möjligt att bearbeta data i realtid med Hadoop. Storm-lösningar kan även ge garanterad bearbetning av data med möjlighet att på nytt spela upp data som inte bearbetades första gången.

## <a name="how-does-storm-work"></a>Så här fungerar Storm

Apache Storm kör **topologier** i stället för de MapReduce-jobb som du kanske känner till från HDInsight eller Hadoop. Topologier består av flera komponenter som är ordnade i
en riktad acyklisk graf (DAG). Följande diagram illustrerar hur data som flödar mellan komponenterna i en grundläggande ordräkningstopologi

![Exempel på hur komponenter är ordnade i en Storm-topologi](./media/hdinsight-storm-overview/wordcount-topology.png)

* __Kanal__-komponenter överför data till en topologi. De sänder en eller flera strömmar till topologin

* __Bult__-komponenter förbrukar strömmar som sänts ut från kanaler eller andra bultar. Bultar kan eventuellt sända nya strömmar till topologin. Bultar ansvarar för att skriva data till beständig lagring, till exempel HDFS eller HBase.

## <a name="why-use-storm-on-hdinsight"></a>Varför använda Storm på HDInsight

Apache Storm i HDInsight har följande två viktiga fördelar:

* Fungerar som en hanterad tjänst med ett SLA med en garanterad drifttid på 99,9 %.

* Enkel anpassning genom skriptkörning mot klustret när det skapas eller i efterhand. Mer information finns i [Customize HDInsight clusters using script action](hdinsight-hadoop-customize-cluster-linux.md) (Anpassa HDInsight-kluster med skriptåtgärder).

* Använd det språk du önskar: Storm-komponenter kan skrivas på olika språk, t.ex. **Java**, **C#** och **Python**.

    * Visual Studio-integration med HDInsight för utveckling, hantering och övervakning av C#-topologier. Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

    * Stöder Java-gränssnittet **Trident**. Med det här gränssnittet kan du skapa Storm-topologier som stöder engångsbearbetning av meddelanden, transaktionell datastore-beständighet och en uppsättning vanliga Stream Analytics-åtgärder.

* Skala enkelt upp och ned klustret: Lägg till eller ta bort arbetarnoder utan att påverka Storm-topologier som körs.

* Integrera med följande Azure-tjänster:

    * Händelsehubbar

    * Virtual Network

    * SQL Database

    * Azure Storage

    * DocumentDB.

    * Kombinera säkert funktionerna i flera HDInsight-kluster med hjälp av Azure Virtual Network: skapa analytiska pipelines som använder HDInsight, HBase eller Hadoop-kluster.

En lista över företag som använder Apache Storm för sina lösningar för analys i realtid finns i [Företag som använder Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Information om hur du kommer igång med Storm finns i [Komma igång med Storm i HDInsight][gettingstarted].

## <a name="ease-of-creation"></a>Enkelt att skapa

Du kan etablera ett nytt Storm på HDInsight-kluster på bara några minuter. Mer information om hur du skapar ett Storm-kluster finns i [Kom igång med Storm på HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Användbarhet

* __Secure Shell-anslutningar__: Du kan komma åt huvudnoderna i ditt HDInsight-kluster via Internet med SSH. Med SSH kan du köra kommandon direkt i klustret.

  Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* __Webbanslutningar__: HDInsight-kluster ger åtkomst till Ambari-webbgränssnittet. Med Ambari-webbgränssnittet kan du enkelt övervaka, konfigurera och hantera tjänster för klustret. Storm för HDInsight tillhandahåller även Storm-användargränssnittet, där du kan övervaka och hantera Storm-topologier som körs från din webbläsare.

  Mer information finns i [Manage HDInsight using the Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Hantera HDInsight med hjälp av Ambari-webbanvändargränssnittet) och [Monitor and manage using the Storm UI](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui) (Övervaka och hantera med hjälp av Storm-användargränssnittet).

* __Azure PowerShell och CLI__: Både Azure PowerShell och Azure CLI tillhandahåller kommandoradsverktyg som du kan använda från ditt klientsystem för att arbeta med HDInsight och andra Azure-tjänster.

* __Visual Studio-integrering__: Data Lake Tools för Visual Studio innehåller projektmallar för att skapa Storm-topologier med SCP.NET-ramverket. Data Lake Tools innehåller också verktyg för att distribuera, övervaka och hantera lösningar med Storm i HDInsight.

  Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

## <a name="integration-with-other-azure-services"></a>Integration med andra Azure-tjänster

* __Azure Data Lake Store__: Ett exempel på hur du använder Data Lake Store med Storm finns i [Use Azure Data Lake Store with Apache Storm on HDInsight](hdinsight-storm-write-data-lake-store.md) (Använda Azure Data Lake Store med Apache Storm i HDInsight).

* __Azure Event Hubs__: Ett exempel på hur du använder Azure Event Hubs med Storm finns i följande dokument:

    * [Develop a Java-based topology for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Utveckla en Java-baserad topologi för Storm i HDInsight)

    * [Process events from Azure Event Hubs with Storm on HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md) (Bearbeta händelser från Azure Event Hubs med Storm i HDInsight (C#))

* Exempel på hur du arbetar med __SQL DB__, __DocumentDB__, __EventHub__ och __HBase__ finns tillgängliga som mallar i Azure Data Lake Tools för Visual Studio. Mer information finns i [Develop a C# topology for Storm on HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla en C#-topologi för Storm i HDInsight).

## <a name="reliability"></a>Tillförlitlighet

Apache Storm garanterar alltid att varje inkommande meddelande bearbetas fullständigt, även om dataanalysen är utspridd på hundratals noder.

**Nimbus-noden** ger liknande funktionalitet till Hadoop JobTracker och tilldelar aktiviteter till andra noder i klustret via **Zookeeper**. Zookeeper-noder samordnar för klustret och underlättar kommunikationen mellan Nimbus och **Övervakar**-processen på arbetsnoderna. Om en nod för bearbetning kraschar informeras Nimbus-noden och uppgiften och tillhörande data tilldelas till en annan nod.

Standardkonfigurationen för Apache Storm är att bara ha en Nimbus-nod. Storm på HDInsight kör två Nimbus-noder. Om den primära noden kraschar växlar HDInsight-klustret till den sekundära noden medan den primära noden återställs.

![Diagram över nimbus och zookeeper och övervakaren](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Skala

Du kan ange antalet noder i klustret när du skapar det men du kan ibland behöva öka eller minska klustret så att det matchar arbetsbelastningen. Du kan ändra antalet noder i alla HDInsight-kluster, även under bearbetning av data.

> [!NOTE]
> Om du vill utnyttja de nya noder som har lagts till via skalning behöver du ombalansera de topologier som startats innan klusterstorleken ökades.

## <a name="support"></a>Support

Storm på HDInsight levereras med fullständig support på företagsnivå, dygnet runt. Storm på HDInsight har också ett SLA för 99,9 %. Det innebär att vi garanterar att klustret har extern anslutning minst 99,9 % av tiden.

Mer information finns på [Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Vanliga användarsituationer

Nedan följer några vanliga scenarier där du kan använda Apache storm på HDInsight. 

* Sakernas Internet (IoT)
* Upptäckt av bedrägerier
* Sociala analyser
* Extrahera, transformera, läsa in (ETL)
* Nätverksövervakning
* Söka
* Mobile engagement

Information om verkliga scenarier finns i dokumentet [Hur företag använder Storm](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Utveckling

Med __Data Lake Tools för Visual Studio__ kan .NET-utvecklare utforma och implementera en topologi i __C#__. Du kan också skapa hybridtopologier som använder Java- och C#-komponenter.

Mer information finns i [Develop C# topologies for Storm on HDInsight using Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla en C#-topologi för Storm i HDInsight).

Du kan också skapa __Java__-lösningar med valfri IDE. Mer information finns i [Develop Java topologies for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Utveckla Java-topologier för Storm i HDInsight).

Du kan också utveckla Storm-komponenter med Python. Mer information finns i [Develop Storm topologies using Python on HDInsight](hdinsight-storm-develop-python-topology.md) (Utveckla Storm-topologier med Python i HDInsight).

## <a name="common-development-patterns"></a>Vanliga utvecklingsmönster

### <a name="guaranteed-message-processing"></a>Garanterad meddelandebehandling

Storm kan ge olika nivåer av garanterad meddelandebehandling. Ett grundläggande Storm-program kan till exempel garantera bearbetning minst en gång och Trident kan garantera bearbetning exakt en gång.

Mer information finns i [Garantier om databearbetning](https://storm.apache.org/about/guarantees-data-processing.html) på apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Ett vanligt mönster är att läsa en inkommande tuppel, sända noll eller flera tupplar och sedan kvittera den inkommande tuppeln omedelbart i slutet av metoden. Storm innehåller ett [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html)-gränssnitt för att automatisera detta mönster.

### <a name="joins"></a>Kopplingar

Hur dataströmmar kopplas varierar mellan olika program. Du kan till exempel koppla samman varje tuppel från flera strömmar  till en ny ström eller så kan du koppla bara batchar av tupplar för ett specifikt fönster. Hur du än gör kan du koppla samman genom att använda  [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), vilket är ett sätt att definiera hur tupplar skickas till bultar.

I Java-exemplet nedan används fieldsGrouping för att dirigera tupplar som kommer från komponenterna ”1”, ”2” och ”3” till bulten **MyJoiner**.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Batchbearbetning

Storm innehåller en intern tidsinställning som kallas en "tidstuppel", som kan användas för att skapa en batch varje X sekunder.

Ett exempel på hur du använder en tidstuppel från en C#-komponent finns i [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Om du använder Trident baseras det på att bearbeta batchar av tupplar.

### <a name="caching"></a>Cachelagring

Minnesintern cachelagring används ofta som en mekanism för snabbare bearbetning eftersom den bevarar ofta använda tillgångar i minnet. Eftersom en topologi distribueras över flera noder, och har flera processer i varje nod, bör du överväga att använda [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). `fieldsGrouping` garanterar att tupplar som innehåller de fält som används för cache-sökning alltid dirigeras till samma process. Denna grupperingsfunktion förhindrar duplicering av cacheposter mellan processer.

### <a name="streaming-top-n"></a>Strömma högsta N

Om topologin är beroende av att ett värde för "högsta N" beräknas bör du beräkna värdet för högsta N parallellt. Koppla sedan utdata från dessa beräkningar till ett globalt värde. Det kan du göra med hjälp av [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) genom att dirigera efter fält för parallell bearbetning och sedan dirigera till en bult som globalt avgör det högsta N-värdet.

Ett exempel på att beräkna ett ”högsta N”-värde finns i [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Loggning

Storm använder Apache Log4j för att logga information. Stora mängder data loggas som standard och det kan vara svårt att gå igenom informationen. Du kan ta med en konfigurationsfil för loggning som en del av Storm-topologin för att styra loggningsbeteendet.

En exempeltopologi som visar hur du konfigurerar loggning finns i ett exempel på [Java-baserad WordCount](hdinsight-storm-develop-java-topology.md) för Storm på HDInsight.

## <a name="next-steps"></a>Nästa steg

Läs mer om lösningarna för realtidsanalys med Apache Storm i HDInsight:

* [Komma igång med Storm i HDInsight][gettingstarted]
* [Exempeltopologier för Storm på HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

