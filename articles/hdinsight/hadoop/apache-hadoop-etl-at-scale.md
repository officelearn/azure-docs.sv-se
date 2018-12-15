---
title: Extrahering, transformering och inläsning (ETL) i stor skala - Azure HDInsight
description: Lär dig hur ETL används i HDInsight med Apache Hadoop.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 205ba822b1221de34f3ee1ae25974a406f2013cb
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438106"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrahering, transformering och laddning (ETL) i stor skala

Extrahering, transformering och laddning (ETL) är den process som data är hämtade från olika källor, som samlas in i en standardplats, rensade och bearbetas och slutligen läses in i ett datalager som den kan ta emot. Importera data äldre ETL-processer, rensa den på plats och sedan lagra den i en motor för relationsdata. Med HDInsight stöder en mängd olika komponenter för Apache Hadoop-ekosystemet utföra ETL i stor skala. 

Användningen av HDInsight i ETL-processen kan sammanfattas av denna pipeline:

![HDInsight ETL-översikt](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

I följande avsnitt utforska vart och ett av ETL-faser och associerade komponenter.

## <a name="orchestration"></a>Orkestrering

Orchestration sträcker sig över alla faser av ETL-pipeline. ETL-jobb i HDInsight innebär ofta flera olika produkter som arbetar tillsammans med varandra.  Du kan använda Hive för att rensa en del av data, medan Pig rensar en annan del.  Du kan använda Azure Data Factory för att läsa in data till Azure SQL Database från Azure Data Lake Store.

Orchestration krävs lämpliga jobbet körs vid rätt tidpunkt.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie är ett system för samordning av arbetsflöden som hanterar Hadoop-jobb. Oozie körs med ett HDInsight-kluster och är integrerat med Hadoop-stacken. Oozie stöder Hadoop-jobb för Apache Hadoop MapReduce, Apache Pig, Apache Hive och Apache Sqoop. Oozie kan också användas för att schemalägga jobb som är specifika för ett system, till exempel Java-program eller kommandoskript.

Mer information finns i [använder Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde på HDInsight](../hdinsight-use-oozie-linux-mac.md) mer ingående information som visar hur du kan använda Oozie för att driva en slutpunkt till slutpunkt-pipeline, se [Operationalisera Datapipeline](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory erbjuder orchestration-funktioner i form av platform as a service. Det är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och Dataomvandling. 

Azure Data Factory kan du:

1. Skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som matar in data från olika datalager.
2. Bearbeta och omvandla data med Beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch och Azure Machine Learning.
3. Publicera utdata till datalager som Azure SQL Data Warehouse och som sedan kan användas av Business Intelligence-program (BI).

Mer information om Azure Data Factory finns i den [dokumentation](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Mata in fillagring och lagring av resultat

Källdatafiler läses vanligtvis till en plats i Azure Storage eller Azure Data Lake Storage. Filer kan vara i valfritt format, men de är oftast flata filer som CSV: er. 

### <a name="azure-storage"></a>Azure Storage 

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) har [specifika skalbarhetsmål](../../storage/common/storage-scalability-targets.md).  För de flesta analytiska noderna skalar Azure Storage bäst när du hanterar många mindre filer.  Azure Storage garanterar samma prestanda, oavsett hur många filer eller hur stora filer (om du befinner dig inom dina gränser).  Det innebär att du kan lagra terabyte med data och fortfarande få konsekvent prestanda om du använder en delmängd av data eller alla data.

Azure Storage har flera olika typer av blobar.  En *tilläggsblobb* är ett bra alternativ för att lagra webbloggar eller sensordata.  

Flera blobar kan distribueras över flera servrar för att skala ut åtkomst till dem, men en enda blob kan bara hanteras av en enskild server. Blobar kan samlas i blob-behållare, finns men det inga partitionering effekter från den här grupperingen.

Azure Storage har också ett WebHDFS API-lager för blob storage.  Alla tjänster i HDInsight kan komma åt filer i Azure Blob Storage för Datarensning och databehandling på samma sätt som hur dessa tjänster skulle använda Hadoop Distributed filer System (HDFS).

Data är vanligtvis matas in i Azure Storage med hjälp av antingen PowerShell, Azure Storage SDK eller AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) är en hanterad lagringsplats i hyperskala för analytics-data som är kompatibelt med HDFS.  ADLS använder en design-paradigm som liknar HDFS och erbjuder obegränsad skalbarhet vad gäller total kapacitet och storleken på enskilda filer. ADLS är mycket bra när du arbetar med stora filer, eftersom en stor fil kan lagras över flera noder.  Partitionera data i ADLS sker i bakgrunden.  Tjänsten ger massivt dataflöde och kan köra analysjobb med tusentals samtidiga körare som effektivt läser och skriver flera hundra terabyte data.

Data matas vanligtvis till ADLS med hjälp av Azure Data Factory, ADLS-SDK: er, AdlCopy Service, Apache DistCp eller Apache Sqoop.  Dessa tjänster att använda hög grad beroende på om data.  Om data är för närvarande i ett befintligt Hadoop-kluster, kan du använda Apache DistCp, AdlCopy Service eller Azure Data Factory.  Om det är i Azure Blob Storage kan du använda Azure Data Lake Storage .NET SDK, Azure PowerShell eller Azure Data Factory.

ADLS optimeras även för händelsepåfyllning med Azure Event Hub eller Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Överväganden för båda lagringsalternativ

För att ladda upp datauppsättningar i terabyte-intervallet, kan svarstid för nätverk vara ett större problem, särskilt om data kommer från en lokal plats.  I sådana fall kan använda du följande alternativ:

* Azure ExpressRoute:  Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och din lokala infrastruktur. De här anslutningarna tillhandahåller en pålitlig alternativ för överföring av stora mängder data. Mer information finns i [dokumentation om Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* ”Offline” dataöverföringen. Du kan använda [tjänsten Azure Import/Export](../../storage/common/storage-import-export-service.md) att skicka hårddiskar med dina data till ett Azure-datacenter. Dina data överförs först till Azure Storage-Blobbar. Du kan sedan använda [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) eller [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) verktyg för att kopiera data från Azure Storage-blobbar till Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW är ett bra alternativ för att lagra rensade och förberett resultat för framtida analys.  Azure HDInsight kan användas för att utföra dessa tjänster för Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) är en relationsdatabas store optimerade för analytiska arbetsbelastningar.  Azure SQL DW skalas baserat på partitionerade tabeller.  Tabeller kan partitioneras över flera noder.  Azure SQL DW-noder har valts vid tidpunkten för skapandet.  De kan skalas i efterhand, men det är en aktiv process som kan kräva dataförflyttning. Se [SQL Data Warehouse – hantera Compute](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) för mer information.

### <a name="apache-hbase"></a>Apache HBase

Apache HBase är en nyckel / värde-lagring som är tillgängliga i Azure HDInsight.  Apache HBase är en NoSQL-databas med öppen källkod som har skapats på Hadoop och modellerats efter Google BigTable. HBase ger högpresterande direktåtkomst och stark konsekvens för stora mängder Ostrukturerade och semistrukturerade data i en schemalös databas sorterad per kolumnfamiljer.

Data lagras i tabellens rader och data i raderna grupperas per kolumnfamilj. HBase är en schemalös databas i den mening att varken kolumner eller den typ av data som lagras i dem måste definieras innan du använder dem. Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. HBase lita på dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-ekosystemet.   

HBase är en utmärkt mål för sensorn och logga data för framtida analys.

HBase skalbarhet beror på antalet noder i HDInsight-klustret.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database och Azure-databas

Azure erbjuder tre olika relationsdatabaser som platform-as-a-service (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) är en implementering av Microsoft SQL Server. Läs mer på prestanda, [justera prestanda i Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database för MySQL](../../mysql/overview.md) är en implementering av Oracle MySQL.
* [Azure Database för PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) är en implementering av PostgreSQL.

De här produkterna skala upp, vilket innebär att de skalas genom att lägga till mer Processorkraft och minne.  Du kan också välja att använda premium-diskar med produkterna för bättre i/o-prestanda.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) är en motor för analysdata som används i stöd för beslut och affärsanalys, vilket ger analysdata för business-rapporter och klientprogram som Power BI, Excel, Reporting Services-rapporter och andra data visualiseringsverktyg.

