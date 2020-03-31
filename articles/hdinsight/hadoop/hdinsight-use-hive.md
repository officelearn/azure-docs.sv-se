---
title: Vad är Apache Hive och HiveQL - Azure HDInsight
description: Apache Hive är ett datalagersystem för Apache Hadoop. Du kan fråga data som lagras i Hive med HiveQL, som liknar Transact-SQL. I det här dokumentet får du lära dig hur du använder Hive och HiveQL med Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: f7dc7b520cba2bbf2351d93795a1a26b3b5124be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471361"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Vad är Apache Hive och HiveQL på Azure HDInsight?

[Apache Hive](https://hive.apache.org/) är ett datalagersystem för Apache Hadoop. Hive möjliggör datasumrisering, frågor och analys av data. Hive-frågor skrivs i HiveQL, vilket är ett frågespråk som liknar SQL.

Hive kan du projektstruktur på i stort sett ostrukturerade data. När du har definierat strukturen kan du använda HiveQL för att fråga data utan kunskap om Java eller MapReduce.

HDInsight innehåller flera klustertyper som är inställda för specifika arbetsbelastningar. Följande klustertyper används oftast för Hive-frågor:

|Klustertyp |Beskrivning|
|---|---|
|Interaktiv fråga|Ett Hadoop-kluster som tillhandahåller [LLAP-funktioner (Low LateEncy Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP) för att förbättra svarstiderna för interaktiva frågor. Mer information finns [i Start med interaktiv fråga i HDInsight-dokumentet.](../interactive-query/apache-interactive-query-get-started.md)|
|Hadoop|Ett Hadoop-kluster som är inställt för arbetsbelastningar för batchbearbetning. Mer information finns i [dokumentet Starta med Apache Hadoop i HDInsight.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)|
|Spark|Apache Spark har inbyggda funktioner för att arbeta med Hive. Mer information finns i [dokumentet Starta med Apache Spark på HDInsight.](../spark/apache-spark-jupyter-spark-sql.md)|
|HBase|HiveQL kan användas för att fråga data som lagras i Apache HBase. Mer information finns i [dokumentet Start med Apache HBase på HDInsight.](../hbase/apache-hbase-tutorial-get-started-linux.md)|

## <a name="how-to-use-hive"></a>Hur man använder Hive

Använd följande tabell för att identifiera olika sätt att använda Hive med HDInsight:

| **Använd den här metoden** om du vill... | ... **interaktiva** frågor | ... **batchbearbetning** | ... från det här **klientoperativsystemet** |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight-verktyg för Visual Studio-kod](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X eller Windows |
| [HDInsight-verktyg för Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Alla (webbläsarbaserade) |
| [Beeline-klient](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X eller Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X eller Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Språkreferens för HiveQL

HiveQL språkreferens finns i [språkhandboken](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive och datastruktur

Hive förstår hur man arbetar med strukturerade och halvstrukturerade data. Textfiler där fälten avgränsas av specifika tecken. I följande HiveQL-sats skapas en tabell över mellanslagna data:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive stöder också anpassade **serializer/deserializers (SerDe)** för komplexa eller oregelbundet strukturerade data. Mer information finns i [Hur du använder ett anpassat JSON SerDe med HDInsight-dokument.](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)

Mer information om filformat som stöds av Hive finns i [språkhandbokenhttps://cwiki.apache.org/confluence/display/Hive/LanguageManual) (](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Interna tabeller för Hive jämfört med externa tabeller

Det finns två typer av tabeller som du kan skapa med Hive:

* __Internt__: Data lagras i Hive-informationslagret. Informationslagret finns `/hive/warehouse/` på standardlagringen för klustret.

    Använd interna tabeller när något av följande villkor gäller:

    * Data är tillfälliga.
    * Du vill att Hive ska hantera livscykeln för tabellen och data.

* __Extern__: Data lagras utanför informationslagret. Data kan lagras på alla lagringar som är tillgängliga för klustret.

    Använd externa tabeller när något av följande gäller:

    * Uppgifterna används också utanför Hive. Datafilerna uppdateras till exempel av en annan process (som inte låser filerna.)
    * Data måste finnas kvar på den underliggande platsen, även efter att du har tappat tabellen.
    * Du behöver en anpassad plats, till exempel ett lagringskonto som inte är standard.
    * Ett annat program än registreringsdatadataformat, plats och så vidare.

Mer information finns i blogginlägget om [intron till Hive-blogginlägget](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/) för interna och externa tabeller.

## <a name="user-defined-functions-udf"></a>Användardefinierade funktioner (UDF)

Hive kan också utökas genom **användardefinierade funktioner (UDF).** Med en UDF kan du implementera funktioner eller logik som inte är lätt modelleras i HiveQL. Ett exempel på hur du använder UDF:er med Hive finns i följande dokument:

* [Använda en Java-användardefinierad funktion med Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Använda en Användardefinierad Python-funktion med Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Använda en C#-användardefinierad funktion med Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Så här lägger du till en anpassad Apache Hive-användardefinierad funktion i HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Ett exempel på apache hive-användardefinierad funktion för att konvertera datum-/tidsformat till Hive-tidsstämpel](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Exempeldata

Hive på HDInsight levereras förinstallerad med en intern tabell med namnet `hivesampletable`. HDInsight ger också exempel på datauppsättningar som kan användas med Hive. Dessa datauppsättningar lagras `/example/data` `/HdiSamples` i katalogerna och. Dessa kataloger finns i standardlagringen för klustret.

## <a name="example-hive-query"></a>Exempel på Hive-fråga

Följande HiveQL-satser projicerar kolumner på `/example/data/sample.log` filen:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

I föregående exempel utför HiveQL-satser följande åtgärder:

|Instruktion |Beskrivning |
|---|---|
|SLÄPP TABELL|Om tabellen redan finns tar du bort den.|
|SKAPA EXTERN TABELL|Skapar en ny **extern** tabell i Hive. Externa tabeller lagrar bara tabelldefinitionen i Hive. Data finns kvar på den ursprungliga platsen och i det ursprungliga formatet.|
|RADFORMAT|Talar om för Hive hur data formateras. I det här fallet avgränsas fälten i varje logg med ett blanksteg.|
|LAGRAD SOM TEXTFILE-PLATS|Talar om för Hive var `example/data` data lagras (katalogen) och att de lagras som text. Data kan finnas i en fil eller spridas över flera filer i katalogen.|
|VÄLJ|Markerar ett antal rader där kolumnen **t4** innehåller värdet **[FEL]**. Den här utdraget returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.|
|INPUT__FILE__NAME SOM %.log|Hive försöker tillämpa schemat på alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra skräpdata i resultaten, säger den här satsen till Hive att vi bara ska returnera data från filer som slutar i .log.|

> [!NOTE]  
> Externa tabeller bör användas när du förväntar dig att de underliggande data som ska uppdateras av en extern källa. Till exempel en automatisk dataöverföringsprocess eller MapReduce-åtgärd.
>
> Om du släpper en extern tabell tas **inte** data bort, tabelldefinitionen tas bara bort.

Om du vill skapa en **intern** tabell i stället för extern använder du följande HiveQL:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Dessa satser utför följande åtgärder:

|Instruktion |Beskrivning |
|---|---|
|SKAPA TABELL OM DET INTE FINNS|Om tabellen inte finns skapar du den. Eftersom nyckelordet **EXTERNAL** inte används skapas en intern tabell i den här satsen. Tabellen lagras i Hive-informationslagret och hanteras helt av Hive.|
|LAGRAS SOM ORC|Lagrar data i ORC-format (Optimized Row Columnar). ORC är ett mycket optimerat och effektivt format för lagring av Hive-data.|
|SÄTT ÖVER SKRIVA ÖVER ... Välj|Markerar rader i tabellen **log4jLogs** som innehåller **[FEL]** och infogar sedan data i tabellen **errorLogs.**|

> [!NOTE]  
> Till skillnad från externa tabeller tas även underliggande data om du släpper en intern tabell.

## <a name="improve-hive-query-performance"></a>Förbättra prestanda för Hive-frågor

### <a name="apache-tez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) är ett ramverk som gör att dataintensiva program, till exempel Hive, kan köras mycket mer effektivt i stor skala. Tez är aktiverad som standard.  [Apache Hive på Tez-designdokumenten](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) innehåller information om implementeringsalternativ och justeringskonfigurationer.

### <a name="low-latency-analytical-processing-llap"></a>Analytisk bearbetning med låg latens (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (kallas ibland Live Long och Process) är en ny funktion i Hive 2.0 som gör att minnescachelagring av frågor. LLAP gör Hive-frågor mycket snabbare, upp till [26x snabbare än Hive 1.x i vissa fall](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight tillhandahåller LLAP i klustertypen Interaktiv fråga. Mer information finns i dokumentet [Starta med interaktiv fråga.](../interactive-query/apache-interactive-query-get-started.md)

## <a name="scheduling-hive-queries"></a>Schemalägga Hive-frågor

Det finns flera tjänster som kan användas för att köra Hive-frågor som en del av ett schemalagt arbetsflöde eller arbetsflöde på begäran.

### <a name="azure-data-factory"></a>Azure Data Factory

Med Azure Data Factory kan du använda HDInsight som en del av en Data Factory-pipeline. Mer information om hur du använder Hive från en pipeline finns i [Transform-data med Hive-aktivitet i Azure Data](../../data-factory/transform-data-using-hadoop-hive.md) Factory-dokumentet.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-jobb och SQL Server Integration Services

Du kan använda SQL Server Integration Services (SSIS) för att köra ett Hive-jobb. Azure Feature Pack för SSIS innehåller följande komponenter som fungerar med Hive-jobb på HDInsight.

* [Azure HDInsight Hive-uppgift](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Azure-hanteraren för prenumerationsanslutning](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Mer information finns i dokumentationen till [Azure Feature Pack.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie är ett arbetsflöde och samordningssystem som hanterar Hadoop-jobb. Mer information om hur du använder Oozie med Hive finns i [Använd Apache Oozie för att definiera och köra ett arbetsflödesdokument.](../hdinsight-use-oozie-linux-mac.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig vad Hive är och hur du använder den med Hadoop i HDInsight använder du följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Överföra data till HDInsight](../hdinsight-upload-data.md)
* [Använd Python User Defined Functions (UDF) med Apache Hive och Apache Pig i HDInsight](./python-udf-hdinsight.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)
