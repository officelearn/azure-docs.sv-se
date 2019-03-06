---
title: Vad är Apache Spark – Azure HDInsight
description: Den här artikeln innehåller en introduktion till Spark på HDInsight och olika scenarier där du kan använda Spark-kluster i HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 01/28/2019
ms.author: hrasheed
ms.openlocfilehash: 264faaf0168d1604668e3358b1d99bc169d7e2f0
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985180"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Vad är Apache Spark i Azure HDInsight

Apache Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att öka prestanda i program för stordataanalys. Apache Spark i Azure HDInsight är Microsofts implementering av Apache Spark i molnet. HDInsight gör det enklare att skapa och konfigurera ett Spark-kluster i Azure. Apache Spark-kluster i HDInsight är kompatibla med Azure Storage och Azure Data Lake Storage. Så du kan använda HDInsight Spark-kluster för att bearbeta dina data som lagras i Azure. Information om komponenter och versionshantering finns i [Apache Hadoop-komponenter och versioner i Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: ett enhetligt ramverk](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Vad är Apache Spark?

Spark tillhandahåller primitiver för klusterbearbetning i minnet. Ett Spark-jobb kan läsa in och cachelagra data i minnet samt köra frågor på dessa data upprepade gånger. Minnesintern beräkning är mycket snabbare än diskbaserade program, till exempel Hadoop, som delar data med hjälp av ett Hadoop-distribuerat filsystem (HDFS, Hadoop Distributed File System). Spark är integrerat i programmeringsspråket Scala. På så sätt kan du bearbeta distribuerade datauppsättningar på samma sätt som lokala samlingar. Det finns inget behov av att strukturera det hela i mappnings- och reduceringsåtgärder.

![Traditionella MapReduce jämfört med Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

Med Spark-kluster HDInsight får du tillgång till en helt hanterad Spark-tjänst. Fördelarna med att skapa ett Spark-kluster i HDInsight visas här.

| Funktion | Beskrivning |
| --- | --- |
| Enkelt att skapa |Du kan skapa ett nytt Spark-kluster i HDInsight på bara några minuter med hjälp av Azure Portal, Azure PowerShell eller HDInsight .NET SDK. Se [Komma igång med Apache Spark-kluster i HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Användbarhet |Spark-kluster i HDInsight innehåller Jupyter och Apache Zeppelin-anteckningsböcker. Du kan de här anteckningsböckerna för interaktiv databehandling och visualisering.|
| REST API:er |HDInsight Spark innehåller [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), en REST-API-baserad Spark-jobbserver för fjärrsändning och -övervakning av jobb. Se [Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster](apache-spark-livy-rest-interface.md).|
| Stöd för Azure Data Lake Storage | Apache Spark-kluster i HDInsight kan använda Azure Data Lake Storage som både primär och extra lagring. Mer information om Data Lake Storage finns i [Översikt över Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrering med Azure-tjänster |Spark-kluster i HDInsight levereras med en anslutningsapp för Azure Event Hubs. Med Event Hubs kan du skapa strömningsprogram utöver [Apache Kafka](https://kafka.apache.org/), som redan ingår i Spark. |
| Stöd för ML Server | Stöd för ML Server i HDInsight tillhandahålls som klustertypen **ML-tjänster**. Du kan konfigurera ett ML-tjänstkluster för att köra distribuerade R-beräkningar i de hastigheter Spark-klustret har kapacitet för. Mer information finns i [Komma igång med ML Server i HDInsight](../r-server/r-server-get-started.md). |
| Integrering med tredje parts IDEs | HDInsight tillhandahåller flera IDE-plugin-program som är användbara när du vill skapa och skicka program till ett HDInsight Spark-kluster. Mer information finns i [Använda Azure Toolkit for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md), [Använda HDInsight for VSCode](../hdinsight-for-vscode.md) samt [Använda Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Samtidiga frågor |Spark-kluster i HDInsight har stöd för samtidiga frågor. Denna kapacitet gör att flera frågor från en användare eller flera frågor från olika användare och program kan dela samma klusterresurser. |
| Cachelagring i SSD:er |Du kan välja att cachelagrade data i minnet eller i SSD:er anslutna till klusternoderna. Cachelagring i minnet ger bästa frågeprestanda, men kan vara dyrt. Cachelagring i SSD:er är ett bra alternativ för att förbättra frågeprestanda utan att behöva skapa ett kluster i den storlek som krävs för att få plats med hela datauppsättningen i minnet. |
| Integrering med BI-verktyg |Spark-kluster för HDInsight tillhandahåller anslutningsappar för BI-verktyg som [Power BI](https://www.powerbi.com/) för dataanalys. |
| Förinstallerade Anaconda-bibliotek |Spark-kluster i HDInsight kommer med förinstallerade Anaconda-bibliotek. [Anaconda](https://docs.continuum.io/anaconda/) innehåller närmare 200 bibliotek för Machine Learning, dataanalys, visualisering med mera. |
| Skalbarhet | Med HDInsight kan du ändra antalet klusternoder. Dessutom kan du ta bort Apache Spark-kluster utan någon dataförlust eftersom alla data lagras i Azure Storage eller Data Lake Storage. |
| SLA |Spark-kluster på HDInsight levereras med dygnet runt-support veckans alla dagar och ett serviceavtal för 99,9 % drifttid. |

Apache Spark-kluster i HDInsight innehåller följande komponenter som är tillgängliga i klustren som standard.

* [Spark Core](https://spark.apache.org/docs/latest/). Omfattar Spark Core, Spark SQL, Spark-API:er för strömning, GraphX och MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Apache Zeppelin Notebook](http://zeppelin-project.org/)

Spark-kluster i HDInsight tillhandahåller även en [ODBC-drivrutin](https://go.microsoft.com/fwlink/?LinkId=616229) för anslutning till Spark-kluster i HDInsight från BI-verktyg som Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Spark-klusterarkitektur

![HDInsight Sparks arkitektur](./media/apache-spark-overview/spark-architecture.png)

Det är enkelt att förstå komponenterna i Spark om man förstår hur Spark körs på HDInsight-kluster.

Spark-program körs som oberoende processuppsättningar i ett kluster, och koordineras av SparkContext-objektet i ditt huvudprogram (kallas drivrutinsprogram).

SparkContext kan ansluta till flera olika typer av klusterhanterare, vilket allokerar resurser till alla program. Bland dessa klusterhanterare finns [Apache Mesos](https://mesos.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) och Spark-klusterhanteraren. I HDInsight körs Spark ovanpå YARN-klusterhanteraren. När du är ansluten inhämtar Spark exekverare på klustrets arbetarnoder, vilka är processer som kör beräkningar och lagrar data för ditt program. Därefter skickar den din programkod (definieras av JAR- eller Python-filer som överförts till SparkContext) till exekverarna. Slutligen skickar SparkContext uppgifter till de exekverare som ska köras.

SparkContext kör användarens main-funktion och utför de olika parallella åtgärderna på arbetsnoderna. Drivrutinen samlar sedan in åtgärdernas resultat. Arbetsnoderna läser data från och skriver data till HDFS-filsystemet (Hadoop Distributed File System). Arbetsnoderna cachelagrar också omvandlade data i minnet som RDD-datauppsättningar (Resilient Distributed Dataset).

SparkContext ansluter till Spark-hanteraren och ansvarar för omvandling av en applikation till en riktad graf (DAG) över enskilda uppgifter som körs i en körningsprocess på arbetsnoderna. Varje applikation får en egen körningsprocess som förblir aktiv under applikationens livslängd och kör uppgifter i flera trådar.

## <a name="spark-in-hdinsight-use-cases"></a>Spark i HDInsight – användningsfall

Spark-kluster i HDInsight möjliggör följande huvudsakliga scenarier:

- Interaktiv dataanalys och BI

    Apache Spark i HDInsight lagrar data i Azure Storage eller Azure Data Lake Storage. Affärsexperter och beslutsfattare kan analysera och skapa rapporter över dessa data och använda Microsoft Power BI till att skapa interaktiva rapporter utifrån analyserade data. Analytiker kan utgå ifrån ostrukturerade/halvstrukturerade data i klusterlagring, definiera ett schema för data med anteckningsböcker och sedan skapa datamodeller med Microsoft Power BI. Spark-kluster i HDInsight har också stöd för ett antal BI-verktyg från tredje part, bland annat Tableau. Det gör det enklare för dataanalytiker, affärsexperter och beslutsfattare.

    [Självstudie: Visualisera Spark-data med Power BI](apache-spark-use-bi-tools.md)
- Spark-maskininlärning

    Med Apache Spark medföljer Machine Learning-biblioteket [MLlib](https://spark.apache.org/mllib/) som är byggt på Spark som du kan använda från ett Spark-kluster i HDInsight. Spark-kluster i HDInsight innehåller Anaconda, en Python-distribution med en rad olika paket för Machine Learning. Ihop med det inbyggda stödet för Jupyter- och Zeppelin-anteckningsböcker ger det en miljö att skapa Machine Learning-program i.

    [Självstudier: Förutsäga temperaturer i en byggnad med hjälp av HVAC-data](apache-spark-ipython-notebook-machine-learning.md)  
    [Självstudier: Förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)    
- Spark-strömning och dataanalys i realtid

    Spark-kluster i HDInsight innehåller omfattande stöd för att skapa lösningar för realtidsanalys. Spark har anslutningsappar för att föra in data från ett flertal källor som Kafka, Flume, Twitter, ZeroMQ och TCP-socket. Med Spark i HDInsight får du dessutom förstklassig stöd för att föra in data från Azure Event Hubs. Event Hubs är den vanligast använda kötjänsten på Azure. Det inbyggda stödet för Event Hubs gör Spark-kluster i HDInsight till en perfekt plattform för att skapa en pipeline för realtidsanalyser.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Du kan använda följande artiklar om du vill veta mer om Apache Spark i HDInsight:

- [Snabbstart: Skapa ett Apache Spark-kluster i HDInsight och köra interaktiva frågor med hjälp av Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
- [Självstudier: Köra ett Apache Spark-jobb med hjälp av Jupyter](./apache-spark-load-data-run-query.md)
- [Självstudier: Analysera data med BI-verktyg](./apache-spark-use-bi-tools.md)
- [Självstudier: Maskininlärning med Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
- [Självstudier: Skapa ett Scala Maven-program med IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Nästa steg

I den här översikten får du viss grundläggande förståelse för Apache Spark i Azure HDInsight. Gå vidare till nästa artikel om du vill lära dig att skapa ett HDInsight Spark-kluster och köra några Spark SQL-frågor:

- [Skapa ett Apache Spark-kluster i HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
