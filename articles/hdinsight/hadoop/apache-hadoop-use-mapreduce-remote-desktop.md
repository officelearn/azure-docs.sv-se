---
title: "MapReduce och Fjärrskrivbord med Hadoop i HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du använder Fjärrskrivbord för att ansluta till Hadoop i HDInsight och kör MapReduce-jobb."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: e0dd2c0c0eeeedda00d73f697897582a48d0fc04
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Använda MapReduce i Hadoop i HDInsight med fjärrskrivbord
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

I den här artikeln får lära du dig att ansluta till en Hadoop på HDInsight-kluster med hjälp av fjärrskrivbord och kör sedan MapReduce-jobb med hjälp av kommandot Hadoop.

> [!IMPORTANT]
> Fjärrskrivbord är bara tillgänglig på Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> För HDInsight 3.4 eller större finns [använda MapReduce med SSH](apache-hadoop-use-mapreduce-ssh.md) för information om hur du ansluter till HDInsight-kluster och kör MapReduce-jobb.

## <a id="prereq"></a>Förhandskrav
Du behöver följande för att slutföra stegen i den här artikeln:

* Ett kluster med Windows-baserade HDInsight (Hadoop på HDInsight)
* En klientdator som kör Windows 10, Windows 8 eller Windows 7

## <a id="connect"></a>Ansluta med fjärrskrivbord
Aktivera Fjärrskrivbord för HDInsight-klustret och sedan ansluta till den genom att följa anvisningarna i [Anslut till HDInsight-kluster med RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>Använd kommandot Hadoop
När du är ansluten till skrivbordet för HDInsight-kluster kan du använda följande steg för att köra ett MapReduce-jobb med hjälp av Hadoop-kommando:

1. HDInsight-skrivbordet och starta den **Hadoop kommandoraden**. Då öppnas en ny kommandotolk i den **c:\apps\dist\hadoop-&lt;versionsnummer >** directory.

   > [!NOTE]
   > Versionsnumret ändras när Hadoop uppdateras. Den **HADOOP_HOME** miljövariabeln kan användas för att hitta sökvägen. Till exempel `cd %HADOOP_HOME%` ändrar kataloger till Hadoop-katalogen utan att du behöver veta versionsnumret.
   >
   >
2. Att använda den **Hadoop** kommando för att köra ett exempel MapReduce-jobb, använder du följande kommando:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Detta startar den **wordcount** -klassen, som ingår i den **hadoop-mapreduce-examples.jar** fil i den aktuella katalogen. Som indata, används den **wasb://example/data/gutenberg/davinci.txt** dokument och utdata är lagrade på: **wasb: / / / exempel/data/WordCountOutput**.

   > [!NOTE]
   > Läs mer om MapReduce-jobb och exempeldata <a href="hdinsight-use-mapreduce.md">använda MapReduce i HDInsight Hadoop</a>.
   >
   >
3. Jobbet sänder ut information när den bearbetas och returnerar informationen liknar när jobbet har slutförts:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. När jobbet är klart, använder du följande kommando för att visa utdatafiler som lagras på **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    Visas två filer, **_SUCCESS** och **del-r-00000**. Den **del-r-00000** filen innehåller utdata för jobbet.

   > [!NOTE]
   > Vissa MapReduce-jobb kan delas upp resultaten över flera **del-r-###** filer. I så fall använder den ### suffix för att ange ordningen för filerna.
   >
   >
5. Om du vill visa utdata, använder du följande kommando:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Detta visar en lista över de ord som finns i den **wasb://example/data/gutenberg/davinci.txt** fil och hur många gånger varje ord inträffade. Följande är ett exempel på de data som ska ingå i filen:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Sammanfattning
Som du ser ger kommandot Hadoop ett enkelt sätt att köra MapReduce-jobb på ett HDInsight-kluster och sedan visa jobbutdata.

## <a id="nextsteps"></a>Nästa steg
Allmän information om MapReduce-jobb i HDInsight:

* [Använda MapReduce på Hadoop och HDInsight](hdinsight-use-mapreduce.md)

Information om andra sätt kan du arbeta med Hadoop i HDInsight:

* [Använda Hive med Hadoop i HDInsight](hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
