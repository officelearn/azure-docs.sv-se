---
title: Använda Apache gris
titleSuffix: Azure HDInsight
description: Lär dig hur du använder gris med Apache Hadoop i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 8c9e13a61f5fcb3b7eb170b2b1110195d725c3ce
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493168"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Använda Apache gris med Apache Hadoop på HDInsight

Lär dig hur du använder [Apache gris](https://pig.apache.org/) med HDInsight.

Apache gris är en plattform för att skapa program för Apache Hadoop med hjälp av ett procedur språk som kallas *gris Latin*. Gris är ett alternativ till Java för att skapa *MapReduce* -lösningar och det ingår i Azure HDInsight. Använd följande tabell för att identifiera de olika sätt som gris kan användas med HDInsight:

## <a id="why"></a>Varför ska man använda Apache gris

En av utmaningarna med att bearbeta data med hjälp av MapReduce i Hadoop är att implementera din bearbetnings logik genom att endast använda en karta och en minsknings funktion. Vid komplex bearbetning måste du ofta bryta bearbetningen till flera MapReduce-åtgärder som sammanställs tillsammans för att uppnå önskat resultat.

Med hjälp av gris kan du definiera bearbetning som en serie med omvandlingar som data flödar genom för att producera önskade utdata.

Med det latinska språket för svin kan du beskriva data flödet från rå data, via en eller flera transformeringar, för att skapa önskade utdata. De latinska programmen i gris följer detta allmänna mönster:

* **Load**: Läs data som ska hanteras från fil systemet.

* **Transform**: manipulera data.

* **Dumpa eller lagra**: mata ut data till skärmen eller lagra den för bearbetning.

### <a name="user-defined-functions"></a>Användardefinierade funktioner

Med det latinska tillägget i gris kan du även använda användardefinierade funktioner (UDF), vilket gör att du kan anropa externa komponenter som implementerar logik som är svårt att modellera i gris Latin.

För mer information om gris Latin, se [gris Latin Reference Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) och [latinsk referens hand bok 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a id="data"></a>Exempel data

HDInsight innehåller olika exempel data uppsättningar som lagras i `/example/data` och `/HdiSamples` kataloger. Dessa kataloger finns i standard lagrings utrymmet för klustret. I exemplet i det här dokumentet används *log4j* -filen från `/example/data/sample.log`.

Varje logg i filen består av en rad med fält som innehåller ett `[LOG LEVEL]` fält för att Visa typ och allvarlighets grad, till exempel:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

I föregående exempel är loggnings nivån fel.

> [!NOTE]  
> Du kan också skapa en log4j-fil med hjälp av verktyget [Apache log4j](https://en.wikipedia.org/wiki/Log4j) -loggning och sedan överföra filen till din BLOB. Instruktioner finns i [överföra data till HDInsight](hdinsight-upload-data.md) . Mer information om hur blobbar i Azure Storage används med HDInsight finns i [använda Azure Blob Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Exempel jobb

I följande exempel latinsk jobb läser in `sample.log`-filen från standard lagringen för ditt HDInsight-kluster. Sedan utför den en serie omvandlingar som resulterar i ett antal gånger som varje loggnings nivå inträffar i indata. Resultaten skrivs till STDOUT.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

Följande bild visar en sammanfattning av vad varje omvandling gör till data.

![Grafisk representation av transformationer][image-hdi-pig-data-transformation]

## <a id="run"></a>Köra det latinska jobbet för gris

HDInsight kan köra Fax latinska jobb genom att använda olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig bäst. Följ sedan länken för en genom gång.

## <a name="pig-and-sql-server-integration-services"></a>Gris och SQL Server Integration Services

Du kan använda SQL Server Integration Services (SSIS) för att köra ett gris-jobb. Azure Feature Pack för SSIS innehåller följande komponenter som fungerar med jobb för gris i HDInsight.

* [Aktivitet för Azure HDInsight-gris][pigtask]

* [Anslutnings hanteraren för Azure-prenumeration][connectionmanager]

Lär dig mer om Azure Feature Pack för SSIS [här][ssispack].

## <a id="nextsteps"></a>Nästa steg

Nu när du har lärt dig hur du använder gris med HDInsight kan du använda följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Ladda upp data till HDInsight](hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](/hadoop/hdinsight-use-hive.md)
* [Använda Apache Sqoop med HDInsight](hdinsight-use-sqoop.md)
* [Använda MapReduce-jobb med HDInsight](/hadoop/hdinsight-use-mapreduce.md)

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
