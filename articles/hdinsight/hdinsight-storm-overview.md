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
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 73e524bf9e7d51cee841a3c5599ab33aa31cbc37
ms.lasthandoff: 04/06/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introduktion till Apache Storm på HDInsight: analys i realtid för Hadoop

Du kan skapa distribuerade lösningar för realtidsanalys med hjälp av Apache Storm i Azure HDInsight.

Storm är ett distribuerat, feltolerant beräkningssystem med öppen källkod. Du kan använda Storm för att bearbeta data i realtid med Hadoop. Storm-lösningar kan även ge garanterad bearbetning av data med möjlighet att på nytt spela upp data som inte bearbetades första gången.

## <a name="how-does-storm-work"></a>Hur fungerar Storm?

Storm kör topologier i stället för de MapReduce-jobb som du kanske känner till från HDInsight eller Hadoop. Topologier består av flera komponenter som är ordnade i
en riktad acyklisk graf (DAG). Följande diagram illustrerar hur data flödar mellan komponenterna i en grundläggande ordräkningstopologi:

![Exempel på hur komponenter är ordnade i en Storm-topologi](./media/hdinsight-storm-overview/wordcount-topology.png)

* Kanalkomponenter överför data till en topologi. De sänder en eller flera strömmar till topologin.

* Bultkomponenter förbrukar strömmar som sänts ut från kanaler eller andra bultar. Bultar kan eventuellt sända nya strömmar till topologin. Bultar ansvarar för att skriva data till beständig lagring, till exempel HDFS eller HBase.

## <a name="why-use-storm-on-hdinsight"></a>Varför använda Storm på HDInsight?

Storm i HDInsight har följande två viktiga fördelar:

* Fungerar som en hanterad tjänst med ett SLA med 99,9 procent drifttid.

* Stöder enkel anpassning genom skriptkörning mot ett kluster när det skapas eller i efterhand. Mer information finns i [Customize HDInsight clusters using script action](hdinsight-hadoop-customize-cluster-linux.md) (Anpassa HDInsight-kluster med skriptåtgärder).

* Använder olika språk. Du kan skriva Storm-komponenter på valfritt språk, till exempel Java, C# och Python.

    * Integrerar Visual Studio med HDInsight för utveckling, hantering och övervakning av C#-topologier. Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

    * Stöder Java-gränssnittet Trident. Du kan skapa Storm-topologier som stöder engångsbearbetning av meddelanden, transaktionell datalagringsbeständighet och en uppsättning vanliga Stream Analytics-åtgärder.

*  Skalar enkelt upp och ned kluster. Du kan lägga till eller ta bort arbetsnoder utan att påverka Storm-topologier som körs.

* Integrera med följande Azure-tjänster:

    * Azure Event Hubs

    * Azure Virtual Network

    * Azure SQL Database

    * Azure Storage

    * Azure DocumentDB

* Kombinera säkert funktionerna i flera HDInsight-kluster med hjälp av Virtual Network. Du kan skapa analytiska pipelines med HDInsight-, HBase- eller Hadoop-kluster.

