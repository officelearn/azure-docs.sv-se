---
title: Vad är Apache Hive och HiveQL - Azure HDInsight
description: Apache Hive är ett data warehouse-system för Hadoop. Du kan fråga efter data som lagras i Hive med HiveQL, vilket liknar Transact-SQL. I det här dokumentet lär du dig hur du använder Hive och HiveQL med Azure HDInsight.
keywords: hiveql, vad är hive, hadoop hiveql, hur du använder hive Läs hive, vad är hive
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 9f22766b0ecbaff027cf4b495624f892d4339d17
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249373"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Vad är Apache Hive och HiveQL på Azure HDInsight?

[Apache Hive](http://hive.apache.org/) är ett data warehouse-system för Hadoop. Hive kan datasammanfattning, frågor och analys av data. Hive-frågor skrivs i HiveQL, vilket är ett frågespråk som liknar SQL.

Du kan projektstruktur på i stort sett Ostrukturerade data i hive. Du kan använda HiveQL för att fråga efter data utan kunskaper om Java- eller MapReduce när du har definierat strukturen.

HDInsight innehåller flera klustertyper som är anpassade för specifika arbetsbelastningar. Följande klustertyper av används oftast för Hive-frågor:

* __Interaktiv fråga__: ett Hadoop-kluster som tillhandahåller [med låg latens analytisk bearbetning (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funktioner för att förbättra svarstiden för interaktiva frågor. Mer information finns i den [börja med interaktiv fråga i HDInsight](../interactive-query/apache-interactive-query-get-started.md) dokumentet.

* __Hadoop__: ett Hadoop-kluster som är anpassad för arbetsbelastningar för batchbearbetning. Mer information finns i den [börja med Hadoop i HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentet.

* __Spark__: Apache Spark finns en inbyggd funktion för att arbeta med Hive. Mer information finns i den [börja med Spark på HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentet.

* __HBase__: HiveQL kan användas för att köra frågor mot data som lagras i HBase. Mer information finns i den [börja med HBase på HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentet.

## <a name="how-to-use-hive"></a>Hur du använder Hive

Använd följande tabell för att identifiera de olika sätten att använda Hive med HDInsight:

| **Använd den här metoden** om du vill... | ... **interaktiva** frågor | ...**batch** bearbetning | ...med detta **klustret operativsystem** | ...from detta **klientoperativsystem** |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight tools för Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X eller Windows |
| [HDInsight tools för Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Linux eller Windows * |Windows |
| [Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Alla (webbläsarbaserade) |
| [Beeline klienten](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X eller Windows |
| [REST-API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux eller Windows * |Linux, Unix, Mac OS X eller Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux eller Windows * |Windows |

> [!IMPORTANT]
> \* Linux är det enda operativsystem som används på HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="hiveql-language-reference"></a>HiveQL Språkreferens

Språkreferens för HiveQL är tillgänglig i den [språk manuell (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive och data struktur

Hive förstår hur du arbetar med strukturerade och halvstrukturerade data. Till exempel textfiler där fälten avgränsas med specifika tecken. Följande HiveQL-instruktionen skapar en tabell över avgränsade med blanksteg data:

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

Hive även stöder anpassad **serialiserare/deserializers (SerDe)** för komplexa eller oregelbundet strukturerade data. Mer information finns i den [hur du använder en anpassad JSON-SerDe med HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) dokumentet.

Mer information om filformat som stöds av Hive finns i den [manuell (för språk https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Hive-interna tabeller jämfört med externa tabeller

Det finns två typer av tabeller som du kan skapa med Hive:

* __Interna__: Data lagras i datalagret Hive. Datalagret finns i `/hive/warehouse/` på standardlagringen för klustret.

    Använd interna tabeller när något av följande villkor gäller:

    * Data är tillfällig.
    * Vill du Hive för att hantera livscykeln för tabellen och data.

* __Externa__: Data lagras utanför datalagret. Data kan lagras på all tillgänglig lagring av klustret.

    Använda externa tabeller när något av följande villkor är uppfyllda:

    * Data används även utanför Hive. Till exempel har datafilerna uppdaterats av en annan process (som inte låser filerna.)
    * Data måste fortfarande visas i den underliggande platsen när släppa tabellen.
    * Du behöver en annan plats, till exempel ett icke-standard storage-konto.
    * Ett annat program än hive hanterar dataformat, platsen, osv.

Mer information finns i den [Hive interna och externa tabeller introduktion] [ cindygross-hive-tables] blogginlägg.

## <a name="user-defined-functions-udf"></a>Användardefinierade funktioner (UDF)

Hive kan också utökas via **användardefinierade funktioner (UDF)**. En UDF kan du implementera funktioner eller logik som inte är enkelt modellerats i HiveQL. Ett exempel på hur du använder UDF med Hive finns i följande dokument:

* [Använda en Java-användardefinierade funktion med Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Använda en användardefinierad funktion i Python med Hive](../hadoop/python-udf-hdinsight.md)

* [Använda en C#-användardefinierade funktion med Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Lägga till en anpassad Hive användardefinierad funktion i HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Ett exempel Hive användardefinierad funktion för att konvertera datum/tid-format till Hive-tidsstämpel](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Exempeldata

Hive på HDInsight levereras med en intern tabell med namnet `hivesampletable`. HDInsight tillhandahåller även exempel datauppsättningar som kan användas med Hive. Dessa datauppsättningar lagras i den `/example/data` och `/HdiSamples` kataloger. Dessa kataloger finns i standardlagringen för klustret.

## <a id="job"></a>Exempel Hive-fråga

Följande HiveQL-instruktioner projektet kolumner till den `/example/data/sample.log` fil:

```hiveql
set hive.execution.engine=tez;
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

I exemplet ovan utför HiveQL-instruktioner följande åtgärder:

* `set hive.execution.engine=tez;`: Anger motorn för körning av att använda Tez. Använda Tez kan ge en ökning av frågeprestanda. Mer information om Tez finns i den [använda Apache Tez för bättre prestanda](#usetez) avsnittet.

    > [!NOTE]
    > Den här instruktionen är endast krävs när du använder ett Windows-baserade HDInsight-kluster. Tez är standard-motorn för körning för Linux-baserade HDInsight.

* `DROP TABLE`: Ta bort om tabellen redan finns.

* `CREATE EXTERNAL TABLE`: Skapar en ny **externa** tabellen i Hive. Externa tabeller kan du bara lagra tabelldefinitionen i Hive. Data är kvar i den ursprungliga platsen och det ursprungliga formatet.

* `ROW FORMAT`: Visar Hive hur informationen har formaterats. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

* `STORED AS TEXTFILE LOCATION`: Visar Hive där data lagras (den `example/data` directory) och att den lagras som text. Data kan finnas i en fil eller sprids över flera filer i katalogen.

* `SELECT`: Väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Det här uttrycket returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive försöker använda schemat för alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra skräpinsamling data i resultatet, meddelar den här instruktionen Hive vi bör endast returnera data från filer som slutar på. log.

> [!NOTE]
> Externa tabeller som ska användas när du förväntar dig att underliggande data uppdateras av en extern källa. Till exempel en automatiserade uppladdningen eller MapReduce-åtgärden.
>
> Tar bort en extern tabell har **inte** ta bort data, tar endast bort tabelldefinitionen.

Skapa en **interna** tabellen i stället för externa, Använd följande HiveQL:

```hiveql
set hive.execution.engine=tez;
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

Dessa instruktioner utför följande åtgärder:

* `CREATE TABLE IF NOT EXISTS`: Om tabellen inte finns, skapar du den. Eftersom den **externa** nyckelord används inte, den här instruktionen skapar en intern tabell. Tabellen lagras i datalagret Hive och hanteras helt av Hive.

* `STORED AS ORC`: Lagrar data i optimerade rad kolumner (ORC)-format. ORC är ett mycket optimerade och effektiv format för att lagra Hive-data.

* `INSERT OVERWRITE ... SELECT`: Väljer rader från den **log4jLogs** tabell som innehåller **[fel]**, och sedan infogar data till den **felvillkoren** tabell.

> [!NOTE]
> Till skillnad från externa tabeller, släppa en intern tabell tar även bort underliggande data.

## <a name="improve-hive-query-performance"></a>Förbättra prestanda för Hive-frågor

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) är ett ramverk som gör att dataintensiva applikationer, som Hive, kan köras mer effektivt i större skala. Tez är aktiverat som standard för Linux-baserade HDInsight-kluster.

> [!NOTE]
> Tez är för närvarande inaktiverat som standard för Windows-baserade HDInsight-kluster och den måste aktiveras. Om du vill dra nytta av Tez, måste du ange följande värde för en Hive-fråga:
>
> `set hive.execution.engine=tez;`
>
> Tez är standard-motor för Linux-baserade HDInsight-kluster.

Den [Hive i Tez designdokument](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) innehåller information om alternativ för implementering och justering konfigurationer.

För att underlätta felsökning jobb kördes med Tez, HDInsight innehåller följande web UIs så att du kan visa information om Tez-jobb:

* [Använda Ambari Tez-vyn på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

* [Använda Tez-Användargränssnittet på Windows-baserade HDInsight](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Låg latens Analytical Processing (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (kallas ibland Live Long and Process) är en ny funktion i Hive 2.0 som gör att cachelagra i minnet för frågor. LLAP gör Hive-frågor som är mycket snabbare, upp till [26 x snabbare än Hive 1.x i vissa fall](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight ger LLAP i klustertypen interaktiv fråga. Mer information finns i den [börja med interaktiv fråga](../interactive-query/apache-interactive-query-get-started.md) dokumentet.

## <a name="scheduling-hive-queries"></a>Schemaläggning av Hive-frågor

Det finns flera tjänster som kan användas för att köra Hive-frågor som en del av ett arbetsflöde för schemalagd eller på begäran.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory kan du använda HDInsight som en del av Data Factory-pipeline. Mer information om hur du använder Hive från en pipeline finns i den [Transformera data med Hive-aktivitet i Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) dokumentet.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-jobb och SQL Server Integration Services

Du kan använda SQL Server Integration Services (SSIS) för att köra ett Hive-jobb. Azure Feature Pack för SSIS innehåller följande komponenter som fungerar med Hive-jobb på HDInsight.

* [Azure HDInsight Hive-aktivitet][hivetask]

* [Azure prenumeration Anslutningshanteraren][connectionmanager]

Mer information finns i den [Azure Feature Pack] [ ssispack] dokumentation.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie är ett arbetsflöde och koordination system som hanterar Hadoop-jobb. Mer information om att använda Oozie med Hive finns i den [Använd Oozie att definiera och köra ett arbetsflöde](../hdinsight-use-oozie-linux-mac.md) dokumentet.

## <a id="nextsteps"></a>Nästa steg

Nu när du har lärt dig vad Hive är och hur du använder det med Hadoop i HDInsight, kan du använda följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Överföra data till HDInsight][hdinsight-upload-data]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Använda MapReduce-jobb med HDInsight][hdinsight-use-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
