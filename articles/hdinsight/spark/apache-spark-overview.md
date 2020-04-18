---
title: Vad är Apache Spark – Azure HDInsight
description: Den här artikeln innehåller en introduktion till Spark på HDInsight och olika scenarier där du kan använda Spark-kluster i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 04/17/2020
ms.openlocfilehash: 6926fb8aa22f57ee6068866f732258703b6d78e9
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641773"
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
| Enkelt att skapa |Du kan skapa ett nytt Spark-kluster i HDInsight på bara några minuter med hjälp av Azure Portal, Azure PowerShell eller HDInsight .NET SDK. Se [Kom igång med Apache Spark-kluster i HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Användbarhet |Spark-kluster i HDInsight innehåller Jupyter och Apache Zeppelin-anteckningsböcker. Du kan de här anteckningsböckerna för interaktiv databehandling och visualisering. Se [Använda Apache Zeppelin-anteckningsböcker med Apache Spark](apache-spark-zeppelin-notebook.md) och Läs in data och kör frågor på ett Apache [Spark-kluster](apache-spark-load-data-run-query.md).|
| REST API:er |HDInsight Spark innehåller [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), en REST-API-baserad Spark-jobbserver för fjärrsändning och -övervakning av jobb. Se [Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster](apache-spark-livy-rest-interface.md).|
| Stöd för Azure Data Lake Storage | Apache Spark-kluster i HDInsight kan använda Azure Data Lake Storage som både primär och extra lagring. Mer information om Data Lake Storage finns i [Översikt över Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrering med Azure-tjänster |Spark-kluster i HDInsight levereras med en anslutningsapp för Azure Event Hubs. Du kan skapa strömmande program med hjälp av eventhubbar. Inklusive Apache Kafka, som redan finns som en del av Spark. |
| Stöd för ML Server | Stöd för ML Server i HDInsight tillhandahålls som klustertypen **ML-tjänster**. Du kan konfigurera ett ML-tjänstkluster för att köra distribuerade R-beräkningar i de hastigheter Spark-klustret har kapacitet för. Mer information finns [i Vad är ML-tjänster i Azure HDInsight](../r-server/r-server-overview.md). |
| Integrering med tredje parts IDEs | HDInsight tillhandahåller flera IDE-plugin-program som är användbara när du vill skapa och skicka program till ett HDInsight Spark-kluster. Mer information finns i [Använda Azure Toolkit för IntelliJ IDEA](apache-spark-intellij-tool-plugin.md), Använd Spark & [Hive Tools för VSCode](../hdinsight-for-vscode.md)och [Använd Azure Toolkit för Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Samtidiga frågor |Spark-kluster i HDInsight har stöd för samtidiga frågor. Denna kapacitet gör att flera frågor från en användare eller flera frågor från olika användare och program kan dela samma klusterresurser. |
| Cachelagring i SSD:er |Du kan välja att cachelagrade data i minnet eller i SSD:er anslutna till klusternoderna. Cachelagring i minnet ger bästa frågeprestanda, men kan vara dyrt. Cachelagring i SSD:er är ett bra alternativ för att förbättra frågeprestanda utan att behöva skapa ett kluster i den storlek som krävs för att få plats med hela datauppsättningen i minnet. Se [Förbättra prestanda för Apache Spark-arbetsbelastningar med Azure HDInsight IO-cache](apache-spark-improve-performance-iocache.md). |
| Integrering med BI-verktyg |Spark-kluster för HDInsight tillhandahåller anslutningsappar för BI-verktyg som Power BI för dataanalys. |
| Förinstallerade Anaconda-bibliotek |Spark-kluster i HDInsight kommer med förinstallerade Anaconda-bibliotek. [Anaconda](https://docs.continuum.io/anaconda/) tillhandahåller närmare 200 bibliotek för maskininlärning, dataanalys, visualisering och så vidare. |
| Anpassningsförmåga | HDInsight kan du ändra antalet klusternoder dynamiskt med funktionen Automatisk skalning. Se [Skala Azure HDInsight-kluster automatiskt](../hdinsight-autoscale-clusters.md). Dessutom kan du ta bort Apache Spark-kluster utan någon dataförlust eftersom alla data lagras i Azure Storage eller Data Lake Storage. |
| SLA |Spark-kluster på HDInsight levereras med dygnet runt-support veckans alla dagar och ett serviceavtal för 99,9 % drifttid. |

Apache Spark-kluster i HDInsight innehåller följande komponenter som är tillgängliga i klustren som standard.

* [Spark Core](https://spark.apache.org/docs/latest/). Omfattar Spark Core, Spark SQL, Spark-API:er för strömning, GraphX och MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Apache Zeppelin Notebook](http://zeppelin-project.org/)

HDInsight Spark kluster en [ODBC-drivrutin](https://go.microsoft.com/fwlink/?LinkId=616229) för anslutning från BI-verktyg som Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Spark-klusterarkitektur

![HDInsight Sparks arkitektur](./media/apache-spark-overview/hdi-spark-architecture.png)

Det är lätt att förstå komponenterna i Spark genom att förstå hur Spark körs på HDInsight-kluster.

Spark-program körs som oberoende uppsättningar processer i ett kluster. Koordinerad av SparkContext-objektet i huvudprogrammet (kallas drivrutinsprogrammet).

SparkContext kan ansluta till flera typer av klusterhanterare, som ger resurser över program. Dessa klusterhanterare inkluderar Apache Mesos, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)eller Spark-klusterhanteraren. I HDInsight körs Spark ovanpå YARN-klusterhanteraren. När du är ansluten inhämtar Spark exekverare på klustrets arbetarnoder, vilka är processer som kör beräkningar och lagrar data för ditt program. Därefter skickar den din programkod (definieras av JAR- eller Python-filer som överförts till SparkContext) till exekverarna. Slutligen skickar SparkContext uppgifter till de exekverare som ska köras.

SparkContext kör användarens main-funktion och utför de olika parallella åtgärderna på arbetsnoderna. Drivrutinen samlar sedan in åtgärdernas resultat. Arbetsnoderna läser data från och skriver data till HDFS-filsystemet (Hadoop Distributed File System). Arbetsnoderna cachelagrar också omvandlade data i minnet som RDD-datauppsättningar (Resilient Distributed Dataset).

SparkContext ansluter till Spark-hanteraren och ansvarar för att konvertera ett program till ett riktat diagram (DAG) för enskilda uppgifter. Uppgifter som körs i en körningsprocess på arbetsnoderna. Varje program får sina egna körprocesser. Som stannar uppe under hela programmet och kör aktiviteter i flera trådar.

## <a name="spark-in-hdinsight-use-cases"></a>Spark i HDInsight – användningsfall

Spark-kluster i HDInsight möjliggör följande huvudsakliga scenarier:

### <a name="interactive-data-analysis-and-bi"></a>Interaktiv dataanalys och BI

Apache Spark i HDInsight lagrar data i Azure Storage eller Azure Data Lake Storage. Affärsexperter och viktiga beslutsfattare kan analysera och bygga rapporter över dessa data. Och använd Microsoft Power BI för att skapa interaktiva rapporter från de analyserade data. Analytiker kan utgå ifrån ostrukturerade/halvstrukturerade data i klusterlagring, definiera ett schema för data med anteckningsböcker och sedan skapa datamodeller med Microsoft Power BI. Spark-kluster i HDInsight stöder också ett antal BI-verktyg från tredje part. Till exempel Tableau, vilket gör det lättare för dataanalytiker, affärsexperter och viktiga beslutsfattare.

* [Självstudier: Visualisera Spark-data med Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark-maskininlärning

Apache Spark levereras med [MLlib](https://spark.apache.org/mllib/). MLlib är ett maskininlärningsbibliotek som bygger på Spark som du kan använda från ett Spark-kluster i HDInsight. Spark-kluster i HDInsight innehåller även Anaconda, en Python-distribution med olika typer av paket för maskininlärning. Och med inbyggt stöd för jupyter och Zeppelin-bärbara datorer har du en miljö för att skapa maskininlärningsprogram.

* [Självstudiekurs: Förutsäg byggtemperaturer med hjälp av VVS-data](apache-spark-ipython-notebook-machine-learning.md)  
* [Handledning: Förutsäga resultat livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark-strömning och dataanalys i realtid

Spark-kluster i HDInsight innehåller omfattande stöd för att skapa lösningar för realtidsanalys. Spark har redan kontakter för att få data från många källor som Kafka, Flume, Twitter, ZeroMQ eller TCP-uttag. Spark i HDInsight lägger till förstklassigt stöd för intag av data från Azure Event Hubs. Event Hubs är den vanligast använda kötjänsten på Azure. Att ha fullständigt stöd för Event Hubs gör Spark-kluster i HDInsight till en idealisk plattform för att skapa analyspipeline i realtid.

* [Översikt över Apache Spark Streaming](apache-spark-streaming-overview.md)
* [Översikt över Apache Spark Structured Streaming](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Du kan använda följande artiklar om du vill veta mer om Apache Spark i HDInsight:

* [Snabbstart: Skapa ett Apache Spark-kluster i HDInsight och kör interaktiv fråga med Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Handledning: Kör en Apache Spark jobb med Jupyter](./apache-spark-load-data-run-query.md)
* [Självstudiekurs: Analysera data med hjälp av BI-verktyg](./apache-spark-use-bi-tools.md)
* [Självstudiekurs: Maskininlärning med Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
* [Självstudiekurs: Skapa ett Scala Maven-program med IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Efterföljande moment

I den här översikten får du viss grundläggande förståelse för Apache Spark i Azure HDInsight. Lär dig hur du skapar ett HDInsight Spark-kluster och kör några Spark SQL-frågor:

* [Skapa ett Apache Spark-kluster i HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
