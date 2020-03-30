---
title: Vad är Apache HBase i Azure HDInsight?
description: En introduktion till Apache HBase i HDInsight en NoSQL-databas som bygger på Hadoop. Läs mer om användningsfall och jämför HBase med andra Hadoop-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271823"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Vad är Apache HBase i Azure HDInsight

[Apache HBase](https://hbase.apache.org/) är en öppen källkod, NoSQL databas som bygger på [Apache Hadoop](https://hadoop.apache.org/) och modelleras efter [Google BigTable](https://cloud.google.com/bigtable/). HBase ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och halvstrukturerade data i en schemalös databas sorterad per kolumnfamiljer.

Ur användarperspektiv liknar HBase en databas. Data lagras i rader och kolumner i en tabell och data inom en rad grupperas efter kolumnfamilj. HBase är en schemalös databas i den mening att varken kolumner eller den typ av data som lagras i dem måste definieras innan du använder dem. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. Den kan utgå ifrån dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-miljön.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hur implementeras Apache HBase i Azure HDInsight?

HDInsight HBase erbjuds som ett hanterat kluster som är integrerat i Azure-miljön. Klustren är konfigurerade för att lagra data direkt i [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), vilket ger låg svarstid och ökad elasticitet i prestanda och kostnadsval. Det gör att kunderna kan bygga interaktiva webbplatser som fungerar med stora datauppsättningar och skapa tjänster som lagrar sensor- och telemetridata från miljontals slutpunkter och analysera dessa data med Hadoop-jobb. HBase och Hadoop är bra startpunkter för stordataprojekt i Azure som gör att realtidsprogram kan arbeta med stora datauppsättningar.

HDInsight-implementeringen utnyttjar HBase skalbara arkitektur för att tillhandahålla automatisk delning av tabeller, stor konsekvens för läsning och skrivning och automatisk redundans. Prestanda utökas av cachelagring i minnet för läsning och snabb strömning för skrivning. HBase-kluster kan skapas i virtuella nätverk. Mer information finns i [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hur hanteras data i HDInsight HBase?

Data kan hanteras i HBase med hjälp av kommandona `create`, `get`, `put` och `scan` i HBase-gränssnittet. Data skrivs till databasen med hjälp av `put` och läses med hjälp av `get`. Kommandot `scan` används till att hämta data från flera rader i en tabell. Data kan också hanteras med HBase C#-API. Det ger ett klientbibliotek utöver HBase REST-API. En HBase-databas kan också efterfrågas med hjälp av [Apache Hive](https://hive.apache.org/). En introduktion till dessa programmeringsmodeller finns i [Komma igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md). Samprocessorer är också tillgängliga, vilket gör att databearbetning i noderna som är värd för databasen.

> [!NOTE]  
> Thrift stöds inte av HBase i HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Användningsfall för Apache HBase

Det kanoniska användningsfallet som BigTable (och i förlängningen HBase) skapades från webbsökning. Sökmotorer skapar index som mappar termer till de webbsidor som innehåller dem. Men HBase passar också för många andra användningsområden och du hittar många av dem i det här avsnittet.

|Scenario |Beskrivning |
|---|---|
|Nyckelvärdeslagring|HBase kan användas som en nyckelvärdesbutik och den är lämplig för hantering av meddelandesystem. Facebook använder HBase för sitt meddelandesystem, och det är idealiskt för att lagra och hantera Internet-kommunikation. WebTable använder HBase till att söka efter och hantera tabeller som extraheras från webbsidor.|
|Sensordata|HBase är användbart för insamling av data som samlas in inkrementellt från olika källor. Detta inkluderar sociala analyser, tidsserier, att hålla interaktiva instrumentpaneler uppdaterade med trender och räknare och hantera granskningsloggsystem. Några exempel är Bloomberg trader terminal och Open Time Series Database (OpenTSDB), som lagrar och ger åtkomst till mätvärden som samlats in om hälsostatus för serversystem.|
|Realtidsfråga|[Apache Phoenix](https://phoenix.apache.org/) är en SQL-frågemotor för Apache HBase. Den används som en JDBC-drivrutin och möjliggör frågor och hantering av HBase-tabeller med hjälp av SQL.|
|HBase som en plattform|Program kan köras ovanpå HBase genom att använda det som ett datalager. Exempel är Phoenix, [OpenTSDB,](http://opentsdb.net/)Kiji och Titan. Program kan också integreras med HBase. Exempel är [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), Apache [Flume](https://flume.apache.org/), [Apache Impala](https://impala.apache.org/), Apache [Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/)och [Apache Drill](https://drill.apache.org/).|

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network)
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md)
