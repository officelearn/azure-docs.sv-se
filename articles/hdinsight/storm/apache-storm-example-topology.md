---
title: Exempel apachestormtopologier i Azure HDInsight
description: En lista över exempel stormtopologier som skapats och testats med Apache Storm på HDInsight, inklusive grundläggande C# och Java-topologier, och arbetar med Event Hubs.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530671"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exempel Apache Storm topologier och komponenter för Apache Storm på HDInsight

Följande är en lista över exempel som skapats och underhålls av Microsoft för användning med [Apache Storm](https://storm.apache.org/) på HDInsight. De här exemplen omfattar en mängd olika avsnitt, från att skapa grundläggande C# och Java-topologier till att arbeta med Azure-tjänster som Event Hubs, Cosmos DB, SQL Database, [Apache HBase](https://hbase.apache.org/) på HDInsight och Azure Storage. Några exempel visar också hur du arbetar med icke-Azure, eller till och med icke-Microsoft-teknik, till exempel SignalR och Socket.IO.

| Beskrivning | Demonstrationer | Språk/ramverk |
|:--- |:--- |:--- |
| [Skriv till Azure Data Lake Storage från Apache Storm](apache-storm-write-data-lake-store.md) |Skriva till Azure Data Lake Storage |Java |
| [Källa Event Hub Spout och Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Källa för Event Hub Spout och Bolt |Java |
| [Utveckla Java-baserade topologier för Apache Storm på HDInsight][5797064f] |Maven |Java |
| [Utveckla C# topologier för Apache Storm på HDInsight med Visual Studio][16fce2d1] |HDInsight-verktyg för Visual Studio |C#, Java |
| [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)][844d1d81] |Händelsehubbar |C# och Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Bearbeta händelser från Azure Event Hubs med Storm i HDInsight (Java)) |Händelsehubbar |Java |
| [Bearbeta fordonssensordata från eventhubbar med Apache Storm på HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Extrahera, transformera och läsa in (ETL) från Azure Event Hubs till Apache HBase med Apache Storm på HDInsight][b4b68194] |Händelsehubbar, HBase |C# |
| [Mall C# Storm-topologiprojekt för att arbeta med Azure-tjänster från Apache Storm på HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase, SignalR |C#, Java |
| [Skalbarhetsriktmärken för läsning från Azure Event Hubs med Apache Storm på HDInsight][d6c540e3] |Meddelandedataflöde, händelsehubbar, SQL-databas |C#, Java |
| [Använd Apache Kafka med Apache Storm på HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm läsa och skriva till Apache Kafka | Java |

> [!WARNING]  
> C#-exemplen i den här listan skapades och testades ursprungligen med Windows-baserade HDInsight och kanske inte fungerar korrekt med Linux-baserade HDInsight-kluster. Linux-baserade kluster använder Mono för att köra .NET-kod och kan ha kompatibilitetsproblem med ramverk och paket som används i exemplet.
>
> Linux är det enda operativsystem som används på HDInsight version 3.4 eller senare.

## <a name="python-only"></a>Endast Python

Se [Använda Python med Apache Storm på HDInsight](apache-storm-develop-python-topology.md) för ett exempel på Python-komponenter med fluxtopologi.

## <a name="next-steps"></a>Efterföljande moment

* [Skapa och övervaka en Apache Storm-topologi i Azure HDInsight](./apache-storm-quickstart.md)
* [Lär dig hur du distribuerar och hanterar Apache Storm-topologier med Apache Storm på HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Lär dig hur du distribuerar och hanterar topologier med hjälp av det webbaserade Apache Storm Dashboard och Storm UI eller HDInsight Tools för Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Lär dig hur du skapar C# Storm-topologier med hjälp av HDInsight Tools för Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Lär dig hur du skapar Storm-topologier i Java, med Maven, genom att skapa en grundläggande wordcount-topologi."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Lär dig hur du läser och skriver data från Azure Event Hubs med Storm på HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Lär dig hur du använder en Storm-topologi för att läsa meddelanden från Azure Event Hubs, läsa dokument från Azure Cosmos DB för datareferenser och spara data till Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Flera topologier för att demonstrera dataflöde när du läser från Azure Event Hubs och lagra till SQL Database med Apache Storm på HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Lär dig hur du läser data från Azure Event Hubs, aggregerade & omvandlar data och lagrar dem sedan i HBase på HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Det här projektet innehåller mallar för pipar, bultar och topologier för att interagera med olika Azure-tjänster som Event Hubs, Cosmos DB och SQL Database."
