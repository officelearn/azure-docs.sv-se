---
title: Vad är Apache Spark – Azure HDInsight
description: Den här artikeln innehåller en introduktion till Spark på HDInsight och olika scenarier där du kan använda Spark-kluster i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc,seoapr2020
ms.topic: overview
ms.date: 04/17/2020
ms.openlocfilehash: e933d48a60a01bdbf00205a389778b9a3befeede
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188233"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Vad är Apache Spark i Azure HDInsight

Apache Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att öka prestanda i program för stordataanalys. Apache Spark i Azure HDInsight är Microsofts implementering av Apache Spark i molnet. HDInsight gör det enklare att skapa och konfigurera ett Spark-kluster i Azure. Apache Spark-kluster i HDInsight är kompatibla med Azure Storage och Azure Data Lake Storage. Så du kan använda HDInsight Spark-kluster för att bearbeta dina data som lagras i Azure. Information om komponenter och versionshantering finns i [Apache Hadoop-komponenter och versioner i Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: ett enhetligt ramverk](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Vad är Apache Spark?

Spark tillhandahåller primitiver för klusterbearbetning i minnet. Ett Spark-jobb kan läsa in och cachelagra data i minnet samt köra frågor på dessa data upprepade gånger. Minnesintern beräkning är mycket snabbare än diskbaserade program, till exempel Hadoop, som delar data med hjälp av ett Hadoop-distribuerat filsystem (HDFS, Hadoop Distributed File System). Spark är integrerat i programmeringsspråket Scala. På så sätt kan du bearbeta distribuerade datauppsättningar på samma sätt som lokala samlingar. Det finns inget behov av att strukturera det hela i mappnings- och reduceringsåtgärder.

![Traditionell MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Med Spark-kluster HDInsight får du tillgång till en helt hanterad Spark-tjänst. Fördelarna med att skapa ett Spark-kluster i HDInsight visas här.

| Funktion | Beskrivning |
| --- | --- |
| Enkelt att skapa |Du kan skapa ett nytt Spark-kluster i HDInsight på bara några minuter med hjälp av Azure Portal, Azure PowerShell eller HDInsight .NET SDK. Se [Kom igång med Apache Spark kluster i HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Användbarhet |Spark-kluster i HDInsight innehåller Jupyter och Apache Zeppelin-anteckningsböcker. Du kan de här anteckningsböckerna för interaktiv databehandling och visualisering. Se [använda Apache Zeppelin-anteckningsböcker med Apache Spark](apache-spark-zeppelin-notebook.md) och [läsa in data och köra frågor på ett Apache Spark-kluster](apache-spark-load-data-run-query.md).|
| REST API:er |HDInsight Spark innehåller [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), en REST-API-baserad Spark-jobbserver för fjärrsändning och -övervakning av jobb. Se [Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster](apache-spark-livy-rest-interface.md).|
| Stöd för Azure Data Lake Storage | Apache Spark-kluster i HDInsight kan använda Azure Data Lake Storage som både primär och extra lagring. Mer information om Data Lake Storage finns i [Översikt över Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrering med Azure-tjänster |Spark-kluster i HDInsight levereras med en anslutningsapp för Azure Event Hubs. Du kan bygga strömmande program med hjälp av Event Hubs. Inklusive Apache Kafka, som redan är tillgänglig som en del av Spark. |
| Stöd för ML Server | Stöd för ML Server i HDInsight tillhandahålls som klustertypen **ML-tjänster**. Du kan konfigurera ett ML-tjänstkluster för att köra distribuerade R-beräkningar i de hastigheter Spark-klustret har kapacitet för. Mer information finns i [Vad är ml-tjänster i Azure HDInsight](../r-server/r-server-overview.md). |
| Integrering med tredje parts IDEs | HDInsight tillhandahåller flera IDE-plugin-program som är användbara när du vill skapa och skicka program till ett HDInsight Spark-kluster. Mer information finns i [använda Azure Toolkit for INTELLIJ idé](apache-spark-intellij-tool-plugin.md), [använda Spark & Hive-verktyg för VSCode](../hdinsight-for-vscode.md)och [använda Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Samtidiga frågor |Spark-kluster i HDInsight har stöd för samtidiga frågor. Denna kapacitet gör att flera frågor från en användare eller flera frågor från olika användare och program kan dela samma klusterresurser. |
| Cachelagring i SSD:er |Du kan välja att cachelagrade data i minnet eller i SSD:er anslutna till klusternoderna. Cachelagring i minnet ger bästa frågeprestanda, men kan vara dyrt. Cachelagring i SSD:er är ett bra alternativ för att förbättra frågeprestanda utan att behöva skapa ett kluster i den storlek som krävs för att få plats med hela datauppsättningen i minnet. Se [förbättra prestanda för Apache Spark arbets belastningar med Azure HDInsight IO-cache](apache-spark-improve-performance-iocache.md). |
| Integrering med BI-verktyg |Spark-kluster för HDInsight tillhandahåller anslutningsappar för BI-verktyg som Power BI för dataanalys. |
| Förinstallerade Anaconda-bibliotek |Spark-kluster i HDInsight kommer med förinstallerade Anaconda-bibliotek. [Anaconda](https://docs.continuum.io/anaconda/) tillhandahåller nära 200 bibliotek för maskin inlärning, data analys, visualisering och så vidare. |
| Anpassnings förmåga | Med HDInsight kan du ändra antalet klusternoder dynamiskt med funktionen för autoskalning. Se [automatisk skalning av Azure HDInsight-kluster](../hdinsight-autoscale-clusters.md). Dessutom kan du ta bort Apache Spark-kluster utan någon dataförlust eftersom alla data lagras i Azure Storage eller Data Lake Storage. |
| SLA |Spark-kluster på HDInsight levereras med dygnet runt-support veckans alla dagar och ett serviceavtal för 99,9 % drifttid. |

Apache Spark-kluster i HDInsight innehåller följande komponenter som är tillgängliga i klustren som standard.

* [Spark Core](https://spark.apache.org/docs/latest/). Omfattar Spark Core, Spark SQL, Spark-API:er för strömning, GraphX och MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Apache Zeppelin Notebook](http://zeppelin-project.org/)

HDInsight Spark-kluster en [ODBC-drivrutin](https://go.microsoft.com/fwlink/?LinkId=616229) för anslutning från BI-verktyg som Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Spark-klusterarkitektur

![HDInsight Sparks arkitektur](./media/apache-spark-overview/hdi-spark-architecture.png)

Det är enkelt att förstå komponenterna i Spark genom att förstå hur Spark körs på HDInsight-kluster.

Spark-program körs som oberoende uppsättningar av processer i ett kluster. Koordineras av SparkContext-objektet i huvud programmet (kallas driv rutins program).

SparkContext kan ansluta till flera olika typer av kluster hanterare, vilket ger resurser mellan olika program. Dessa kluster hanterare omfattar Apache Mesos, [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)eller Spark Cluster Manager. I HDInsight körs Spark ovanpå YARN-klusterhanteraren. När du är ansluten inhämtar Spark exekverare på klustrets arbetarnoder, vilka är processer som kör beräkningar och lagrar data för ditt program. Därefter skickar den din programkod (definieras av JAR- eller Python-filer som överförts till SparkContext) till exekverarna. Slutligen skickar SparkContext uppgifter till de exekverare som ska köras.

SparkContext kör användarens main-funktion och utför de olika parallella åtgärderna på arbetsnoderna. Drivrutinen samlar sedan in åtgärdernas resultat. Arbetsnoderna läser data från och skriver data till HDFS-filsystemet (Hadoop Distributed File System). Arbetsnoderna cachelagrar också omvandlade data i minnet som RDD-datauppsättningar (Resilient Distributed Dataset).

SparkContext ansluter till Spark-hanteraren och ansvarar för att konvertera ett program till ett riktat diagram (DAG) för enskilda uppgifter. Aktiviteter som körs i en utförar-process på arbetsnoderna. Varje program får sin egen utförar-process. Som håller sig under hela programmets varaktighet och kör aktiviteter i flera trådar.

## <a name="spark-in-hdinsight-use-cases"></a>Spark i HDInsight – användningsfall

Spark-kluster i HDInsight möjliggör följande huvudsakliga scenarier:

### <a name="interactive-data-analysis-and-bi"></a>Interaktiv dataanalys och BI

Apache Spark i HDInsight lagrar data i Azure Storage eller Azure Data Lake Storage. Företags experter och viktiga besluts fattare kan analysera och bygga rapporter över dessa data. Och använda Microsoft Power BI för att bygga interaktiva rapporter från analyserade data. Analytiker kan utgå ifrån ostrukturerade/halvstrukturerade data i klusterlagring, definiera ett schema för data med anteckningsböcker och sedan skapa datamodeller med Microsoft Power BI. Spark-kluster i HDInsight har också stöd för ett antal BI-verktyg från tredje part. Till exempel Tableau, vilket gör det enklare för dataanalytiker, affärs experter och viktiga besluts fattare.

* [Självstudier: Visualisera Spark-data med Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark-maskininlärning

Apache Spark levereras med [MLlib](https://spark.apache.org/mllib/). MLlib är ett maskin inlärnings bibliotek som byggts ovanpå Spark och som du kan använda från ett Spark-kluster i HDInsight. Spark-kluster i HDInsight innehåller också Anaconda, en python-distribution med olika typer av paket för Machine Learning. Och med inbyggt stöd för Jupyter-och Zeppelin-anteckningsböcker har du en miljö för att skapa maskin inlärnings program.

* [Självstudie: förutsäga Bygg temperaturer med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)  
* [Självstudie: förutsäga resultatet av livsmedels undersökningen](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark-strömning och dataanalys i realtid

Spark-kluster i HDInsight innehåller omfattande stöd för att skapa lösningar för realtidsanalys. Spark har redan kopplingar för att mata in data från många källor som Kafka, FLUME, Twitter, ZeroMQ eller TCP Sockets. Spark i HDInsight lägger till förstklassigt stöd för att mata in data från Azure Event Hubs. Event Hubs är den vanligast använda kötjänsten på Azure. Att ha ett fullständigt stöd för Event Hubs gör Spark-kluster i HDInsight till en idealisk plattform för att bygga pipeline för real tids analys.

* [Översikt över Apache Spark strömning](apache-spark-streaming-overview.md)
* [Översikt över Apache Spark strukturerad strömning](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Du kan använda följande artiklar om du vill veta mer om Apache Spark i HDInsight:

* [Snabb start: skapa ett Apache Spark kluster i HDInsight och köra interaktiv fråga med Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Självstudie: köra ett Apache Spark jobb med Jupyter](./apache-spark-load-data-run-query.md)
* [Självstudie: analysera data med BI-verktyg](./apache-spark-use-bi-tools.md)
* [Självstudie: Machine Learning med hjälp av Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
* [Självstudie: skapa ett Scala maven-program med hjälp av IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Efterföljande moment

I den här översikten får du viss grundläggande förståelse för Apache Spark i Azure HDInsight. Lär dig hur du skapar ett HDInsight Spark-kluster och kör några Spark SQL-frågor:

* [Skapa ett Apache Spark-kluster i HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
