---
title: Använda Apache Pig
titleSuffix: Azure HDInsight
description: Läs om hur du använder Pig med Apache Hadoop på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: ea960a92aee1c9447bb12d27cffdc42de9fd907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672131"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Använd Apache Pig med Apache Hadoop på HDInsight

Läs om hur du använder [Apache Pig](https://pig.apache.org/) med HDInsight.

Apache Pig är en plattform för att skapa program för Apache Hadoop med hjälp av ett förfarandespråk som kallas *Pig Latin*. Pig är ett alternativ till Java för att skapa *MapReduce-lösningar,* och det ingår i Azure HDInsight. Använd följande tabell för att upptäcka de olika sätt som Pig kan användas med HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Varför använda Apache Pig

En av utmaningarna med att bearbeta data med hjälp av MapReduce i Hadoop är att implementera din bearbetningslogik genom att bara använda en karta och en reduceringsfunktion. För komplex bearbetning måste du ofta dela upp bearbetningen i flera MapReduce-åtgärder som är sammankopplade för att uppnå önskat resultat.

Med Pig kan du definiera bearbetning som en serie omvandlingar som data flödar igenom för att producera önskad utdata.

Med det latinska språket Pig kan du beskriva dataflödet från råindata, genom en eller flera omvandlingar, för att producera önskad utdata. Pig latinska program följer detta allmänna mönster:

* **Läs in**: Läs data som ska manipuleras från filsystemet.

* **Transformera**: Manipulera data.

* **Dumpa eller lagra**: Mata ut data till skärmen eller lagra dem för bearbetning.

### <a name="user-defined-functions"></a>Användardefinierade funktioner

Pig Latin stöder också användardefinierade funktioner (UDF), som gör att du kan anropa externa komponenter som implementerar logik som är svår att modellera i Pig Latin.

För mer information om Pig Latin, se [Referenshandbok för gris latin 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) och [referensmanual för gris latin 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Exempeldata

HDInsight ger olika exempel datauppsättningar, `/example/data` `/HdiSamples` som lagras i och kataloger. Dessa kataloger finns i standardlagringen för klustret. I exemplet Pig i det här dokumentet `/example/data/sample.log`används *filen log4j* från .

Varje logg inuti filen består av en rad `[LOG LEVEL]` med fält som innehåller ett fält för att visa typen och allvarlighetsgraden, till exempel:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

I föregående exempel är loggnivån FEL.

> [!NOTE]  
> Du kan också generera en log4j-fil med hjälp av [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) loggningsverktyget och sedan ladda upp filen till din blob. Se [Ladda upp data till HDInsight](hdinsight-upload-data.md) för instruktioner. Mer information om hur blobbar i Azure-lagring används med HDInsight finns i [Använda Azure Blob Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Exempel på jobb

Följande pig latin jobb `sample.log` läser in filen från standardlagring för din HDInsight kluster. Sedan utför en serie omvandlingar som resulterar i ett antal av hur många gånger varje loggnivå inträffade i indata. Resultaten skrivs till STDOUT.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

Följande bild visar en sammanfattning av vad varje omvandling gör med data.

![Grafisk framställning av omformningar][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Kör pig latin jobb

HDInsight kan köra Pig Latin jobb med hjälp av en mängd olika metoder. Använd följande tabell för att bestämma vilken metod som är rätt för dig och följ sedan länken för en genomgång.

## <a name="pig-and-sql-server-integration-services"></a>Integrationstjänster för gris och SQL Server

Du kan använda SQL Server Integration Services (SSIS) för att köra ett Gris-jobb. Azure Feature Pack för SSIS innehåller följande komponenter som fungerar med Grisjobb på HDInsight.

* [Azure HDInsight Pig-uppgift][pigtask]

* [Azure-hanteraren för prenumerationsanslutning][connectionmanager]

Läs mer om Azure Feature Pack för SSIS [här][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Nästa steg

Nu när du har lärt dig hur du använder Pig med HDInsight kan du använda följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Överföra data till HDInsight](hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](./hadoop/hdinsight-use-hive.md)
* [Använda Apache Sqoop med HDInsight](hdinsight-use-sqoop.md)
* [Använda MapReduce-jobb med HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
