---
title: Vad är HBase i Azure HDInsight?
description: En introduktion till Apache HBase i HDInsight en NoSQL-databas som bygger på Hadoop. Läs mer om användningsfall och jämför HBase med andra Hadoop-kluster.
keywords: bigtable,nosql,what is hbase,apache hbase,hbase,habase overview,
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 8bcdccc0a8cdb50d2c587513b887f6dfdb95c13f
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287295"
---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Vad är HBase i HDInsight: En NoSQL-databas som tillhandahåller BigTable-liknande kapacitet för Hadoop
Apache HBase är en NoSQL-databas med öppen källkod som har skapats på Hadoop och modellerats efter Google BigTable. HBase ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och halvstrukturerade data i en schemalös databas sorterad per kolumnfamiljer.

HBase är liknar en databas ur användarens perspektiv. Data lagras i rader och kolumner i en tabell och data i en rad grupperas per kolumnfamilj. HBase är en schemalös databas i den mening att varken kolumner eller den typ av data som lagras i dem måste definieras innan du använder dem. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. Den kan utgå ifrån dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-miljön.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Hur är HBase implementerat i Azure HDInsight?

HDInsight HBase erbjuds som ett hanterat kluster som är integrerat i Azure-miljön. Klustren har konfigurerats för att lagra data direkt i [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) som ger mindre fördröjning och ökad flexibilitet när det gäller alternativ för prestanda och kostnader. Det gör att kunderna kan bygga interaktiva webbplatser som fungerar med stora datauppsättningar och skapa tjänster som lagrar sensor- och telemetridata från miljontals slutpunkter och analysera dessa data med Hadoop-jobb. HBase och Hadoop är bra startpunkter för stordataprojekt i Azure som gör att realtidsprogram kan arbeta med stora datauppsättningar.

HDInsight-implementeringen utnyttjar HBase skalbara arkitektur för att tillhandahålla automatisk delning av tabeller, stor konsekvens för läsning och skrivning och automatisk redundans. Prestanda utökas av cachelagring i minnet för läsning och snabb strömning för skrivning. HBase-kluster kan skapas i virtuella nätverk. Mer information finns i [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hur hanteras data i HDInsight HBase?
Data kan hanteras i HBase med hjälp av kommandona `create`, `get`, `put` och `scan` i HBase-gränssnittet. Data skrivs till databasen med hjälp av `put` och läses med hjälp av `get`. Kommandot `scan` används till att hämta data från flera rader i en tabell. Data kan också hanteras med HBase C#-API. Det ger ett klientbibliotek utöver HBase REST-API. En HBase-databas kan också efterfrågas med hjälp av Hive. En introduktion till dessa programmeringsmodeller finns i [Komma igång med Hadoop i HDInsight HBase](./apache-hbase-tutorial-get-started-linux.md). Det finns också coprocessorer för databehandling i de noder som är värdar för databasen.

> [!NOTE]
> Thrift stöds inte av HBase i HDInsight.
>

## <a name="scenarios-use-cases-for-hbase"></a>Scenarier: Användningsfall för HBase
Canonical användningsfall för BigTable (och HBase via tillägg) skapades från webbsökning. Sökmotorer skapar index som mappar termer till de webbsidor som innehåller dem. Men HBase passar också för många andra användningsområden och du hittar många av dem i det här avsnittet.

* Nyckelvärdeslagring
  
    HBase kan användas som ett nyckelvärdeslager och passar för hantering av meddelandesystem. HBase är perfekt för lagring och hantering av internetkommunikation och används till exempel av Facebook för meddelandesystemen. WebTable använder HBase till att söka efter och hantera tabeller som extraheras från webbsidor.
* Sensordata
  
    HBase är användbart för insamling av data som samlas in inkrementellt från olika källor. Det omfattar sociala analyser, tidsserier, hålla interaktiva instrumentpaneler uppdaterade med trender och räknare och hantera system för granskningsloggar. Några exempel är Bloomberg trader terminal och Open Time Series Database (OpenTSDB), som lagrar och ger åtkomst till mätvärden som samlats in om hälsostatus för serversystem.
* Realtidsfråga
  
    [Phoenix](http://phoenix.apache.org/) är en SQL-frågemotor för Apache HBase. Den används som en JDBC-drivrutin, och gör att du kan ställa frågor och hantera HBase-tabeller med SQL.
* HBase som en plattform
  
    Program kan köras ovanpå HBase genom att använda det som ett datalager. Några exempel är Phoenix, OpenTSDB, Kiji och Titan. Program kan också integreras med HBase. Exemplen innefattar Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia och Drill.

## <a name="next-steps"></a>Nästa steg
* [Komma igång med HBase med Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Skapa HDInsight-kluster i Azure Virtual Network)
* [Konfigurera HBase-replikering i HDInsight](apache-hbase-replication.md)
* [Use Maven to build Java applications that use HBase with HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) (Använda Maven för att skapa Java-program som använder HBase med HDInsight (Hadoop))

## <a name="see-also"></a>Se även
* [Apache HBase](https://hbase.apache.org/)
* [Referensguiden för Apache HBase](https://hbase.apache.org/book.html)
* [Bigtable: Ett distribuerat Storage-system för strukturerade data](http://research.google.com/archive/bigtable.html)
* [Apache HBase/Phoenix – Tips, trick och bästa praxis i Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2016/08/28/hdinsight-hbase-faq/)




