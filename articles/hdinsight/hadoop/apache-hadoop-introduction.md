---
title: Vad är stacken för Apache Hadoop Technology? - Azure HDInsight
description: En introduktion till HDInsight och Apache Hadoop Technology stack och komponenter.
keywords: Azure hadoop, hadoop azure, introduktion av hadoop, introduktion av hadoop, hadoop-teknikstacken, introduktion till hadoop, introduktion till hadoop, vad är ett hadoop-kluster, vad är hadoop-kluster, vad används hadoop för
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 08/15/2019
ms.openlocfilehash: 193b8f59a805de4303bfc38d397a821cc068dcc0
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088190"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Vad är Apache Hadoop i Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) refererar till ett ekosystem av program med öppen källkod som är ett ramverk för distribuerad bearbetning och analys av stordata-uppsättningar i kluster. Hadoop-eko systemet innehåller relaterad program vara och verktyg, inklusive Apache Hive, Apache HBase, Spark, Kafka och många andra.

Azure HDInsight är en fullständigt hanterad analys tjänst med full spektrum och öppen källkod i molnet för företag. Med kluster typen Apache Hadoop i Azure HDInsight kan du använda HDFS, garn resurs hantering och en enkel MapReduce programmerings modell för att bearbeta och analysera batch-data parallellt.

För att se tillgängliga stack-komponenter för Hadoop Technology i HDInsight, se [komponenter och versioner som är tillgängliga med HDInsight](../hdinsight-component-versioning.md). Mer information om Hadoop i HDInsight finns på [sidan om Azure-funktioner för HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a id="whatis"></a>Vad är MapReduce

Apache Hadoop MapReduce är ett ramverk för program vara som används för att skriva jobb som bearbetar stora mängder data. Indata delas upp i oberoende segment. Varje segment bearbetas parallellt över noderna i klustret. Ett MapReduce-jobb består av två funktioner:

* **Mapper**: Använder indata, analyserar dem (vanligt vis med filter-och sorterings åtgärder) och genererar tupler (nyckel/värde-par)

* **Minskning**: Använder tupel som genereras av mappningen och utför en sammanfattnings åtgärd som skapar ett mindre kombinerat resultat från Mapper-data

Ett exempel på en grundläggande ord räkning för MapReduce-jobb illustreras i följande diagram:   

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Utdata för det här jobbet är antalet hur många gånger varje ord har inträffat i texten.

* Mapper tar varje rad från inmatade text som inmatade och delar upp den i ord. Den genererar ett nyckel/värde-par varje gången ett ord inträffar för ordet följt av 1. Utdata sorteras innan de skickas till minsknings verktyget.
* Minskningen summerar dessa enskilda antal för varje ord och avger ett nyckel/värde-par som innehåller ordet följt av summan av dess förekomster.

MapReduce kan implementeras på olika språk. Java är den vanligaste implementeringen och används i demonstrations syfte i det här dokumentet.

## <a name="development-languages"></a>Utvecklings språk

Språk eller ramverk som baseras på Java och Java Virtual Machine kan köras direkt som ett MapReduce-jobb. Exemplet som används i det här dokumentet är ett Java MapReduce-program. Andra språk än Java, till exempel C#python eller fristående körbara filer, måste använda **Hadoop-direktuppspelning**.

Hadoop-direktuppspelning kommunicerar med mapper och minskar för STDIN och STDOUT. Mapper och dereducerar Läs data en rad i taget från STDIN och skriver utdata till STDOUT. Varje rad som läses eller genereras av mapper och-minskningen måste vara i formatet för ett nyckel/värde-par, avgränsade med ett tabbtecken:

    [key]/t[value]

Mer information finns i [Hadoop streaming](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Exempel på användning av Hadoop streaming med HDInsight finns i följande dokument:

* [Utveckla C# MapReduce-jobb](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa Apache Hadoop-kluster i HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
