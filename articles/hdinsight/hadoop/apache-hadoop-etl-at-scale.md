---
title: Extrahera, transformering och laddning (ETL) i skala - Azure HDInsight | Microsoft Docs
description: "Lär dig hur ETL används i HDInsight med Hadoop."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 8b55bafee83dd43d535f9ebb0488134b5c7b3446
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrahering, transformering och laddning (ETL) i skala

Extrahering, transformering och laddning (ETL) är en process som data är har köpt från olika källor, som samlas in i en standardplats, rensade och bearbetas och slutligen läses in i ett datalager som den kan efterfrågas. Importera data äldre ETL-rutiner, rensa den på plats och lagra den på en relationsdata motor. Med HDInsight stöder en mängd olika komponenterna i Hadoop-ekosystemet utför ETL i större skala. 

Användningen av HDInsight i ETL-processen kan sammanfattas av denna pipeline:

![HDInsight ETL-översikt](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

I följande avsnitt utforska varje ETL-faser och deras associerade komponenter.

## <a name="orchestration"></a>Orkestrering

Orchestration sträcker sig över alla faser av ETL-pipeline. ETL-jobb i HDInsight innebär ofta flera olika produkter som arbetar tillsammans med varandra.  Du kan använda Hive för att rensa en del av data, medan Pig rensar en annan del.  Du kan använda Azure Data Factory för att läsa in data till Azure SQL Database från Azure Data Lake Store.

Orchestration behövs för att köra lämplig jobbet vid rätt tidpunkt.

### <a name="oozie"></a>Oozie

Apache Oozie är ett system för samordning av arbetsflödet som hanterar Hadoop-jobb. Oozie körs inom ett HDInsight-kluster och är integrerad med Hadoop-stacken. Oozie stöder Hadoop-jobb för Apache MapReduce, Apache Pig, Apache Hive och Apache Sqoop. Oozie kan också användas för att schemalägga jobb som är specifika för ett system, till exempel Java-program eller kommandoskript.

Mer information finns i [använder Oozie med Hadoop för att definiera och köra ett arbetsflöde på HDInsight](../hdinsight-use-oozie-linux-mac.md) ingående som visar hur du använder Oozie för att ge en slutpunkt till slutpunkt-pipeline, se [Operationalisera Data Pipeline](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory innehåller orchestration i form av plattform som en tjänst. Det är en molnbaserad integration datatjänst som hjälper dig att skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataflytt och Dataomvandling av. 

Azure Data Factory kan du:

1. Skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som mata in data från olika datalager.
2. Processen och transformera data med hjälp av compute-tjänster, till exempel Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch och Azure Machine Learning.
3. Publicera utdata till datalager som Azure SQL Data Warehouse och som sedan kan användas av Business Intelligence-program (BI).

Mer information om Azure Data Factory finns på [dokumentationen](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Mata in fillagring och resultatet lagring

Källdatafiler laddas vanligtvis till en plats i Azure Storage eller Azure Data Lake Store. Filer kan vara i valfritt format, men de är oftast flat-filer som CSV: er. 

### <a name="azure-storage"></a>Azure Storage 

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) har [skalbarhetsmål för specifika](../../storage/common/storage-scalability-targets.md).  För de flesta analytiska noder skalas Azure Storage bäst när du hanterar flera mindre filer.  Azure Storage garanterar samma prestanda, oavsett hur många filer eller hur stora filer som (så länge du befinner dig inom din gränser).  Det innebär att du kan lagra terabyte data och fortfarande få konsekvent prestanda om du använder en delmängd av data eller alla data.

Azure Storage har flera olika typer av blobbar.  En *lägga till blob* är ett bra alternativ för att lagra webbloggar eller sensordata.  

Flera blobbar kan distribueras över flera servrar ska skalas ut åtkomst till dem, men en enda blob kan bara hanteras av en enskild server. Blobbar kan samlas i blob-behållare, finns men det inga partitionering effekter från den här grupperingen.

Azure Storage har också ett WebHDFS API-lager för blobblagring.  Alla tjänster i HDInsight kan komma åt filer i Azure Blob Storage för Datarensning och databearbetning, på samma sätt som hur dessa tjänster använder Hadoop Distributed filer System (HDFS).

Data är vanligtvis inhämtas i Azure Storage med hjälp av antingen PowerShell, Azure Storage SDK: N eller AZCopy.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store (ADLS) är en hanterad, storskaliga databas för analytics-data som är kompatibelt med HDFS.  ADLS använder en design paradigmet som liknar HDFS och erbjuder obegränsade skalbarhet vad gäller total kapacitet och storleken på enskilda filer. ADLS är mycket bra när du arbetar med stora filer, eftersom en stor fil kan lagras över flera noder.  Partitionering data i ADLS sker i bakgrunden.  Tjänsten ger massivt dataflöde och kan köra analysjobb med tusentals samtidiga körare som effektivt läser och skriver flera hundra terabyte data.

Data är vanligtvis inhämtas i ADLS med hjälp av Azure Data Factory ADLS-SDK: er, AdlCopy Service, Apache DistCp eller Apache Sqoop.  Vilken av dessa tjänster ska användas i stort sett beror på var data finns.  Om data är för närvarande i ett befintligt Hadoop-kluster, kan du använda Apache DistCp, AdlCopy Service eller Azure Data Factory.  Om den är i Azure Blob Storage kan du använda Azure Data Lake Store .NET SDK, Azure PowerShell eller Azure Data Factory.

ADLS optimeras också för händelsen införandet med hjälp av Azure Event Hub eller Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Överväganden för båda lagringsalternativ

För uppladdning datauppsättningar i intervallet terabyte kan Nätverksfördröjningen vara ett större problem, särskilt om data kommer från en lokal plats.  I sådana fall kan använda du följande alternativ:

* Azure ExpressRoute: Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och din lokala infrastruktur. Dessa anslutningar ge en tillförlitlig alternativet för att överföra stora mängder data. Mer information finns i [Azure ExpressRoute dokumentation](../../expressroute/expressroute-introduction.md).

* ”Offline” överföra data. Du kan använda [Azure Import/Export service](../../storage/common/storage-import-export-service.md) att leverera hårddiskar med dina data till en Azure-datacenter. Dina data överförs först till Azure Storage-Blobbar. Du kan sedan använda [Azure Data Factory](../../data-factory/v1/data-factory-azure-datalake-connector.md) eller [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) verktyg för att kopiera data från Azure Storage BLOB till Data Lake Store.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW är ett bra alternativ för att lagra rensad och förberedda resultat för framtida analys.  Azure HDInsight kan användas för att utföra dessa tjänster för Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) är en relationsdatabas lagring som är optimerade för analytiska arbetsbelastningar.  Azure SQL DW skalas baserat på partitionerade tabeller.  Vara kan partitionerade tabeller över flera noder.  Azure SQL DW-noder har valts vid tidpunkten för skapandet.  De kan skalas efteråt, men som är en aktiv process som kan kräva dataflyttning. Se [SQL Data Warehouse - hantera Compute](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) för mer information.

### <a name="hbase"></a>HBase

Apache HBase är en nyckelvärdeslagring som är tillgängliga i Azure HDInsight.  Apache HBase är en NoSQL-databas med öppen källkod som har skapats på Hadoop och modellerats efter Google BigTable. HBase ger performant direktåtkomst och stark konsekvens för stora mängder Ostrukturerade och halvstrukturerade data i en schemalös databas sorterad per kolumnfamiljer.

Data lagras i tabellens rader och data i raderna grupperas per kolumnfamilj. HBase är en schemalös databas i den mening att varken kolumner eller den typ av data som lagras i dem måste definieras innan du använder dem. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. HBase kan förlitar sig på dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-ekosystemet.   

HBase är en utmärkt mål för sensor och loggar data för framtida analys.

HBase skalbarhet är beroende av antalet noder i HDInsight-klustret.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database och Azure-databas

Azure erbjuder tre olika relationsdatabaser som plattform som en-tjänst (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) är en implementering av Microsoft SQL Server. Mer information om prestanda finns [justera prestanda i Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure-databas för MySQL](../../mysql/overview.md) är en implementering av Oracle MySQL.
* [Azure-databas för PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) är en implementering av PostgreSQL.

Dessa produkter skala upp, vilket innebär att de är anpassas genom att lägga till mer Processorkraft och minne.  Du kan också välja att använda premiumdiskar med produkter för bättre i/o-prestanda.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) är en analysdata-motor används i stöd och Företagsanalys, tillhandahåller analysdata för business rapporter samt klientprogram som Power BI, Excel, Reporting Services-rapporter och andra data visualiseringsverktyg.

