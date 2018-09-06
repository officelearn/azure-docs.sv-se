---
title: Datascenarier som omfattar Data Lake Storage Gen1 | Microsoft Docs
description: Förstå de olika scenarier och verktyg med vilken data kan inhämtas, bearbetas, hämtas och visualiseras i Data Lake Storage Gen1 (kallades tidigare Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 19743dcd2866b8fc7b6ad1fdf387b134f7b3ca50
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783084"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Med hjälp av Azure Data Lake Storage Gen1 för stordatakrav

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Det finns fyra viktiga steg i stordatabearbetning:

* Mata in stora mängder data i ett datalager i realtid eller i batchar
* Bearbetning av data
* Hämta data
* Visualisera dina data

I den här artikeln ska titta vi på dessa steg med avseende på Azure Data Lake Store alternativ och de verktyg som är tillgängliga för att möta dina behov för stordata.

## <a name="ingest-data-into-data-lake-store"></a>Mata in data i Data Lake Store
Det här avsnittet beskrivs de olika datakällor och de olika sätt som dessa data kan hämtas till ett Data Lake Store-konto.

![Mata in data i Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "mata in data i Data Lake Store")

### <a name="ad-hoc-data"></a>Ad hoc-data
Detta representerar mindre datamängder som används för prototyper ett program för stordata. Det finns flera olika sätt att mata in ad hoc-data, beroende på den datakällan.

| Datakälla | Mata in den med hjälp av |
| --- | --- |
| Lokal dator |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure plattformsoberoende CLI 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[Med Data Lake Tools för Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage-Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp som körs på HDInsight-kluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Strömmade data
Detta representerar data som genereras av olika källor, till exempel program, enheter, sensorer, osv. Dessa data kan hämtas till ett Data Lake Store med en mängd olika verktyg. De här verktygen kommer vanligtvis samla in och bearbeta data regelbundet en händelse med händelse i realtid, och sedan skriva händelser i batchar i Data Lake Store så att de kan bearbetas ytterligare.

Följande är verktyg som du kan använda:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -händelser matas in i Event Hubs kan skrivas till Azure Data Lake med hjälp av en Azure Data Lake Store-utdata.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) – du kan skriva data direkt till Data Lake Store från Storm-kluster.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – du kan ta emot händelser från Event Hubs och sedan skriva till Data Lake Store med hjälp av den [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relationsdata
Du kan också styra data från relationsdatabaser. Samla in stora mängder data som kan få viktiga insikter om bearbetas via en big datapipeline under en viss tidsperiod, relationsdatabaser. Du kan använda följande verktyg för att flytta sådana data till Data Lake Store.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Loggdata för Web server (överföring med hjälp av anpassade program)
Den här typen av datauppsättning framhävs specifikt eftersom analys av loggdata för web server är ett vanligt användningsfall för stordata-program som kräver stora mängder loggfiler som ska överföras till Data Lake Store. Du kan använda någon av följande verktyg för att skriva egna skript eller program för att ladda upp dessa data.

* [Azure plattformsoberoende CLI 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

För att ladda upp loggdata för web server, och även för att ladda upp andra typer av data (t.ex. social sentiment data), är det en bra metod för att skriva din egen anpassade skript/program eftersom det ger dig möjlighet att ta med dina data laddar du upp komponenten som en del av ditt större stordata-program. I vissa fall kan den här koden ske i form av ett skript eller ett enkelt kommandoradsverktyg. I annat fall kan koden användas för att integrera bearbetning av stordata i ett affärsprogram eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster
Klustertyper för de flesta HDInsight (Hadoop, HBase, Storm) stöder Data Lake Store som en databas för lagring av data. HDInsight-kluster åtkomst till data från Azure Storage BLOB-objekt (WASB). Av prestandaskäl kan kopiera du data från WASB till ett Data Lake Store-konto som är kopplat till klustret. Du kan använda följande verktyg för att kopiera data.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i en lokal eller IaaS Hadoop kluster
Stora mängder data kan lagras i befintliga Hadoop-kluster lokalt på datorer med hjälp av HDFS. Hadoop-kluster kan vara i en lokal distribution eller kan vara i ett IaaS-kluster på Azure. Det kan finnas krav för att kopiera sådana data till Azure Data Lake Store för en direkt metod eller i ett återkommande sätt. Det finns olika alternativ som du kan använda för att uppnå detta. Nedan visas en lista över alternativ och associerade avvägningarna.

| Metoden | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använd Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Store |[ADF stöder HDFS som en datakälla](../data-factory/connector-hdfs.md) |ADM ger ut inbyggda stödet för HDFS och första klassens från slutpunkt till slutpunkt-hantering och övervakning |Kräver Data Management Gateway distribueras lokalt eller i IaaS-kluster |
| Exportera data från Hadoop som filer. Kopiera sedan filerna till Azure Data Lake Store med hjälp av lämplig mekanism. |Du kan kopiera filer till Azure Data Lake Store med hjälp av: <ul><li>[Azure PowerShell för Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure plattformsoberoende CLI 2.0 för Windows OS](data-lake-store-get-started-cli-2.0.md)</li><li>Anpassad app med hjälp av alla Data Lake Store SDK</li></ul> |Snabbt att komma igång. Kan göra anpassade uppladdningar |Process i flera steg som omfattar flera tekniker. Hantering och övervakning utökas för att vara en utmaning med tiden som den anpassade naturen av verktyg |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Store med hjälp av lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Store med hjälp av: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp som körs på HDInsight-kluster](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Du kan använda open source-verktyg. |Process i flera steg som omfattar flera tekniker |

### <a name="really-large-datasets"></a>Väldigt stora datauppsättningar
För att ladda upp datauppsättningar som intervall i flera terabyte kan med hjälp av de metoder som beskrivs ovan ibland vara långsam och dyrt. I sådana fall kan använda du alternativen nedan.

* **Med hjälp av Azure ExpressRoute**. Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och infrastrukturen i era lokala. Detta ger en pålitlig alternativ för överföring av stora mängder data. Mer information finns i [dokumentation om Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **Dataöverföringen ”offline”**. Om du inte är möjligt med hjälp av Azure ExpressRoute av någon anledning, kan du använda [tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) att skicka hårddiskar med dina data till ett Azure-datacenter. Dina data överförs först till Azure Storage-Blobbar. Du kan sedan använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) eller [AdlCopy verktyget](data-lake-store-copy-data-azure-storage-blob.md) att kopiera data från Azure Storage BLOB till Data Lake Store.

  > [!NOTE]
  > När du använder Import/Export-tjänst, filstorlekar på diskar som du skickar till Azure-Datacenter inte får vara större än 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Bearbeta data som lagras i Data Lake Store
När data är tillgängliga i Data Lake Store kan du köra analys på dessa data med hjälp av stöds stordata-program. För närvarande kan du använda Azure HDInsight och Azure Data Lake Analytics för att köra data analysis-jobb på data som lagras i Data Lake Store.

![Analysera data i Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "analysera data i Data Lake Store")

Du kan titta på följande exempel.

* [Skapa ett HDInsight-kluster med Data Lake Store som lagring](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Hämta data från Data Lake Store
Du kanske också vill ladda ned eller flytta data från Azure Data Lake Store för scenarier som:

* Flytta data till andra databaser gränssnittet med dina befintliga pipelines för databearbetning. Du kanske exempelvis vill flytta data från Data Lake Store till Azure SQL Database eller en lokal SQL Server.
* Ladda ned data till den lokala datorn för bearbetning i IDE-miljöer när du skapar program prototyper.

![Egress data från Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "Egress data från Data Lake Store")

I sådana fall kan använda du någon av följande alternativ:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Du kan också använda följande metoder för att skriva egna skript/program för att hämta data från Data Lake Store.

* [Azure plattformsoberoende CLI 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualisera data i Data Lake Store
Du kan använda en blandning av tjänster för att skapa visuella representationer av data som lagras i Data Lake Store.

![Visualisera data i Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "visualisera data i Data Lake Store")

* Du kan börja med hjälp av [Azure Data Factory för att flytta data från Data Lake Store till Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Därefter kan du [integrera Power BI med Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) att skapa visuell representation av data.
