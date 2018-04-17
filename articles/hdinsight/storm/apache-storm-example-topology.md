---
title: Exempel Apache Storm-topologier på HDInsight | Microsoft Docs
description: En lista över exempel på Storm-topologier skapas och testas med Apache Storm på HDInsight inklusive grundläggande C# och Java-topologier och arbetar med Händelsehubbar.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: f633797147b2090680ebb5ea11f60271addc7f52
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exempel på Storm-topologier och komponenter för Apache Storm på HDInsight

Följande är en lista över exempel skapas och hanteras av Microsoft för användning med Apache Storm på HDInsight. De här exemplen omfattar en mängd ämnen, från att skapa grundläggande C# och Java-topologier att arbeta med Azure-tjänster, till exempel Händelsehubbar, Cosmos DB, SQL-databas, HBase på HDInsight och Azure Storage. Några exempel visas också hur du arbetar med Azure- eller även icke-Microsoft-teknik som SignalR och Socket.IO.

| Beskrivning | Visar | Språk/Framework |
|:--- |:--- |:--- |
| [Skriva till Azure Data Lake Store från Apache Storm](apache-storm-write-data-lake-store.md) |Skrivning till Azure Data Lake Store |Java |
| [Event Hub-kanalen och bult källa](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Källa för Event Hub kanal och bult |Java |
| [Utveckla Java-baserad topologier för Apache Storm på HDInsight][5797064f] |Maven |Java |
| [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio][16fce2d1] |HDInsight Tools för Visual Studio |C#, Java |
| [Bearbeta händelser från Azure Event Hubs med Storm på HDInsight (C#)][844d1d81] |Händelsehubbar |C# och Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) (Bearbeta händelser från Azure Event Hubs med Storm i HDInsight (Java)) |Händelsehubbar |Java |
| [Bearbeta vehicle sensordata från Händelsehubbar med Storm på HDInsight][246ee964] |Event Hubs Cosmos DB Azure Storage Blob (WASB) |C#, Java |
| [Extrahering, transformering och inläsning (ETL) från Azure Event Hubs till HBase, med Storm på HDInsight][b4b68194] |Händelsehubbar, HBase |C# |
| [Mallprojekt C# Storm-topologi för att arbeta med Azure-tjänster från Storm på HDInsight][ce0c02a2] |Händelsen NAV, Cosmos DB SQL-databas, HBase, SignalR |C#, Java |
| [Skalbarhet prestandamått för att läsa från Azure Event Hubs med Storm på HDInsight][d6c540e3] |Genomströmningen, Event Hubs SQL-databas |C#, Java |
| [Använda Python med Storm på HDInsight](apache-storm-develop-python-topology.md) |Python-komponenter med en topologi som |Python |
| [Använda Kafka med Storm på HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm läsning och skrivning till Apache Kafka | Java |

### <a name="next-steps"></a>Nästa steg

* [Komma igång med Apache Storm i HDInsight][2b8c3488]
* [Lär dig att distribuera och hantera Storm-topologier med Storm på HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Lär dig hur du skapar ett Storm på HDInsight-kluster och använda Storm-instrumentpanelen för att distribuera exempeltopologier."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Lär dig mer om att distribuera och hantera topologier med hjälp av instrumentpanelen webbaserad Storm och Storm-Användargränssnittet eller HDInsight Tools för Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Lär dig hur du skapar C# Storm-topologier med HDInsight Tools för Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Lär dig mer om att skapa Storm-topologier i Java, med Maven, genom att skapa en grundläggande wordcount-topologi."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Lär dig hur du läser och skriver data från Azure Event Hubs med Storm på HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Lär dig hur du använder en Storm-topologi att läsa meddelanden från Azure Event Hubs, läsa dokument från Azure Cosmos DB för refererar till data och sparar data till Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Flera topologier för att demonstrera dataflöde vid läsning från Azure Event Hubs och lagring till SQL-databas med Apache Storm på HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Lär dig mer om att läsa data från Azure Event Hubs, sammanställd & Transformera data och sedan lagra den HBase på HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Projektet innehåller mallar för kanaler, bultar och topologier för att interagera med olika Azure-tjänster som Händelsehubbar, Cosmos-DB och SQL-databas."

