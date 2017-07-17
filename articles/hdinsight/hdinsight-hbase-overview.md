---
title: "Vad är HBase i Azure HDInsight? | Microsoft Docs"
description: "En introduktion till Apache HBase i HDInsight en NoSQL-databas som bygger på Hadoop. Läs mer om användningsfall och jämför HBase med andra Hadoop-kluster."
keywords: bigtable,nosql,what is hbase,apache hbase,hbase,habase overview,
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: d2a76d53-133a-4849-a30c-88d9c794391c
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 61b4038c5878e4e4b92c4bbabc1d535031c78815
ms.contentlocale: sv-se
ms.lasthandoff: 05/12/2017


---
# Vad är HBase i HDInsight: En NoSQL-databas som tillhandahåller BigTable-liknande kapacitet för Hadoop
<a id="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop" class="xliff"></a>
Apache HBase är en NoSQL-databas med öppen källkod som har skapats på Hadoop och modellerats efter Google BigTable. HBase ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och halvstrukturerade data i en schemalös databas sorterad per kolumnfamiljer.

Data lagras i tabellens rader och data i raderna grupperas per kolumnfamilj. HBase är en schemalös databas i den mening att varken kolumner eller den typ av data som lagras i dem måste definieras innan du använder dem. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. Den kan utgå ifrån dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-miljön.

## Hur är HBase implementerat i Azure HDInsight?
<a id="how-is-hbase-implemented-in-azure-hdinsight" class="xliff"></a>
HDInsight HBase erbjuds som ett hanterat kluster som är integrerat i Azure-miljön. Klustren har konfigurerats för att lagra data direkt i Azure Storage. Det ger mindre fördröjning och större flexibilitet när det gäller alternativ för prestanda och kostnader. Det gör att kunderna kan bygga interaktiva webbplatser som fungerar med stora datauppsättningar och skapa tjänster som lagrar sensor- och telemetridata från miljontals slutpunkter och analysera dessa data med Hadoop-jobb. HBase och Hadoop är bra startpunkter för stordataprojekt i Azure som gör att realtidsprogram kan arbeta med stora datauppsättningar.

HDInsight-implementeringen utnyttjar HBase skalbara arkitektur för att tillhandahålla automatisk delning av tabeller, stor konsekvens för läsning och skrivning och automatisk redundans. Prestanda utökas av cachelagring i minnet för läsning och snabb strömning för skrivning. HBase-kluster kan skapas i virtuella nätverk. Mer information finns i [Create HDInsight clusters on Azure Virtual Network][hbase-provision-vnet] (Skapa HDInsight-kluster i Azure Virtual Network).

## Hur hanteras data i HDInsight HBase?
<a id="how-is-data-managed-in-hdinsight-hbase" class="xliff"></a>
Data kan hanteras i HBase med hjälp av kommandona `create`, `get`, `put` och `scan` i HBase-gränssnittet. Data skrivs till databasen med hjälp av `put` och läses med hjälp av `get`. Kommandot `scan` används till att hämta data från flera rader i en tabell. Data kan också hanteras med HBase C#-API. Det ger ett klientbibliotek utöver HBase REST-API. En HBase-databas kan också efterfrågas med hjälp av Hive. En introduktion till dessa programmeringsmodeller finns i [Komma igång med Hadoop i HDInsight HBase][hbase-get-started]. Det finns också coprocessorer för databehandling i de noder som är värdar för databasen.

>
> [!NOTE]
> Thrift stöds inte av HBase i HDInsight.
>

## Scenarier: Användningsfall för HBase
<a id="scenarios-use-cases-for-hbase" class="xliff"></a>
BigTable (och HBase via tillägg) skapades framförallt för webbsökning. Sökmotorer skapar index som mappar termer till de webbsidor som innehåller dem. Men HBase passar också för många andra användningsområden och du hittar många av dem i det här avsnittet.

* Nyckelvärdeslagring
  
    HBase kan användas som ett nyckelvärdeslager och passar för hantering av meddelandesystem. HBase är perfekt för lagring och hantering av internetkommunikation och används till exempel av Facebook för meddelandesystemen. WebTable använder HBase till att söka efter och hantera tabeller som extraheras från webbsidor.
* Sensordata
  
    HBase är användbart för insamling av data som samlas in inkrementellt från olika källor. Det omfattar sociala analyser, tidsserier, hålla interaktiva instrumentpaneler uppdaterade med trender och räknare och hantera system för granskningsloggar. Några exempel är Bloomberg trader terminal och Open Time Series Database (OpenTSDB), som lagrar och ger åtkomst till mätvärden som samlats in om hälsostatus för serversystem.
* Realtidsfråga
  
    [Phoenix](http://phoenix.apache.org/) är en SQL-frågemotor för Apache HBase. Den används som en JDBC-drivrutin, och gör att du kan ställa frågor och hantera HBase-tabeller med SQL.
* HBase som en plattform
  
    Program kan köras ovanpå HBase genom att använda det som ett datalager. Några exempel är Phoenix, OpenTSDB, Kiji och Titan. Program kan också integreras med HBase. Exemplen innefattar Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia och Drill.

## <a name="next-steps"></a>Nästa steg
* [Komma igång med HBase med Hadoop i HDInsight][hbase-get-started]
* [Create HDInsight clusters on Azure Virtual Network][hbase-provision-vnet] (Skapa HDInsight-kluster i Azure Virtual Network)
* [Konfigurera HBase-replikering i HDInsight](hdinsight-hbase-replication.md)
* [Analyze Twitter sentiment with HBase in HDInsight][hbase-twitter-sentiment] (Analysera Twitter-sentiment med HBase i HDInsight)
* [Use Maven to build Java applications that use HBase with HDInsight (Hadoop)][hbase-build-java-maven] (Använda Maven för att skapa Java-program som använder HBase med HDInsight (Hadoop))

## <a name="see-also"></a>Se även
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: Ett distribuerat Storage-system för strukturerade data](http://research.google.com/archive/bigtable.html)

[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/

