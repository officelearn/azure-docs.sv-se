---
title: Extrahera, transformera och läsa in (ETL) i skala - Azure HDInsight
description: Lär dig hur extrahera, transformera och ladda används i HDInsight med Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: f4be3343f090c4d31ccb85eba8e99f22a3b1fcae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529483"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrahera, transformera och ladda (ETL) i stor skala

Extrahera, transformera och ladda (ETL) är den process genom vilken data förvärvas från olika källor, samlas in på en standardplats, rengöras och bearbetas, och slutligen laddas i ett datalager som det kan efterfrågas. Äldre ETL-processer importerar data, rensar dem på plats och lagrar dem sedan i en relationsdatamotor. Med HDInsight stöder en mängd olika Apache Hadoop-ekosystemkomponenter att utföra ETL i stor skala.

Användningen av HDInsight i ETL-processen kan sammanfattas av den här pipelinen:

![HDInsight ETL i skala översikt](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

I följande avsnitt undersöks var och en av ETL-faserna och tillhörande komponenter.

## <a name="orchestration"></a>Orkestrering

Orkestrering sträcker sig över alla faser av ETL-pipelinen. ETL jobb i HDInsight innebär ofta flera olika produkter som arbetar tillsammans med varandra.  Du kan använda Hive för att rensa en del av data, medan Pig rensar en annan del.  Du kan använda Azure Data Factory för att läsa in data i Azure SQL Database från Azure Data Lake Store.

Orkestrering behövs för att köra rätt jobb vid lämplig tidpunkt.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie är ett system för att koordinera arbetsflöden som hanterar Hadoop-jobb. Oozie körs inom ett HDInsight-kluster och är integrerat med Hadoop-stacken. Oozie stöder Hadoop-jobb för Apache Hadoop MapReduce, Apache Pig, Apache Hive och Apache Sqoop. Oozie kan också användas för att schemalägga jobb som är specifika för ett system, till exempel Java-program eller skalskript.

Mer information finns i [Använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde på HDInsight](../hdinsight-use-oozie-linux-mac.md) För en djupdykning som visar hur du använder Oozie för att driva en end-to-end pipeline, se [Operationalize datapipelinen](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory tillhandahåller orchestration-funktioner i form av plattform-som-en-tjänst. Det är en molnbaserad dataintegrationstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att dirigera och automatisera dataförflyttningar och dataomvandling.

Med Hjälp av Azure Data Factory kan du:

1. Skapa och schemalägga datadrivna arbetsflöden (så kallade pipelines) som förtär data från olika datalager.
2. Bearbeta och omvandla data med hjälp av beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch och Azure Machine Learning.
3. Publicera utdata till datalager som Azure SQL Data Warehouse och som sedan kan användas av Business Intelligence-program (BI).

Mer information om Azure Data Factory finns i [dokumentationen](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingest fillagring och resultatlagring

Källdatafiler läses vanligtvis in på en plats i Azure Storage eller Azure Data Lake Storage. Filer kan vara i valfritt format, men vanligtvis är de platta filer som CSV: er.

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) har specifika skalbarhetsmål. Mer information finns i [Skalbarhets- och prestandamål för Blob-lagring](../../storage/blobs/scalability-targets.md). För de flesta analytiska noder skalas Azure Storage bäst när det handlar om många mindre filer.  Azure Storage garanterar samma prestanda, oavsett hur många filer eller hur stora filerna (så länge du är inom dina gränser).  Det innebär att du kan lagra terabyte data och ändå få konsekventa prestanda, oavsett om du använder en delmängd av data eller alla data.

Azure Storage har flera olika typer av blobbar.  En *tilläggsblob* är ett bra alternativ för att lagra webbloggar eller sensordata.  

Flera blobbar kan distribueras över många servrar för att skala ut åtkomsten till dem, men en enda blob kan bara hanteras av en enda server. Även om blobbar logiskt kan grupperas i blob-behållare, finns det inga partitioneringskonsekvenser från den här grupperingen.

Azure Storage har också ett WebHDFS API-lager för blob-lagring.  Alla tjänster i HDInsight kan komma åt filer i Azure Blob Storage för datarengöring och databehandling, på samma sätt som dessa tjänster skulle använda Hadoop Distributed Files System (HDFS).

Data används vanligtvis i Azure Storage med hjälp av antingen PowerShell, Azure Storage SDK eller AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) är en hanterad lagringsplats i hyperskala för analysdata som är kompatibel med HDFS.  ADLS använder ett designparadigm som liknar HDFS och erbjuder obegränsad skalbarhet när det gäller total kapacitet och storleken på enskilda filer. ADLS är mycket bra när du arbetar med stora filer, eftersom en stor fil kan lagras över flera noder.  Partitionering av data i ADLS görs bakom kulisserna.  Du får massivt dataflöde för att köra analytiska jobb med tusentals samtidiga executors som effektivt läser och skriver hundratals terabyte data.

Data används vanligtvis i ADLS med Azure Data Factory, ADLS SDK: er, AdlCopy Service, Apache DistCp eller Apache Sqoop.  Vilka av dessa tjänster som ska användas beror till stor del på var uppgifterna finns.  Om data för närvarande finns i ett befintligt Hadoop-kluster kan du använda Apache DistCp, AdlCopy Service eller Azure Data Factory.  Om det finns i Azure Blob Storage kan du använda Azure Data Lake Storage .NET SDK, Azure PowerShell eller Azure Data Factory.

ADLS är också optimerat för händelseintag med Azure Event Hub eller Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Överväganden för båda lagringsalternativen

För att överföra datauppsättningar i terabyte-området kan nätverksfördröjning vara ett stort problem, särskilt om data kommer från en lokal plats.  I sådana fall kan du använda alternativen nedan:

* Azure ExpressRoute: Azure ExpressRoute låter dig skapa privata anslutningar mellan Azure-datacenter och din lokala infrastruktur. Dessa anslutningar är ett tillförlitligt alternativ för överföring av stora mängder data. Mer information finns i [Azure ExpressRoute-dokumentation](../../expressroute/expressroute-introduction.md).

* "Offline" överföring av data. Du kan använda [Azure Import/Export-tjänsten](../../storage/common/storage-import-export-service.md) för att leverera hårddiskar med dina data till ett Azure-datacenter. Dina data överförs först till Azure Storage Blobbar. Du kan sedan använda [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) eller [AdlCopy-verktyget](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) för att kopiera data från Azure Storage-blobbar till Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW är ett utmärkt val för att lagra rensade och förberedda resultat för framtida analyser.  Azure HDInsight kan användas för att utföra dessa tjänster för Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) är ett relationsdatabasarkiv som är optimerat för analytiska arbetsbelastningar.  Azure SQL DW-skalor baserat på partitionerade tabeller.  Tabeller kan partitioneras mellan flera noder.  Azure SQL DW-noder väljs när de skapas.  De kan skala i efterhand, men det är en aktiv process som kan kräva dataflyttning. Mer information finns i [SQL Data Warehouse - Manage Compute](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase är en nyckelvärdesbutik som är tillgänglig i Azure HDInsight.  Apache HBase är en NoSQL-databas med öppen källkod som har skapats på Hadoop och modellerats efter Google BigTable. HBase ger högpresterande slumpmässig åtkomst och stark konsekvens för stora mängder ostrukturerade och semistrukturerade data i en schemalös databas som organiseras av kolumnfamiljer.

Data lagras i tabellens rader och data i raderna grupperas per kolumnfamilj. HBase är en schemalös databas i den mening att varken kolumner eller den typ av data som lagras i dem måste definieras innan du använder dem. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. HBase kan förlita sig på dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-ekosystemet.

HBase är ett utmärkt resmål för sensor- och loggdata för framtida analys.

HBase skalbarhet är beroende av antalet noder i HDInsight-klustret.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database och Azure Database

Azure erbjuder tre olika relationsdatabaser som PAAS (Platform-as-a-service).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) är en implementering av Microsoft SQL Server. Mer information om prestanda finns [i Justera prestanda i Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) är en implementering av Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) är en implementering av PostgreSQL.

