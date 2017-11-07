---
title: "Använda Pig med Hadoop i HDInsight | Microsoft Docs"
description: "Lär dig använda Pig med Hadoop i HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: larryfr
ms.openlocfilehash: 5f5b34c6610a1c07687f0681480f7422986d6f20
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Använda Pig med Hadoop i HDInsight

Lär dig hur du använder [Apache Pig](http://pig.apache.org/) med HDInsight...

Pig är en plattform för att skapa program för Hadoop med hjälp av en procedurmässig språk som kallas *Pig Latin*. Pig är ett alternativ till Java för att skapa *MapReduce* lösningar och ingår i Azure HDInsight. Använd följande tabell för att identifiera de olika sätt som svin kan användas med HDInsight:

| **Använd den här** om du vill... | ...an **interaktiva** shell | ...**batch** bearbetning | ...med detta **klustret operativsystem** | ...from detta **klientoperativsystem** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X eller Windows |
| [REST-API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux- eller Windows |Linux, Unix, Mac OS X eller Windows |
| [.NET SDK för Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux- eller Windows |Windows (för tillfället) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux- eller Windows |Windows |
| [Fjärrskrivbord](apache-hadoop-use-pig-remote-desktop.md) (HDInsight 3.2 och 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Varför använda Pig

En av utmaningarna med bearbetning av data genom att använda MapReduce i Hadoop implementerar logik för bearbetning med hjälp av endast en karta och minska funktionen. För komplexa bearbetning, behöver du ofta Bryt bearbetning i flera MapReduce-åtgärder som är sammankedjade tillsammans för att uppnå önskat resultat.

Pig kan du definiera bearbetning som en serie transformationer som data flödar genom för att skapa önskade utgående.

Pig Latin-språk kan du beskriva dataflödet från rådata indata via en eller flera omvandlingar till önskat resultat. Pig Latin program följer detta allmänna mönster:

* **Läs in**: läsa data hanteras från filsystemet

* **Transformera**: manipulera data

* **Dump eller lagra**: utdata till skärmen eller lagra för bearbetning

### <a name="user-defined-functions"></a>Användardefinierade funktioner

Pig Latin stöder även användardefinierade funktioner (UDF), där du kan anropa externa komponenter som implementerar logik som är svår att modellen i Pig Latin.

Läs mer om Pig Latin [Pig Latin referens manuell 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) och [Pig Latin referens manuell 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Ett exempel på med Pig UDF: er, finns i följande dokument:

* [Använda DataFu med Pig i HDInsight](apache-hadoop-use-pig-datafu-udf.md) -DataFu är en samling användbar UDF: er som underhålls av Apache
* [Använda Python med Pig och Hive i HDInsight](python-udf-hdinsight.md)
* [Använda C# med Hive och Pig i HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Exempeldata

HDInsight tillhandahåller olika exempel datauppsättningar som lagras i den `/example/data` och `/HdiSamples` kataloger. Dessa kataloger finns i standardlagring för klustret. Pig-exemplet i det här dokumentet används de *log4j* filen från `/example/data/sample.log`.

Varje logg i filen består av en rad med fält som innehåller en `[LOG LEVEL]` fält att visa typen och allvarlighetsgrad, till exempel:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

I föregående exempel är loggningsnivån fel.

> [!NOTE]
> Du kan också generera en log4j-fil med hjälp av den [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) loggning verktyget och överför denna fil till ditt blob. Se [överför Data till HDInsight](../hdinsight-upload-data.md) anvisningar. Mer information om hur du använder blobbar i Azure storage med HDInsight finns [använda Azure Blob Storage med HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Exempel jobb

Följande Pig Latin jobbet läser in den `sample.log` filen från standard lagringen för ditt HDInsight-kluster. Därefter utför en serie transformationer som resulterar i en uppräkning av hur många gånger varje loggningsnivån uppstod i indata. Resultaten skrivs till STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Följande bild visar en sammanfattning av vad varje omvandling gör att data.

![Grafisk representation av omvandlingarna][image-hdi-pig-data-transformation]

## <a id="run"></a>Köra Pig Latin-jobbet

HDInsight kan köra Pig Latin jobb med hjälp av olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig bäst och följ länken för en genomgång.

| **Använd den här** om du vill... | ...an **interaktiva** shell | ...**batch** bearbetning | ...med detta **klustret operativsystem** | ...from detta **klienten** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X eller Windows |
| [CURL](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux- eller Windows |Linux, Unix, Mac OS X eller Windows |
| [.NET SDK för Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux- eller Windows |Windows (för tillfället) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux- eller Windows |Windows |
| [Fjärrskrivbord](apache-hadoop-use-pig-remote-desktop.md) (HDInsight 3.2 och 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Pig och SQL Server Integration Services

Du kan använda SQL Server Integration Services (SSIS) för att köra Pig-jobbet. Azure-Funktionspaket för SSIS tillhandahåller följande komponenter som arbetar med Pig-jobb i HDInsight.

* [Azure HDInsight Pig-aktivitet][pigtask]

* [Azure prenumeration Anslutningshanteraren][connectionmanager]

Läs mer om Azure Feature Pack för SSIS [här][ssispack].

## <a id="nextsteps"></a>Nästa steg
Nu när du har lärt dig hur du använder Pig med HDInsight, Använd följande länkar för att undersöka andra sätt att arbeta med Azure HDInsight.

* [Överföra data till HDInsight](../hdinsight-upload-data.md)
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Sqoop med HDInsight](hdinsight-use-sqoop.md)
* [Använda Oozie med HDInsight](../hdinsight-use-oozie.md)
* [Använda MapReduce-jobb med HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
