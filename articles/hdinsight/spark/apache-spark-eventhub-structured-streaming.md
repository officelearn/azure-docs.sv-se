---
title: "Använda Apache Spark strukturerad strömning med Händelsehubbar i Azure HDInsight | Microsoft Docs"
description: "Skapa ett Apache Spark streaming exempel på hur du skickar en dataström till Azure-Händelsehubb och sedan ta emot händelser i HDInsight Spark-kluster med ett scala-program."
keywords: Apache spark streaming, spark streaming, spark-exemplet, apache spark streaming exempel event hub azure exempel, spark-exempel
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: a542295e91a641289fa4261920a08eddbad6a217
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Apache Spark strukturerade strömning på HDInsight för att bearbeta händelser från Event Hubs

I den här artikeln får du lära dig att bearbeta realtid telemetri med Spark strukturerade strömning. För att åstadkomma detta utför du följande anvisningar:

1. Kompilera och köra på din lokala arbetsstationen ett exempelprogram händelse producenten som genererar händelser som du skickar till Händelsehubbar.
2. Använd den [Spark Shell](apache-spark-shell.md) att definiera och kör ett enkelt program Spark strukturerade strömning.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Ett namnområde för Händelsehubbar i Azure. Se [skapa ett namnområde för Azure Event Hubs](apache-spark-eventhub-streaming.md#create-an-azure-event-hub) för mer information.

* Oracle Java Development kit. Du kan installera den från [här](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Apache Maven. Du kan ladda ned det från [här](https://maven.apache.org/download.cgi). Anvisningar för att installera Maven finns [här](https://maven.apache.org/install.html).

## <a name="build-configure-and-run-the-event-producer"></a>Skapa, konfigurera och köra producenten händelse
I den här uppgiften kan du klona ett exempelprogram som skapar slumpmässiga händelser och skickar dem till en konfigurerad Händelsehubb. Det här exempelprogrammet är tillgänglig i GitHub på [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer).

1. Kontrollera att du har git-verktygen som installeras. Du kan hämta från [GIT hämtar](http://www.git-scm.com/downloads). 
2. Öppna en kommandotolk eller terminal shell och kör följande kommando från katalogen för ditt val att klona projektet.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. Detta skapar en ny mapp med namnet eventhubs-klient-sample. Navigera till den här mappen i gränssnittet eller Kommandotolken.
4. Kör Maven för att skapa programmet med hjälp av följande kommando:

          mvn package

5. I gränssnittet eller Kommandotolken, navigera till målkatalogen som skapas och innehåller filen ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar``.
6. Därefter måste du bygga upp kommandoraden för att köra händelsen producenten mot din Event Hub. Gör detta genom att ersätta värdena i kommandot på följande sätt:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. Fullständiga kommandot skulle till exempel se ut ungefär så här:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. Kommandot startas och om konfigurationen är korrekt om en stund visas utdata som rör de händelser som skickas till din Event Hub liknar följande:

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. Lämna händelse producenten körs medan du fortsätter med instruktionerna.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>Kör Spark-gränssnittet på ditt HDInsight-kluster
I det här steget kommer du att SSH till ditt HDInsight-kluster huvudnod, starta Spark Shell och kör ett Spark Streaming program som hämtar och bearbetar händelser från Event Hubs. 

Ditt HDInsight-kluster ska vara klar med den här punkten. Om inte, måste du vänta tills etableringen har slutförts. När det är klart, fortsätter du med följande steg:

1. Anslut till HDInsight-klustret via SSH. Instruktioner finns i [Anslut till HDInsight med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

5. Programmet som du skapar kräver Spark Streaming Händelsehubbar-paketet. Att köra Spark Shell så att den hämtar automatiskt den här beroende från [Maven centrala](https://search.maven.org), måste du se till leverans paketen växel med Maven koordinaterna på följande sätt:

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.0"

6. När Spark-gränssnittet är klar läser in, bör du se:

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.0.2.6.0.10-29
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_131)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. Kopiera följande kodfragment i en textredigerare och ändra den så att den har nyckeln för principen och Namespace inställt som passar din Event Hub.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )

8. Klistra in den ändrade fragment i väntar scala > Kommandotolken och tryck på RETUR. Du bör se utdata som liknar följande:

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

9. Därefter du börjar skriva en Spark strukturerade strömning frågan att ange källa. Klistra in följande i Spark-gränssnittet och tryck på RETUR.

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

10. Du bör se utdata som liknar följande:

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

11. Sedan redigera frågan så att den skrivs utdata till konsolen. Gör detta genom att klistra in följande i Spark-gränssnittet och trycka på RETUR.

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

12. Du bör se vissa grupper som börjar med utdata som liknar följande

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

13. Detta följs av resultatet med bearbetning av varje microbatch av händelser. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

14. När nya händelser anländer från händelsen producenten, bearbetas de av den här frågan strukturerade strömning.
15. Ta bort ditt HDInsight-kluster när du är klar med det här exemplet.



## <a name="see-also"></a>Se även

* [Översikt över Spark streaming](apache-spark-streaming-overview.md)