Analyskuber kan skala genom att ändra nivåerna för varje enskild kub.  Mer information finns i [priser för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahera och läsa in

När data finns i Azure, kan du kan använda många tjänster för att extrahera och läsa in dem i andra produkter.  HDInsight stöder Sqoop och Flume. 

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop är ett verktyg som utformats för att effektivt överföra data mellan strukturerade, halvstrukturerade och Ostrukturerade datakällor. 

Sqoop använder MapReduce för att importera och exportera data för att ge parallell åtgärd och feltolerans.

### <a name="apache-flume"></a>Apache Flume

Apache Flume är en distribuerad, tillförlitliga och tillgängliga tjänst för effektivt samla in, sammanställa och flytta stora mängder loggdata. Flume har en enkel och flexibel arkitektur baserad på strömmande dataflöden. Flume är stabila och feltolerant med justerbara tillförlitlighet mekanismer och många mekanismer för redundans och återställning. Flume använder en enkel utökningsbar datamodell som möjliggör online analytiska program.

Apache Flume kan inte användas med Azure HDInsight.  En lokal Hadoop-installation kan använda Flume för att skicka data till Azure Storage-Blobbar eller Azure Data Lake Storage.  Mer information finns i [med hjälp av Apache Flume med HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformering

När det finns data i den valda platsen, måste du rensa den, kombinera det eller förbereda den för en specifik användningsmönstret.  Hive, Pig och Spark SQL är alla bra val för den typ av arbete.  De stöds på HDInsight. 

## <a name="next-steps"></a>Nästa steg

* [Använda Apache Pig med Apache Hadoop på HDInsight](hdinsight-use-pig.md)
* [Med Apache Hive som ett ETL-verktyg](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
