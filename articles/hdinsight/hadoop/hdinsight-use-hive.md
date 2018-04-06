---
title: Vad är Apache Hive och HiveQL - Azure HDInsight | Microsoft Docs
description: Apache Hive är ett data warehouse system för Hadoop. Du kan fråga efter data som lagras i Hive med HiveQL, som liknar Transact-SQL. Lär dig hur du använder Hive och HiveQL med Azure HDInsight i det här dokumentet.
keywords: hiveql, vad är hive, hadoop hiveql, hur du använder hive, Läs hive, vad är hive
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: afd2bc95beb2458ec149824723ec62381b31b2da
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Vad är Apache Hive och HiveQL på Azure HDInsight?

[Apache Hive](http://hive.apache.org/) är ett data warehouse system för Hadoop. Hive kan sammanfatta data, frågor och analys av data. Hive-frågor är skrivna i HiveQL, vilket är ett frågespråk som liknar SQL.

Du kan projektstrukturen på i stort sett Ostrukturerade data i hive. Du kan använda HiveQL för att fråga efter data utan kännedom om Java eller MapReduce när du har definierat strukturen.

HDInsight tillhandahåller flera klustertyper som är anpassade för specifika arbetsbelastningar. Följande typer av klustret används oftast för Hive-frågor:

* __Interaktiva frågan__: A Hadoop-kluster som innehåller [låg latens analytisk behandling (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funktioner för att förbättra svarstiden för interaktiva frågor. Mer information finns i [starta med interaktiva fråga i HDInsight](../interactive-query/apache-interactive-query-get-started.md) dokumentet.

* __Hadoop__: A Hadoop-kluster som är anpassad för batchbearbetning arbetsbelastningar. Mer information finns i [starta med Hadoop i HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentet.

* __Spark__: Apache Spark har inbyggda funktioner för att arbeta med Hive. Mer information finns i [börja med Spark i HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentet.

* __HBase__: HiveQL kan användas för att fråga efter data lagras i HBase. Mer information finns i [börjar med HBase på HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentet.

## <a name="how-to-use-hive"></a>Hur du använder Hive

Använd följande tabell för att identifiera de olika sätten att använda Hive med HDInsight:

| **Använd den här metoden** om du vill... | ... **interaktiva** frågor | ...**batch** bearbetning | ...med detta **klustret operativsystem** | ...from detta **klientoperativsystem** |
|:--- |:---:|:---:|:--- |:--- |
| [Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Alla (webbläsarbaserade) |
| [Beeline klienten](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X eller Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux- eller Windows * |Linux, Unix, Mac OS X eller Windows |
| [HDInsight tools för Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X eller Windows |
| [HDInsight tools för Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux- eller Windows * |Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux- eller Windows * |Windows |

> [!IMPORTANT]
> \* Linux är det enda operativsystem som används i HDInsight version 3.4 eller senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="hiveql-language-reference"></a>Språkreferens för HiveQL

Språkreferens för HiveQL är tillgängliga i den [språk manuell (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive och data struktur

Hive förstår hur du arbetar med strukturerade och halvstrukturerade data. Till exempel textfiler där fälten avgränsas med tecken. Följande HiveQL-sats skapar en tabell över blankstegsavgränsad data:

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

Hive även stöder anpassad **serialiseraren/deserializers (SerDe)** för komplex eller oregelbundet strukturerade data. Mer information finns i [hur du använder en anpassad JSON-SerDe med HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) dokumentet.

Mer information om filformat som stöds av Hive finns i [språk manuell (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Hive interna register vs externa tabeller

Det finns två typer av tabeller som du kan skapa med Hive:

* __Internt__: Data lagras i datalagret Hive. Datalagret finns i `/hive/warehouse/` på standardlagring för klustret.

    Använd internt tabeller när något av följande villkor gäller:

    * Data är tillfällig.
    * Vill du Hive för att hantera livscykeln för tabellen och data.

* __Externa__: Data lagras utanför datalagret. Data kan lagras på all tillgänglig lagring av klustret.

    Använda externa tabeller när något av följande villkor gäller:

    * Data används också utanför Hive. Till exempel har datafilerna uppdaterats av en annan process (som inte låser filer.)
    * Data behöver kvar på den underliggande platsen även efter att släppa tabellen.
    * Du behöver en anpassad plats, till exempel ett lagringskonto som inte är standard.
    * Ett annat program än hive hanterar dataformat, platsen, osv.

Mer information finns i [Hive interna och externa tabeller introduktion] [ cindygross-hive-tables] blogginlägg.

## <a name="user-defined-functions-udf"></a>Användardefinierade funktioner (UDF)

Hive kan också utökas genom **användardefinierade funktioner (UDF)**. En UDF kan du implementera funktioner eller som inte är enkelt modelleras i HiveQL. Ett exempel på hur UDF: er med Hive finns i följande dokument:

* [Använda en användardefinierad funktion i Java med Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Använda Python användardefinierad funktion med Hive](../hadoop/python-udf-hdinsight.md)

* [Använda en C# användardefinierad funktion med Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Lägga till en anpassad Hive användardefinierad funktion i HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Ett exempel Hive användardefinierad funktion för att konvertera datum/tid-format till Hive tidsstämpel](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Exempeldata

Hive i HDInsight levereras förinstallerade med en intern tabell med namnet `hivesampletable`. HDInsight tillhandahåller även exempel datamängder som kan användas med Hive. Dessa uppgifter som lagras i den `/example/data` och `/HdiSamples` kataloger. Dessa kataloger finns i standardlagring för klustret.

## <a id="job"></a>Exempel Hive-fråga

Följande HiveQL-instruktioner projektet kolumner till den `/example/data/sample.log` filen:

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

I det förra exemplet utför HiveQL-instruktioner följande åtgärder:

* `set hive.execution.engine=tez;`: Anger motorn för körning av du använder Tez. Tez ger en ökning i frågeprestanda. Mer information om Tez finns i [använda Apache Tez för bättre prestanda](#usetez) avsnitt.

    > [!NOTE]
    > Den här instruktionen är bara krävs när du använder ett Windows-baserade HDInsight-kluster. Tez är standard Körningsmotor för Linux-baserade HDInsight.

* `DROP TABLE`: Om tabellen redan finns tar du bort den.

* `CREATE EXTERNAL TABLE`: Skapar en ny **externa** tabellen i Hive. Externa tabeller kan du bara lagra tabelldefinitionen i Hive. Data finns kvar i den ursprungliga platsen och det ursprungliga formatet.

* `ROW FORMAT`: Anger hur data är formaterad Hive. I det här fallet avgränsas fälten i varje logg med ett blanksteg.

* `STORED AS TEXTFILE LOCATION`: Talar om Hive var data lagras (den `example/data` directory) och att den lagras som text. Data kan vara i en fil eller sprida över flera filer i katalogen.

* `SELECT`: Väljer en uppräkning av alla rader där kolumnen **t4** innehåller värdet **[fel]**. Den här instruktionen returnerar ett värde för **3** eftersom det finns tre rader som innehåller det här värdet.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive försöker använda schemat för alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra ogiltiga data i resultaten visar den här instruktionen Hive vi bör endast returnera data från filer som slutar på. log.

> [!NOTE]
> Externa tabeller ska användas när du förväntar dig underliggande data uppdateras av en extern källa. Till exempel en automatisk överföring av data eller MapReduce-åtgärden.
>
> Släppa en extern tabell har **inte** ta bort data, tar endast bort tabelldefinitionen.

Så här skapar du en **interna** tabellen i stället för externa ska du använda följande HiveQL:

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

* `CREATE TABLE IF NOT EXISTS`: Skapa den om tabellen inte finns. Eftersom den **externa** nyckelordet används inte, skapar en intern tabell för den här instruktionen. Tabellen lagras i datalagret Hive och hanteras helt av Hive.

* `STORED AS ORC`: Lagrar data i optimerad raden kolumner (ORC)-format. ORC är ett mycket optimerad och effektiv format för att lagra data med Hive.

* `INSERT OVERWRITE ... SELECT`: Väljer rader från den **log4jLogs** tabell som innehåller **[fel]**, och infogar data till den **errorLogs** tabell.

> [!NOTE]
> Till skillnad från externa tabeller kan släppa en intern tabell tas även bort underliggande data.

## <a name="improve-hive-query-performance"></a>Förbättra prestanda för Hive-frågor

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) är ett ramverk som gör att dataintensiva applikationer, som Hive, kan köras mer effektivt i större skala. Tez är aktiverad som standard för Linux-baserade HDInsight-kluster.

> [!NOTE]
> Tez är inaktiverat som standard för Windows-baserade HDInsight-kluster och den måste aktiveras. Om du vill dra nytta av Tez måste följande värde anges för en Hive-fråga:
>
> `set hive.execution.engine=tez;`
>
> Tez är standard-motor för Linux-baserade HDInsight-kluster.

Den [Hive i Tez designdokument](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) innehåller information om alternativ för implementering och prestandajustering konfigurationer.

För att underlätta felsökning jobb kördes med Tez, HDInsight tillhandahåller följande web användargränssnitt som gör att du vill visa detaljer för Tez jobb:

* [Använd vyn Ambari Tez på Linux-baserat HDInsight](../hdinsight-debug-ambari-tez-view.md)

* [Använda Tez-Användargränssnittet på Windows-baserade HDInsight](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Låg latens Analytical Processing (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (kallas ibland Live långa och processen) är en ny funktion i Hive 2.0 som kan cachelagra i minnet för frågor. LLAP gör Hive-frågor som är mycket snabbare till [26 x snabbare än Hive 1.x i vissa fall](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight tillhandahåller LLAP i klustret för interaktiva frågetypen. Mer information finns i [starta med interaktiva fråga](../interactive-query/apache-interactive-query-get-started.md) dokumentet.

## <a name="scheduling-hive-queries"></a>Schemalägga Hive-frågor

Det finns flera tjänster som kan användas för att köra Hive-frågor som en del av ett arbetsflöde schemalagda eller på begäran.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory kan du använda HDInsight som en del av Data Factory-pipelinen. Mer information om hur du använder Hive från en pipeline finns i [Transformera data med hjälp av Hive aktivitet i Azure Data Factory](/data-factory/transform-data-using-hadoop-hive.md) dokumentet.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-jobb och SQL Server Integration Services

Du kan använda SQL Server Integration Services (SSIS) för att köra en Hive-jobb. Azure-Funktionspaket för SSIS tillhandahåller följande komponenter som arbetar med Hive-jobb i HDInsight.

* [Azure HDInsight Hive-aktivitet][hivetask]

* [Azure prenumeration Anslutningshanteraren][connectionmanager]

Mer information finns i [Azure Feature Pack] [ ssispack] dokumentation.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie är ett arbetsflöde och samordning system som hanterar Hadoop-jobb. Mer information om hur du använder Oozie med Hive finns i [Använd Oozie att definiera och köra ett arbetsflöde](../hdinsight-use-oozie-linux-mac.md) dokumentet.

## <a id="nextsteps"></a>Nästa steg

Nu när du har lärt dig Hive är och hur du använder med Hadoop i HDInsight, Använd följande länkar för att undersöka andra sätt att arbeta med Azure HDInsight.

* [Överföra data till HDInsight][hdinsight-upload-data]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Använda MapReduce-jobb med HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

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
