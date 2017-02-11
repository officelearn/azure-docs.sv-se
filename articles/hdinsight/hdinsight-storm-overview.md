---
title: "Introduktion till Apache Storm på HDInsight | Microsoft Docs"
description: "Få en introduktion till Apache Storm och lär dig hur du kan använda Storm på HDInsight för att skapa lösningar för dataanalys i realtid i molnet."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: 426c7f5860a4be45b4c8e7ff4dd4b4406715ea34


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introduktion till Apache Storm på HDInsight: analys i realtid för Hadoop

Apache Storm på HDInsight gör att du kan skapa distribuerade analyslösningar i realtid i Azure-miljön med [Apache Hadoop](http://hadoop.apache.org).

## <a name="what-is-apache-storm"></a>Vad är Apache Storm?

Apache Storm är ett distribuerat, feltolerant, öppet system för källkodsberäkning som gör det möjligt att bearbeta data i realtid med Hadoop. Storm-lösningar kan även ge garanterad bearbetning av data med möjlighet att på nytt spela upp data som inte bearbetades första gången.

## <a name="why-use-storm-on-hdinsight"></a>Varför använda Storm på HDInsight?

Apache Storm på HDInsight är ett hanterat kluster som är integrerat i Azure-miljön. Storm och andra Hadoop-komponenter i HDInsight baseras på HDP-plattformen (Hortonworks Data Platform), medan operativsystemet för klustret är Ubuntu (en Linux-distribution). Detta ger en plattform som är väldigt kompatibel med populära verktyg och tjänster i Hadoops ekosystem.

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i avsnittet om [utfasningen av HDInsight i Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

Apache Storm i HDInsight har följande två viktiga fördelar:

* Fungerar som en hanterad tjänst med ett SLA med en garanterad drifttid på 99,9 %.

* Enkel anpassning genom skriptkörning mot klustret när det skapas eller i efterhand. Mer information finns i [Customize HDInsight clusters using script action](hdinsight-hadoop-customize-cluster-linux.md) (Anpassa HDInsight-kluster med skriptåtgärder).

* Använd önskat språk: stöder Storm-komponenter skrivna i **Java**, **C#** och **Python**.
  
  * Visual Studio-integration med HDInsight för utveckling, hantering och övervakning av C#-topologier. Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

  * Stöder Java-gränssnittet **Trident**. Med det här gränssnittet kan du skapa Storm-topologier som stöder ”engångsbearbetning” av meddelanden, ”transaktionell” datastore-beständighet och en uppsättning vanliga Stream Analytics-åtgärder.

* Skala enkelt upp och ned klustret: Lägg till eller ta bort arbetarnoder utan att påverka Storm-topologier som körs.

* Integrera med andra Azure-tjänster, inklusive Event Hubs, Azure Virtual Network, SQL Database, Blob Storage och DocumentDB.
  
  * Kombinera säkert funktionerna i flera HDInsight-kluster med hjälp av Azure Virtual Network: skapa analytiska pipelines som använder HDInsight, HBase eller Hadoop-kluster.

En lista över företag som använder Apache Storm för sina lösningar för analys i realtid finns i [Företag som använder Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Information om hur du kommer igång med Storm finns i [Komma igång med Storm i HDInsight][gettingstarted].

### <a name="ease-of-provisioning"></a>Enkel etablering

Du kan etablera ett nytt Storm på HDInsight-kluster på bara några minuter. Ange klusternamn, storlek, administratörskonto och lagringskonto. Azure skapar klustret, inklusive exempeltopologier och en instrumentpanel för webbhantering.

> [!NOTE]
> Du kan också etablera Storm-kluster med hjälp av [Azure CLI](../xplat-cli-install.md) eller [Azure PowerShell](/powershell/azureps-cmdlets-docs).

Inom 15 minuter från det att du har skickat förfrågan har du ett nytt Storm-kluster redo för din första pipeline för realtidsanalys.

### <a name="ease-of-use"></a>Användarvänlighet

* __Secure Shell-anslutningar__: Du kan komma åt huvudnoderna i ditt HDInsight-kluster via Internet med SSH. På så sätt kan du köra kommandon direkt i klustret.

  Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* __Webbanslutningar__: HDInsight-kluster ger åtkomst till Ambari-webbgränssnittet. På så sätt kan du enkelt övervaka, konfigurera och hantera tjänster för klustret. Storm för HDInsight tillhandahåller även Storm-användargränssnittet, där du kan övervaka och hantera Storm-topologier som körs från din webbläsare.

  Mer information finns i [Manage HDInsight using the Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Hantera HDInsight med hjälp av Ambari-webbanvändargränssnittet) och [Monitor and manage using the Storm UI](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-using-the-storm-ui) (Övervaka och hantera med hjälp av Storm-användargränssnittet).

* __Azure PowerShell och CLI__: Både Azure PowerShell och Azure CLI tillhandahåller kommandoradsverktyg som du kan använda från ditt klientsystem för att arbeta med HDInsight och andra Azure-tjänster.

* __Visual Studio-integration__: Data Lake Tools för Visual Studio (tidigare kallat HDInsight Tools för Visual Studio) innehåller projektmallar för att skapa Storm-topologier i C#, samt verktyg för Storm för HDInsight. Du kan skapa, distribuera, övervaka och hantera dina C#-topologier från Visual Studio.

  Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

* __Integration med andra Azure-tjänster__

  * För __Java__-utveckling utnyttjar Microsoft befintliga Storm-komponenter för att integrera med andra Azure-tjänster då det är möjligt. I vissa fall kan en tjänstspecifik komponent eller lösning krävas.

    * __Azure Data Lake Store__: Java-baserade topologier kan komma åt Data Lake Store med hjälp av Storm-HDFS-bulten med ett URI-schema av typen `adl://`. Ett exempel på hur du använder Storm-HDFS-bulten finns i [Use Azure Data Lake Store with Apache Storm on HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-write-data-lake-store) (Använda Azure Data Lake Store med Apache Storm i HDInsight).

    * __Azure Blob Storage__ (när det används som lagringsplats för HDInsight): Java-baserade topologier kan komma åt Azure Blob-lagring som är associerat med klustret genom att använda Storm-HDFS-bulten med ett URI-schema av typen `wasb://`.

    * __Azure Event Hubs__: Kan nås med EventHubSpout- och EventHubBolt-komponenter som tillhandahålls av Microsoft. Dessa komponenter är skrivna i Java och tillhandahålls som en fristående JAR-fil.

    Mer information om hur du utvecklar Java-lösningar finns i [Develop a Java-based topology for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Utveckla en Java-baserad topologi för Storm i HDInsight).

  * För __C#__-utveckling kan du ofta använda .NET SDK för Azure-tjänsten. I vissa fall kan SDK vara beroende av ramverk som inte är tillgängliga i Linux (värdoperativsystemet för HDInsight 3.4 och senare.) I detta fall kan du använda Java-komponenter från din C#-lösning.

    * Exempel på hur du arbetar med __SQL DB__, __DocumentDB__, __EventHub__ och __HBase__ finns tillgängliga som mallar i Azure Data Lake Tools för Visual Studio. Mer information finns i [Develop a C# topology for Storm on HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla en C#-topologi för Storm i HDInsight).

    * __Azure Event Hubs__: Ett exempel på hur du använder Java-komponenter från en C#-lösning finns i [Process events from Azure Event Hubs with Storm on HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md) (Bearbeta händelser från Azure Event Hubs med Storm i HDInsight (C#)).

    Mer information om hur du utvecklar C#-lösningar finns i [Develop a C# topology for Storm on HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Utveckla en C#-topologi för Storm i HDInsight).

### <a name="reliability"></a>Tillförlitlighet

Apache Storm garanterar alltid att varje inkommande meddelande  bearbetas fullständigt, även om dataanalysen är utspridd på hundratals noder.

**Nimbus-noden** ger liknande funktionalitet till Hadoop JobTracker och tilldelar aktiviteter till andra noder i klustret via **Zookeeper**. Zookeeper-noder samordnar för klustret och underlättar kommunikationen mellan Nimbus och **Övervakar**-processen på arbetsnoderna. Om en nod för bearbetning kraschar informeras Nimbus-noden och uppgiften och tillhörande data tilldelas till en annan nod.

Standardkonfigurationen för Apache Storm är att bara ha en Nimbus-nod. Storm på HDInsight kör två Nimbus-noder. Om den primära noden kraschar växlar HDInsight-klustret till den sekundära noden medan den primära noden återställs.

![Diagram över nimbus och zookeeper och övervakaren](./media/hdinsight-storm-overview/nimbus.png)

### <a name="scale"></a>Skala

Du kan ange antalet noder i klustret när du skapar det men du kan ibland behöva öka eller minska klustret så att det matchar arbetsbelastningen. Du kan ändra antalet noder i alla HDInsight-kluster, även under bearbetning av data.

> [!NOTE]
> Om du vill utnyttja de nya noder som har lagts till via skalning behöver du ombalansera de topologier som startats innan klusterstorleken  ökades.

### <a name="support"></a>Support

Storm på HDInsight levereras med fullständig support på företagsnivå, dygnet runt. Storm på HDInsight har också ett SLA för 99,9 %. Det innebär att vi garanterar att klustret ska ha extern anslutning minst 99,9 % av tiden.

## <a name="common-use-cases-for-real-time-analytics"></a>Vanliga användning för realtidsanalys

Nedan följer några vanliga scenarier där du kan använda Apache storm på HDInsight. Information om verkliga scenarier finns i [Hur företag använder Storm](https://storm.apache.org/documentation/Powered-By.html).

* Sakernas Internet (IoT)
* Upptäckt av bedrägerier
* Sociala analyser
* Extrahera, transformera, läsa in (ETL)
* Nätverksövervakning
* Söka
* Mobile engagement

## <a name="how-is-data-in-hdinsight-storm-processed"></a>Hur bearbetas data i HDInsight Storm?

Apache Storm kör **topologier** i stället för de MapReduce-jobb som du kanske känner till från HDInsight eller Hadoop. Ett Storm på HDInsight-kluster innehåller två typer av noder: huvudnoder som kör **Nimbus** och arbetsnoder som kör **Övervakare**.

* **Nimbus**: liknar JobTracker i Hadoop och ansvarar för att distribuera kod i hela klustret, att tilldela uppgifter till virtuella datorer och att övervaka så att det inte uppstår fel. HDInsight tillhandahåller två Nimbus-noder så det finns inte en enskild felpunkt för Storm på HDInsight
* **Övervakare**: övervakaren för varje arbetsnod ansvarar för att starta och stoppa **arbetsprocesser** på noden.
* **Arbetsprocess**: kör en delmängd av en **topologi**. En topologi som körs distribueras till många arbetsprocesser i hela klustret.
* **Topologi**: definierar ett diagram över beräkning som bearbetar **strömmar** av data. Till skillnad från MapReduce-jobb körs topologier tills du stoppar dem.
* **Ström**: en obunden samling av **tupplar**. Strömmar produceras av **kanaler** och **bultar** och de förbrukas av **bultar**.
* **Tuppel**: en namngiven lista över dynamiskt skrivna värden.
* **Kanal**: använder data från en datakälla och genererar en eller flera **strömmar**.
  
  > [!NOTE]
  > I många fall läses data från en kö, till exempel Kafka eller Azure Event-hubbar. Kön garanterar att data sparas om det finns ett avbrott.

* **Bult**: förbrukar **strömmar**, utför bearbetning på **tupplar** och  kan skapa **strömmar**. Bultar ansvarar även för att skriva data till externa lagringsenheter, till exempel en kö, HDInsight, HBase, en blobb eller ett annat datalager.
* **Apache Thrift**: ett ramverk för programvara för utveckling av skalbara tjänster på flera språk. Det gör att du kan skapa tjänster som fungerar mellan C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk och andra språk.
  
  * **Nimbus** är en Thrift-tjänst och en **topologi** är en Thrift-definition, så det är möjligt att utveckla topologier med ett antal olika programmeringsspråk.

Mer information om Storm-komponenter finns i [självstudiekursen om Storm][apachetutorial] på apache.org.

## <a name="what-programming-languages-can-i-use"></a>Vilka programmeringsspråk kan jag använda?

Storm på HDInsight-klustret har stöd för C# och Java och Python.

### <a name="c35"></a>C&#35;

Med Data Lake Tools för Visual Studio kan .NET-utvecklare utforma och implementera en topologi i C#. Du kan också skapa hybridtopologier som använder Java- och C#-komponenter.

Mer information finns i [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

### <a name="java"></a>Java

De flesta Java-exempel du ser är vanlig Java eller Trident. Trident är en abstraktion på hög nivå som gör det enklare att göra sådant som kopplingar, aggregeringar, gruppering och filtrering. Trident fungerar dock på batchar av tupplar, medan en rå Java-lösning bearbetar en ström med en tuppel i taget.

Mer information om Trident finns i [Trident-självstudierna](https://storm.apache.org/documentation/Trident-tutorial.html) på apache.org.

Exempel på Java- och Trident-topologier finns i [listan över exempel på Storm-topologier](hdinsight-storm-example-topology.md) eller de storm-startexempel som finns på ditt HDInsight-kluster.

Storm-starter-exemplen finns i katalogen ** /usr/hdp/current/storm-client/contrib/storm-starter** i ditt HDInsight-kluster.

## <a name="what-are-some-common-development-patterns"></a>Vilka är några vanliga utvecklingsmönster?

### <a name="guaranteed-message-processing"></a>Garanterad meddelandebehandling

Storm kan ge olika nivåer av garanterad meddelandebehandling. Ett grundläggande Storm-program kan till exempel garantera bearbetning minst en gång och Trident kan garantera bearbetning exakt en gång.

Mer information finns i [Garantier om databearbetning](https://storm.apache.org/about/guarantees-data-processing.html) på apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Ett mycket vanligt mönstret är att läsa en inkommande tuppel, sända noll eller flera tupplar och sedan kvittera den inkommande tuppeln omedelbart i slutet av metoden och Storm tillhandahåller [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) -gränssnittet för att automatisera detta mönster.

### <a name="joins"></a>Kopplingar

Hur två dataströmmar kopplas samman varierar mellan program. Du kan till exempel koppla samman varje tuppel från flera strömmar  till en ny ström eller så kan du koppla bara batchar av tupplar för ett specifikt fönster. Hur du än gör kan du koppla samman genom att använda  [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), vilket är ett sätt att definiera hur tupplar skickas till bultar.

I Java-exemplet nedan används fieldsGrouping för att dirigera tupplar som kommer från komponenterna ”1”, ”2” och ”3” till bulten **MyJoiner**.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Batchbearbetning

Batchbearbetning kan utföras på flera olika sätt. Med en C#- eller Java-topologi kan du använda enkla räknare för att skapa batchar av X antal tupplar innan de sänds. Du kan även använda en intern tidsmekanism kallad ”tidstuppel” för att generera en batch var X sekund.

Ett exempel på hur du använder en tidstuppel från en C#-komponent finns i [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Om du använder Trident baseras det på att bearbeta batchar av tupplar.

### <a name="caching"></a>Cachelagring

Minnesintern cachelagring används ofta som en mekanism för snabbare bearbetning eftersom den bevarar ofta använda tillgångar i minnet. Eftersom en topologi distribueras över flera noder och flera processer i varje nod bör du använda [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) så att tupplar som innehåller de fält som används för cache-sökning alltid dirigeras till samma process. Detta förhindrar duplicering av cacheposter mellan processer.

### <a name="streaming-top-n"></a>Strömma högsta N

När topologin beror på att beräkna ett värde för ”högsta N”, t.ex. de 5 främsta trenderna på Twitter, bör du beräkna det högsta N-värdet parallellt och sedan sammanfoga resultatet av dessa beräkningar i ett globalt värde. Det kan du göra med hjälp av [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) för att dirigera till de parallella bultarna (som partitionerar data efter fältvärde) och sedan dirigera till en bult som globalt avgör det högsta N-värdet.

Ett exempel på detta finns i [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="what-type-of-logging-does-storm-use"></a>Vilken typ av loggning använder Storm?

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



<!--HONumber=Feb17_HO2-->