En lista över företag som använder Storm för sina realtidsanalyslösningar finns i [Företag som använder Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Information om hur du kommer igång med Storm finns i [Komma igång med Storm i HDInsight][gettingstarted].

## <a name="ease-of-creation"></a>Enkelt att skapa

Du kan etablera ett nytt Storm-kluster i HDInsight på några minuter. Mer information om hur du skapar ett Storm-kluster finns i [Kom igång med Storm på HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Användbarhet

* __Secure Shell-anslutningar (SSH)__: Du kan komma åt huvudnoderna i ditt HDInsight-kluster via Internet med SSH. Du kan köra kommandon direkt i klustret med hjälp av SSH.

  Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* __Webbanslutningar__: HDInsight-kluster ger åtkomst till Ambari-webbgränssnittet. Du kan enkelt övervaka, konfigurera och hantera tjänster i klustret med hjälp av Ambari-webbanvändargränssnittet. Storm i HDInsight tillhandahåller även Storm-användargränssnittet. Du kan övervaka och hantera Storm-topologier som körs från din webbläsare med hjälp av Storm-användargränssnittet.

  Mer information finns i [Manage HDInsight using the Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Hantera HDInsight med hjälp av Ambari-webbanvändargränssnittet) och [Monitor and manage using the Storm UI](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui) (Övervaka och hantera med hjälp av Storm-användargränssnittet).

* __Azure PowerShell och Azure CLI__: Både PowerShell och CLI tillhandahåller kommandoradsverktyg som du kan använda från ditt klientsystem för att arbeta med HDInsight och andra Azure-tjänster.

* __Visual Studio-integrering__: Azure Data Lake Tools för Visual Studio innehåller projektmallar för att skapa C#-baserade Storm-topologier med SCP.NET-ramverket. Data Lake Tools innehåller också verktyg för att distribuera, övervaka och hantera lösningar med Storm i HDInsight.

  Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

## <a name="integration-with-other-azure-services"></a>Integration med andra Azure-tjänster

* __Azure Data Lake Store__: Ett exempel på hur du använder Data Lake Store med Storm finns i [Use Azure Data Lake Store with Apache Storm on HDInsight](hdinsight-storm-write-data-lake-store.md) (Använda Azure Data Lake Store med Apache Storm i HDInsight).

* __Event Hubs__: Ett exempel på hur du använder Event Hubs med Storm finns i följande dokument:

    * [Develop a Java-based topology for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Utveckla en Java-baserad topologi för Storm i HDInsight)

    * [Process events from Azure Event Hubs with Storm on HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md) (Bearbeta händelser från Azure Event Hubs med Storm i HDInsight (C#))

* __SQL Database__, __DocumentDB__, __Event Hubs__ och __HBase__: Mallexempel ingår i Data Lake Tools för Visual Studio. Mer information finns i [Develop a C# topology for Storm on HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla en C#-topologi för Storm i HDInsight).

## <a name="reliability"></a>Tillförlitlighet

Storm ser till att varje inkommande meddelande alltid bearbetas helt, även om dataanalysen är utspridd över hundratals noder.

Nimbus-noden tillhandahåller funktioner liknande dem i Hadoop JobTracker och tilldelar uppgifter till andra noder i ett kluster genom Zookeeper. Zookeeper-noder samordnar kluster och underlättar kommunikationen mellan Nimbus och övervakarprocessen på arbetsnoderna. Om en nod för bearbetning kraschar informeras Nimbus-noden och uppgiften och tillhörande data tilldelas till en annan nod.

Standardkonfigurationen för Storm är att bara ha en Nimbus-nod. Storm på HDInsight kör två Nimbus-noder. Om den primära noden kraschar växlar HDInsight-klustret till den sekundära noden medan den primära noden återställs. Följande diagram illustrerar konfigurationen av uppgiftsflödet för Storm i HDInsight:

![Diagram över nimbus och zookeeper och övervakaren](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Skala

Du kan ange antalet noder i klustret när du skapar det men du kan ibland behöva öka eller minska klustret så att det matchar arbetsbelastningen. Du kan ändra antalet noder i alla HDInsight-kluster, även under bearbetningen av data.

> [!NOTE]
> Om du vill utnyttja de nya noder som har lagts till via skalning behöver du ombalansera de topologier som startats innan klusterstorleken ökades.

## <a name="support"></a>Support

Storm i HDInsight levereras med fullständig, fortlöpande support på företagsnivå. Storm i HDInsight har även ett SLA med 99,9 % drifttid. Det innebär att vi garanterar att ett kluster har en extern anslutning minst 99,9 procent av tiden.

Mer information finns på [Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Vanliga användarsituationer

Nedan följer några vanliga scenarier där du kan använda Storm i HDInsight. 

* Sakernas Internet (IoT)
* Upptäckt av bedrägerier
* Sociala analyser
* Extrahering, transformering och inläsning (ETL)
* Nätverksövervakning
* Söka
* Mobile engagement

Information om verkliga scenarier finns i dokumentet [Hur företag använder Storm](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Utveckling

.NET-utvecklare kan utforma och implementera topologier i C# med hjälp av Data Lake Tools för Visual Studio. Du kan också skapa hybridtopologier som använder Java- och C#-komponenter.

Mer information finns i [Develop C# topologies for Storm on HDInsight using Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla en C#-topologi för Storm i HDInsight).

Du kan också skapa Java-lösningar med hjälp av valfri IDE. Mer information finns i [Develop Java topologies for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Utveckla Java-topologier för Storm i HDInsight).

Du kan också utveckla Storm-komponenter med Python. Mer information finns i [Develop Storm topologies using Python on HDInsight](hdinsight-storm-develop-python-topology.md) (Utveckla Storm-topologier med Python i HDInsight).

## <a name="common-development-patterns"></a>Vanliga utvecklingsmönster

### <a name="guaranteed-message-processing"></a>Garanterad meddelandebehandling

Storm kan ge olika nivåer av garanterad meddelandebehandling. Ett grundläggande Storm-program kan till exempel garantera bearbetning minst en gång och Trident kan garantera bearbetning exakt en gång.

Mer information finns i [Garantier om databearbetning](https://storm.apache.org/about/guarantees-data-processing.html) på apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Ett vanligt mönster är att läsa en inkommande tuppel, sända noll eller flera tupplar och sedan kvittera den inkommande tuppeln omedelbart i slutet av metoden. Storm innehåller ett [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html)-gränssnitt för att automatisera detta mönster.

### <a name="joins"></a>Kopplingar

Hur dataströmmar kopplas varierar mellan olika program. Du kan till exempel koppla samman varje tuppel från flera strömmar till en ny ström eller så kan du koppla bara batchar av tupplar för ett specifikt fönster. Hur du än gör kan du koppla samman genom att använda  [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), vilket är ett sätt att definiera hur tupplar skickas till bultar.

I Java-exemplet nedan används fieldsGrouping för att dirigera tupplar som kommer från komponenterna ”1”, ”2” och ”3” till bulten MyJoiner:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Batchar

Storm innehåller en intern tidsinställning kallad en ”tidstuppel”, som kan användas för att skapa en batch var X:e sekund.

Ett exempel på hur du använder en tidstuppel från en C#-komponent finns i [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Trident baseras på bearbetningen av batchar med tupplar.

### <a name="caches"></a>Caches

Minnesintern cachelagring används ofta som en mekanism för snabbare bearbetning eftersom den bevarar ofta använda tillgångar i minnet. Eftersom en topologi distribueras över flera noder, och har flera processer i varje nod, bör du överväga att använda [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). Använd `fieldsGrouping` för att se till att tupplar som innehåller fälten som används för cachesökning alltid dirigeras till samma process. Denna grupperingsfunktion förhindrar duplicering av cacheposter mellan processer.

### <a name="stream-top-n"></a>Dataström för ”högsta N”

Om topologin är beroende av att ett värde för ”högsta N” beräknas, bör du beräkna värdet för högsta N parallellt. Koppla sedan utdata från dessa beräkningar till ett globalt värde. Det kan du göra med hjälp av [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) genom att dirigera efter fält för parallell bearbetning och sedan dirigera till en bult som globalt avgör det högsta N-värdet.

Ett exempel på en beräkning av ett ”högsta N”-värde finns i [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Loggning

Storm använder Apache Log4j för att logga information. Stora mängder data loggas som standard och det kan vara svårt att gå igenom informationen. Du kan ta med en konfigurationsfil för loggning som en del av Storm-topologin för att styra loggningsbeteendet.

En exempeltopologi som visar hur du konfigurerar loggning finns i ett exempel på [Java-baserad WordCount](hdinsight-storm-develop-java-topology.md) för Storm på HDInsight.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om lösningar för realtidsanalys med Storm i HDInsight:

* [Komma igång med Storm i HDInsight][gettingstarted]
* [Exempeltopologier för Storm på HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

