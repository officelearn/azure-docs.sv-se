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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271823"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Vad är Apache HBase i Azure HDInsight

[Apache HBase](https://hbase.apache.org/) är en NoSQL-databas med öppen källkod som bygger på [Apache Hadoop](https://hadoop.apache.org/) och modelleras efter [Google BigTable](https://cloud.google.com/bigtable/). HBase ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och halvstrukturerade data i en schemalös databas sorterad per kolumnfamiljer.

HBase liknar en databas från användar perspektiv. Data lagras i rader och kolumner i en tabell och data i en rad grupperas efter kolumn serie. HBase är en schemalös databas i den mening att varken kolumner eller den typ av data som lagras i dem måste definieras innan du använder dem. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. Den kan utgå ifrån dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-miljön.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hur implementeras Apache HBase i Azure HDInsight?

HDInsight HBase erbjuds som ett hanterat kluster som är integrerat i Azure-miljön. Klustren har kon figurer ATS för att lagra data direkt i [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), vilket ger låg latens och ökad elastiskhet i alternativ för prestanda och kostnad. Det gör att kunderna kan bygga interaktiva webbplatser som fungerar med stora datauppsättningar och skapa tjänster som lagrar sensor- och telemetridata från miljontals slutpunkter och analysera dessa data med Hadoop-jobb. HBase och Hadoop är bra startpunkter för stordataprojekt i Azure som gör att realtidsprogram kan arbeta med stora datauppsättningar.

HDInsight-implementeringen utnyttjar HBase skalbara arkitektur för att tillhandahålla automatisk delning av tabeller, stor konsekvens för läsning och skrivning och automatisk redundans. Prestanda utökas av cachelagring i minnet för läsning och snabb strömning för skrivning. HBase-kluster kan skapas i virtuella nätverk. Mer information finns i [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hur hanteras data i HDInsight HBase?

Data kan hanteras i HBase med hjälp av kommandona `create`, `get`, `put` och `scan` i HBase-gränssnittet. Data skrivs till databasen med hjälp av `put` och läses med hjälp av `get`. Kommandot `scan` används till att hämta data från flera rader i en tabell. Data kan också hanteras med HBase C#-API. Det ger ett klientbibliotek utöver HBase REST-API. En HBase-databas kan också frågas genom att använda [Apache Hive](https://hive.apache.org/). En introduktion till dessa programmerings modeller finns i [komma igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md). Samprocessorer är också tillgängliga, vilket gör det möjligt att bearbeta data i noderna som är värdar för databasen.

> [!NOTE]  
> Thrift stöds inte av HBase i HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Användnings fall för Apache HBase

Det kanoniska användnings fallet för vilken BigTable (och efter tillägg, HBase) skapades från Webbs ökningen. Sökmotorer skapar index som mappar termer till de webbsidor som innehåller dem. Men HBase passar också för många andra användningsområden och du hittar många av dem i det här avsnittet.

|Scenario |Beskrivning |
|---|---|
|Nyckelvärdeslagring|HBase kan användas som nyckel värdes lager och är lämpligt för att hantera meddelande system. Facebook använder HBase för sina meddelande system och är perfekt för att lagra och hantera Internet-kommunikation. WebTable använder HBase till att söka efter och hantera tabeller som extraheras från webbsidor.|
|Sensordata|HBase är användbart för insamling av data som samlas in inkrementellt från olika källor. Detta inkluderar sociala analyser, tids serier, som håller interaktiva instrument paneler uppdaterade med trender och räknare och hanterar Gransknings logg system. Några exempel är Bloomberg trader terminal och Open Time Series Database (OpenTSDB), som lagrar och ger åtkomst till mätvärden som samlats in om hälsostatus för serversystem.|
|Realtidsfråga|[Apache Phoenix](https://phoenix.apache.org/) är en SQL-frågemotor för Apache HBase. Den används som en JDBC-drivrutin, och den gör det möjligt att fråga och hantera HBase-tabeller med hjälp av SQL.|
|HBase som en plattform|Program kan köras ovanpå HBase genom att använda det som ett datalager. Exempel är Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji och Titan. Program kan också integreras med HBase. Exempel på detta är [Apache Hive](https://hive.apache.org/), [Apache gris](https://pig.apache.org/), [solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache FLUME](https://flume.apache.org/), [Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [ganglia](http://ganglia.info/)och [Apache-granskning](https://drill.apache.org/).|

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network)
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md)
