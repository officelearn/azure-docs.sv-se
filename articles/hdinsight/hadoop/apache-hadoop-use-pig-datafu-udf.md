---
title: Använda Apache DataFu med Apache Pig i HDInsight - Azure
description: Apache DataFu Pig är en samling av bibliotek för användning med Apache Pig på Apache Hadoop. Lär dig hur du kan använda DataFu med Pig i HDInsight-klustret.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/16/2018
ms.author: hrasheed
ms.openlocfilehash: 59065c1eefedc2dce9eb56394c4c7db862744366
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691836"
---
# <a name="use-apache-datafu-pig-with-apache-pig-on-hdinsight"></a>Använda Apache DataFu Pig med Apache Pig i HDInsight

Lär dig hur du använder Apache DataFu Pig med HDInsight.

Apache DataFu Pig är en samling av bibliotek med öppen källkod för användning med Apache Pig på Apache Hadoop.
Läs mer på DataFu Pig [ https://datafu.apache.org/ ](https://datafu.apache.org/).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration.

* Ett Azure HDInsight-kluster (Linux eller Windows-baserad)

  > [!IMPORTANT]  
  > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En grundläggande kunskaper om [med Apache Pig på HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Installera DataFu på Linux-baserat HDInsight

> [!IMPORTANT]  
> DataFu är installerad på Linux-baserade kluster version 3.3 och högre och på Windows-baserade kluster. Det är inte installerat på Linux-baserade kluster tidigare än 3.3.
>
> Om du använder ett Windows-baserat kluster eller ett Linux-baserade kluster som är högre än version 3.3 kan du hoppa över det här avsnittet.

DataFu kan hämtas och installeras från Maven-centrallagret. Använd följande steg för att hitta versionen som du behöver och lägga till den i ditt HDInsight-kluster:

> [!WARNING]  
> DataFu versioner kan ha krav som inte uppfylls av HDInsight. Om du använder en äldre version av DataFu, kan det kräva en annan version av Pig än vad som ingår i HDInsight.

### <a name="find-a-version"></a>Hitta en version

1. I din webbläsare, navigerar du till https://mvnrepository.com/artifact/org.apache.datafu/datafu-pig och se vilken version som du behöver.

2. Välj det länkade versionsnumret.

3. Välj __visa alla__ att visa alla filer.

4. Hitta .jar-filen i listan över filer. Den här filen är vanligtvis det största visas, eftersom det innehåller alla beroenden. Högerklicka på länken och Kopiera länkadress.

### <a name="download-datafu-to-hdinsight"></a>Ladda ned DataFu till HDInsight

1. Anslut till din Linux-baserade HDInsight-kluster med SSH. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando för att hämta DataFu jar-filen med hjälp av wget-verktyget:

    > [!IMPORTANT]  
    > Ersätt länken i kommandot med URL: en som du kopierade tidigare.

    ```
    wget https://central.maven.org/maven2/org/apache/datafu/datafu-pig/1.4.0/datafu-pig-1.4.0.jar
    ```

3. Sedan överföra filen till standardlagringen för HDInsight-kluster. Placera filen i gör storage dem tillgängliga för alla noder i klustret.

    > [!IMPORTANT]  
    > Ersätt versionsnumret i namnet på filen med den version som du laddade ned.

    ```
    hdfs dfs -put datafu-pig-1.4.0.jar /example/jars
    ```

    > [!NOTE]  
    > Föregående kommando lagrar jar i `/example/jars` eftersom den här katalogen finns redan i klusterlagringen. Du kan använda valfri plats som du vill på HDInsight klusterlagringen.

## <a name="use-datafu-with-pig"></a>Använda DataFu med Pig

I det här avsnittet förutsätter vi att du är bekant med Pig på HDInsight. Mer information om hur du använder Pig med HDInsight finns i [använda Pig med HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]  
> Om du har installerat DataFu med hjälp av stegen i föregående avsnitt manuellt måste du registrera den innan du använder den.
>
> * Om klustret använder Azure Storage, använda en `wasb://` sökväg. Till exempel `register wasb:///example/jars/datafu-pig-1.4.0.jar`.
>
> * Om klustret använder Azure Data Lake Store Gen2, använda en `abfs://` sökväg. Till exempel `register abfs://home/example/jars/datafu-pig-1.4.0.jar`.
>
> * Om klustret använder Azure Data Lake Store Gen1, använda en `adl://` sökväg. Till exempel `register adl://home/example/jars/datafu-pig-1.4.0.jar`.

Ofta definierar du ett alias för DataFu funktioner. Följande exempel definierar ett alias för `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Du kan sedan använda detta alias i ett Pig Latin-skript för att generera ett hash-värde för indata. Följande kod ersätter till exempel plats i indatan med ett hash-värde:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Den genererar följande utdata:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Nästa steg

Mer information om DataFu eller Pig finns i följande dokument:

* [Apache DataFu Pig har börjat](https://datafu.apache.org/docs/datafu/getting-started.html).
* [Använda Apache Pig med HDInsight](hdinsight-use-pig.md)
