---
title: Vad är Apache Hadoop och MapReduce - Azure HDInsight
description: En introduktion till HDInsight och Apache Hadoop-teknikstacken och komponenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244874"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Vad är Apache Hadoop i Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) refererar till ett ekosystem av program med öppen källkod som är ett ramverk för distribuerad bearbetning och analys av stordata-uppsättningar i kluster. Hadoop-ekosystemet innehåller relaterad programvara och verktyg, inklusive Apache Hive, Apache HBase, Spark, Kafka och många andra.

Azure HDInsight är en fullständigt hanterad analystjänst med öppen källkod med full spektrum i molnet för företag. Med Apache Hadoop-klustertypen i Azure HDInsight kan du använda HDFS, YARN-resurshantering och en enkel MapReduce-programmeringsmodell för att bearbeta och analysera batchdata parallellt.

Mer information om tillgängliga stackkomponenter med Hadoop-teknik i HDInsight finns i [Komponenter och versioner som är tillgängliga med HDInsight](../hdinsight-component-versioning.md). Mer information om Hadoop i HDInsight finns på [sidan om Azure-funktioner för HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Vad är MapReduce

Apache Hadoop MapReduce är ett program ramverk för att skriva jobb som behandlar stora mängder data. Indata delas upp i oberoende segment. Varje segment bearbetas parallellt över noderna i klustret. Ett MapReduce-jobb består av två funktioner:

* **Mapper**: Förbrukar indata, analyserar dem (vanligtvis med filter- och sorteringsåtgärder) och avger tupplar (nyckel-värde-par)

* **Reducer**: Förbrukar tupplar som avges av Mapper och utför en sammanfattningsåtgärd som skapar ett mindre, kombinerat resultat från Mapper-data

Ett grundläggande ordräkning MapReduce-jobbexempel illustreras i följande diagram:

 ![Hdi. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Utdata för det här jobbet är ett antal gånger varje ord har inträffat i texten.

* Mappern tar varje rad från indatatexten som en indata och bryter den till ord. Det avger en nyckel / värde par varje gång ett ord inträffar av ordet följs av en 1. Utdata sorteras innan den skickas till reducer.
* Reducersummar dessa enskilda räknas för varje ord och avger ett enda nyckel-/värdepar som innehåller ordet följt av summan av dess förekomster.

MapReduce kan implementeras på olika språk. Java är den vanligaste implementeringen och används för demonstrationsändamål i det här dokumentet.

## <a name="development-languages"></a>Utvecklingsspråk

Språk eller ramverk som baseras på Java och Java Virtual Machine kan kördes direkt som ett MapReduce-jobb. Exemplet som används i det här dokumentet är ett Java MapReduce-program. Icke-Java-språk, till exempel C#, Python eller fristående körbara filer, måste använda **Hadoop-direktuppspelning**.

Hadoop streaming kommunicerar med mapper och reducer över STDIN och STDOUT. Mapper och reducer läsa data en rad i taget från STDIN, och skriva utdata till STDOUT. Varje rad som läss eller avges av mappern och reduceraren måste vara i formatet för ett nyckel-/värdepar, avgränsade med ett fliktecken:

    [key]/t[value]

Mer information finns i [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Exempel på hur du använder Hadoop-direktuppspelning med HDInsight finns i följande dokument:

* [Utveckla C# MapReduce jobb](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa Apache Hadoop-kluster i HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