Analyskuber kan skala genom att ändra nivåer för varje enskild kub.  Mer information finns i [priser för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahera och läsa in

När data finns i Azure, kan du använda många tjänster att extrahera och läsa in den i andra produkter.  HDInsight stöder Sqoop och Flume. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop är ett verktyg för att effektivt överföra data mellan strukturerade, delvis strukturerade och Ostrukturerade datakällor. 

Sqoop använder MapReduce för att importera och exportera data för att tillhandahålla parallell åtgärd och feltolerans.

### <a name="flume"></a>Flume

Apache Flume är en distribuerad, tillförlitlig och tillgänglig tjänst för att samla in, sammanställa effektivt och flytta stora mängder loggdata. Flume har en enkel och flexibel arkitektur som baseras på strömning dataflöden. Flume är stabilt och feltolerant med justerbara tillförlitlighet metoder och många mekanismer för redundans och återställning. Flume använder en enkel utökningsbar datamodell som ger online analytiska tillämpning.

Apache Flume kan inte användas med Azure HDInsight.  En lokal Hadoop-installation kan använda Flume för att skicka data till Azure Storage-Blobbar eller Azure Data Lake Store.  Mer information finns i [med Apache Flume med HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformera

När data finns på den valda platsen, måste du rensa den, kombinera det och förbereda den för en specifik användningsmönstret.  Hive, Pig och Spark SQL är alla bra alternativ för den typ av arbete.  De stöds i HDInsight. 

## <a name="next-steps"></a>Nästa steg

* [Använda Pig med Hadoop i HDInsight](hdinsight-use-pig.md)
* [Med Apache Hive som ett ETL-verktyg](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