Dessa produkter skala upp, vilket innebär att de skalas genom att lägga till mer CPU och minne.  Du kan också välja att använda premiumdiskar med produkterna för bättre I/O-prestanda.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) är en analytisk datamotor som används i beslutsstöd och affärsanalys, som tillhandahåller analysdata för affärsrapporter och klientprogram som Power BI, Excel, Reporting Services-rapporter och andra data visualiseringsverktyg.

Analyskuber kan skalas genom att ändra nivåer för varje enskild kub.  Mer information finns i [Azure Analysis Services Pricing](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahera och ladda

När data finns i Azure kan du använda många tjänster för att extrahera och läsa in dem i andra produkter.  HDInsight stöder Sqoop och Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop är ett verktyg som utformats för att effektivt överföra data mellan strukturerade, halvstrukturerade och ostrukturerade datakällor.

Sqoop använder MapReduce för att importera och exportera data, för att ge parallell drift och feltolerans.

### <a name="apache-flume"></a>Apache Flume

Apache Flume är en distribuerad, tillförlitlig och tillgänglig tjänst för att effektivt samla in, samla in och flytta stora mängder loggdata. Flume har en enkel och flexibel arkitektur baserad på strömmande dataflöden. Flume är robust och feltolerant med avstämda tillförlitlighetsmekanismer och många redundans- och återställningsmekanismer. Flume använder en enkel utökningsbar datamodell som möjliggör online analytisk tillämpning.

Apache Flume kan inte användas med Azure HDInsight.  En lokal Hadoop-installation kan använda Flume för att skicka data till antingen Azure Storage Blobbar eller Azure Data Lake Storage.  Mer information finns i [Använda Apache Flume med HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformering

När det finns data på den valda platsen måste du rengöra dem, kombinera dem eller förbereda dem för ett visst användningsmönster.  Hive, Pig och Spark SQL är alla bra val för den typen av arbete.  De stöds alla på HDInsight.

## <a name="next-steps"></a>Nästa steg

* [Använda Apache Hive som ETL-verktyg](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Flytta data från Azure SQL Database till Apache Hive-tabellen](./apache-hadoop-use-sqoop-mac-linux.md)
