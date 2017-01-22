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
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bccec1e4078c38e1cc9205a36d3a5df579df35b6
ms.openlocfilehash: d5ff397e947a7edc8310da59ff9fe8896829e35d


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introduktion till Apache Storm på HDInsight: analys i realtid för Hadoop
Apache Storm på HDInsight gör att du kan skapa distribuerade analyslösningar i realtid i Azure-miljön med [Apache Hadoop](http://hadoop.apache.org).

## <a name="what-is-apache-storm"></a>Vad är Apache Storm?
Apache Storm är ett distribuerat, feltolerant, öppet system för källkodsberäkning som gör det möjligt att bearbeta data i realtid med Hadoop. Storm-lösningar kan även ge garanterad bearbetning av data med möjlighet att på nytt spela upp data som inte bearbetades första gången.

## <a name="why-use-storm-on-hdinsight"></a>Varför använda Storm på HDInsight?
Apache Storm på HDInsight är ett hanterat kluster som är integrerat i Azure-miljön. Det ger följande viktiga fördelar:

* Utförs som en hanterad tjänst med ett SLA för 99,9 % drifttid
* Använd språket du föredrar: har stöd för Storm-komponenter som skrivits i **Java**, **C#**, och **Python**
  
  * Har stöd för olika programmeringsspråk: läs data med Java och sedan bearbeta den med hjälp av C#
    
    > [!NOTE]
    > Om du vill använda en C#-topologi med ett Linux-baserat kluster måste du uppdatera Microsoft.SCP.Net.SDK NuGet-paketet som används av projektet till version 0.10.0.6 eller högre. Paketversionen måste även matcha huvudversionen av Storm som är installerad på HDInsight. Storm på HDInsight versionerna 3.3 och 3.4 använder till exempel Storm version 0.10.x, medan HDInsight 3.5 använder Storm 1.0.x.
    > 
    > C#-topologier på Linux-baserade kluster måste använda .NET 4.5 och använda Mono för att köra på HDInsight-klustret. De flesta saker kommer att fungera, men du bör kontrollera dokumentet [Mono-kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/) för potentiella kompatibilitetsproblem.
    > 
  * Använd **Trident** Java-gränssnittet för att skapa Storm-topologier som stöder ”engångsbearbetning” av meddelanden, ”transaktionell" datastore-beständighet och en uppsättning vanliga stream analytics-åtgärder
* Innehåller inbyggda funktioner för att skala upp och skala ned: skala ett HDInsight-kluster utan att påverka Storm-topologier som körs
* Integrera med andra Azure-tjänster, inklusive händelsehubbar, Azure Virtual Network, SQL Database, Blob Storage och DocumentDB
  
  * Kombinera funktionerna i flera HDInsight-kluster med hjälp av det Azure Virtual Network: skapa analytiska pipelines som använder HDInsight, HBase eller Hadoop-kluster

En lista över företag som använder Apache Storm för sina lösningar för analys i realtid finns i [Företag som använder Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Information om hur du kommer igång med Storm finns i [Komma igång med Storm i HDInsight][gettingstarted].

### <a name="ease-of-provisioning"></a>Enkel etablering
Du kan etablera ett nytt Storm på HDInsight-kluster på bara några minuter. Ange klusternamn, storlek, administratörskonto och lagringskonto. Azure skapar klustret, inklusive exempeltopologier och en instrumentpanel för webbhantering.

> [!NOTE]
> Du kan också etablera Storm-kluster med hjälp av [Azure CLI](../xplat-cli-install.md) eller [Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Inom 15 minuter från det att du har skickat förfrågan har du ett nytt Storm-kluster redo för din första pipeline för realtidsanalys.

### <a name="ease-of-use"></a>Användarvänlighet
**För Linux-baserade Storm på HDInsight-kluster** kan du ansluta till klustret med SSH och använda `storm`-kommandot för att starta och hantera topologier. Du kan dessutom använda Ambari och övervaka Storm-tjänsten och Storm-gränssnittet för att övervaka och hantera topologier som körs.

Mer information om att arbeta med Linux-baserade Storm-kluster finns i [Kom igång med Apache Storm på Linux-baserade HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

**För Windows-baserade Storm på HDInsight-kluster** gör HDInsight Tools för Visual Studio att du kan skapa C#- och hybrid C#/Java-topologier och skicka dem till ditt Storm på HDInsight-kluster.  

![Skapa Storm-projekt](./media/hdinsight-storm-overview/createproject.png)

HDInsight Tools för Visual Studio innehåller också ett gränssnitt som gör att du kan övervaka och hantera Storm-topologier i ett kluster.

![Storm-hantering](./media/hdinsight-storm-overview/stormview.png)

Ett exempel för att skapa ett Storm-program med HDInsight Tools finns i [Utveckla C# Storm-topologier med HDInsight Tools för Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Mer information om HDInsight Tools för Visual Studio finns i [Komma igång med HDInsight Tools för Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

Varje Storm på HDInsight-kluster tillhandahåller även en webbaserad Storm-instrumentpanel som gör att du kan skicka, övervaka och hantera Storm-topologier som körs i klustret.

![Storm-instrumentpanelen](./media/hdinsight-storm-overview/dashboard.png)

Mer information om hur du använder Storm-instrumentpanelen finns i [Distribuera och hantera Apache Storm-topologier på HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Storm på HDInsight ger även enkel integrering med Azure Event Hubs via **Event Hub-kanalen**. Den senaste versionen av den här komponenten är tillgänglig på [https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs). Mer information om hur du använder den här komponenten finns i följande dokument.

* [Utveckla en C#-topologi som använder Azure Event Hubs](hdinsight-storm-develop-csharp-event-hub-topology.md)
* [Utveckla en Java-topologi som använder Azure Event Hubs](hdinsight-storm-develop-java-event-hub-topology.md)

### <a name="reliability"></a>Tillförlitlighet
Apache Storm garanterar alltid att varje inkommande meddelande  bearbetas fullständigt, även om dataanalysen är utspridd på hundratals noder.

**Nimbus-noden** ger liknande funktionalitet till Hadoop JobTracker och tilldelar aktiviteter till andra noder i klustret via **Zookeeper**. Zookeeper-noder samordnar för klustret och underlättar kommunikationen mellan Nimbus och **Övervakar**-processen på arbetsnoderna. Om en nod för bearbetning kraschar informeras Nimbus-noden och uppgiften och tillhörande data tilldelas till en annan nod.

Standardkonfigurationen för Apache Storm är att bara ha en Nimbus-nod. Storm på HDInsight kör två Nimbus-noder. Om den primära noden kraschar växlar HDInsight-klustret till den sekundära noden medan den primära noden återställs.

![Diagram över nimbus och zookeeper och övervakaren](./media/hdinsight-storm-overview/nimbus.png)

### <a name="scale"></a>Skala
Du kan ange antalet noder i klustret när du skapar det men du kan ibland behöva öka eller minska klustret så att det matchar arbetsbelastningen. Du kan ändra antalet noder i alla HDInsight-kluster, även under bearbetning av data.

> [!NOTE]
> Om du vill utnyttja de nya noder som har lagts till via skalning behöver du ombalansera de topologier som startats innan klusterstorleken  ökades.
> 
> 

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
  > I många fall läses data från en kö, till exempel Kafka, Azure Service Bus-köer eller Event Hubs. Kön garanterar att data sparas om det finns ett avbrott.
  > 
  > 
* **Bult**: förbrukar **strömmar**, utför bearbetning på **tupplar** och  kan skapa **strömmar**. Bultar ansvarar även för att skriva data till externa lagringsenheter, till exempel en kö, HDInsight, HBase, en blobb eller ett annat datalager.
* **Apache Thrift**: ett ramverk för programvara för utveckling av skalbara tjänster på flera språk. Det gör att du kan skapa tjänster som fungerar mellan C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk och andra språk.
  
  * **Nimbus** är en Thrift-tjänst och en **topologi** är en Thrift-definition, så det är möjligt att utveckla topologier med ett antal olika programmeringsspråk.

Mer information om Storm-komponenter finns i [självstudiekursen om Storm][apachetutorial] på apache.org.

## <a name="what-programming-languages-can-i-use"></a>Vilka programmeringsspråk kan jag använda?
Storm på HDInsight-klustret har stöd för C# och Java och Python.

### <a name="c35"></a>C&#35;
HDInsight Tools för Visual Studio gör att .NET-utvecklare kan utforma och implementera en topologi i C#. Du kan också skapa hybridtopologier som använder Java- och C#-komponenter.

Mer information finns i [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

### <a name="java"></a>Java
De flesta Java-exempel du ser är vanlig Java eller Trident. Trident är en abstraktion på hög nivå som gör det enklare att göra sådant som kopplingar, aggregeringar, gruppering och filtrering. Trident fungerar dock på batchar av tupplar, medan en rå Java-lösning bearbetar en ström med en tuppel i taget.

Mer information om Trident finns i [Trident-självstudierna](https://storm.apache.org/documentation/Trident-tutorial.html) på apache.org.

Exempel på Java- och Trident-topologier finns i [listan över exempel på Storm-topologier](hdinsight-storm-example-topology.md) eller de storm-startexempel som finns på ditt HDInsight-kluster.

Storm-startexemplen finns i katalogen ** /usr/hdp/current/storm-client/contrib/storm-starter** på Linux-baserade kluster och **%storm_home%\contrib\storm-starter** på Windows-baserade kluster.

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
Batchbearbetning kan utföras på flera olika sätt. Med en grundläggande Storm Java-topologi kan du använda enkla räknare för att skapa batchar av X antal tupplar innan de sänds. Du kan även använda en intern tidsmekanism som kallas ”tidstuppel” för att generera en batch var X sekund.

Ett exempel på hur du använder tidstupplar finns i [Analysera sensordata med Storm och HBase på HDInsight](hdinsight-storm-sensor-data-analysis.md).

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



<!--HONumber=Jan17_HO1-->


