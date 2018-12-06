---
title: Datascenarier som omfattar Azure Data Lake Storage Gen2 | Microsoft Docs
description: Förstå de olika scenarier och verktyg med vilken data kan inhämtas, bearbetas, hämtas och visualiseras i Data Lake Storage Gen2 (kallades tidigare Azure Data Lake Store)
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: 978f86141d72cc7be43f24909f9780ab9570605d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974894"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Med hjälp av Azure Data Lake Storage Gen2 för stordatakrav

Det finns fyra viktiga steg i stordatabearbetning:

* Mata in stora mängder data i ett datalager i realtid eller i batchar
* Bearbetning av data
* Hämta data
* Visualisera dina data

I den här artikeln ska titta vi på dessa steg med avseende på Azure Data Lake Storage Gen2 alternativ och de verktyg som är tillgängliga för att möta dina behov för stordata.

## <a name="ingest-data-into-data-lake-storage-gen2"></a>Mata in data i Data Lake Storage Gen2
Det här avsnittet beskrivs de olika datakällor och de olika sätt som dessa data kan hämtas till ett Data Lake Storage Gen2-konto.

![Mata in data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "mata in data i Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc-data
Detta representerar mindre datamängder som används för prototyper ett program för stordata. Det finns flera olika sätt att mata in ad hoc-data, beroende på den datakällan.

| Datakälla | Mata in den med hjälp av |
| --- | --- |
| Lokal dator |<ul> <li>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| Azure Storage-Blob |<ul> <li>[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)</li><li>[DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Strömmade data
Detta representerar data som genereras av olika källor, till exempel program, enheter, sensorer, osv. Dessa data kan hämtas till Data Lake Storage Gen2 av en rad olika verktyg. De här verktygen kommer vanligtvis samla in och bearbeta data regelbundet en händelse med händelse i realtid, och sedan skriva händelser i batchar i Data Lake Storage Gen2 så att de kan bearbetas ytterligare.

Följande är verktyg som du kan använda:

* [Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md) – du kan skriva data direkt till Data Lake Storage Gen2 från Storm-kluster.

### <a name="relational-data"></a>Relationsdata
Du kan också styra data från relationsdatabaser. Samla in stora mängder data som kan få viktiga insikter om bearbetas via en big datapipeline under en viss tidsperiod, relationsdatabaser. Du kan använda följande verktyg för att flytta sådana data till Data Lake Storage Gen2.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Loggdata för Web server (överföring med hjälp av anpassade program)
Den här typen av datauppsättning framhävs specifikt eftersom analys av loggdata för web server är ett vanligt användningsfall för stordata-program som kräver stora mängder loggfiler som ska överföras till Data Lake Storage Gen2. Du kan använda någon av följande verktyg för att skriva egna skript eller program för att ladda upp dessa data.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

För att ladda upp loggdata för web server, och även för att ladda upp andra typer av data (t.ex. social sentiment data), är det en bra metod för att skriva din egen anpassade skript/program eftersom det ger dig möjlighet att ta med dina data laddar du upp komponenten som en del av ditt större stordata-program. I vissa fall kan den här koden ske i form av ett skript eller ett enkelt kommandoradsverktyg. I annat fall kan koden användas för att integrera bearbetning av stordata i ett affärsprogram eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster
Klustertyper för de flesta HDInsight (Hadoop, HBase, Storm) stöder Data Lake Storage Gen2 som en databas för lagring av data. HDInsight-kluster åtkomst till data från Azure Storage BLOB-objekt (WASB). Av prestandaskäl kan kopiera du data från WASB till ett Data Lake Storage Gen2-konto som är kopplat till klustret. Du kan använda följande verktyg för att kopiera data.

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [AzCopy-verktyget](../common/storage-use-azcopy-v10.md)
* [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i en lokal eller IaaS Hadoop kluster
Stora mängder data kan lagras i befintliga Hadoop-kluster lokalt på datorer med hjälp av HDFS. Hadoop-kluster kan vara i en lokal distribution eller kan vara i ett IaaS-kluster på Azure. Det kan finnas krav för att kopiera sådana data till Azure Data Lake Storage Gen2 för en direkt metod eller i ett återkommande sätt. Det finns olika alternativ som du kan använda för att uppnå detta. Nedan visas en lista över alternativ och associerade avvägningarna.

| Metoden | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använd Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Storage Gen2 |[ADF stöder HDFS som en datakälla](../../data-factory/connector-hdfs.md) |ADM ger ut inbyggda stödet för HDFS och första klassens från slutpunkt till slutpunkt-hantering och övervakning |Kräver Data Management Gateway distribueras lokalt eller i IaaS-kluster |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Storage Gen2 med lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Storage Gen2 med: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)</li></ul> |Du kan använda open source-verktyg. |Process i flera steg som omfattar flera tekniker |

### <a name="really-large-datasets"></a>Väldigt stora datauppsättningar
För att ladda upp datauppsättningar som intervall i flera terabyte kan med hjälp av de metoder som beskrivs ovan ibland vara långsam och dyrt. I sådana fall kan använda du alternativen nedan.

* **Med hjälp av Azure ExpressRoute**. Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och infrastrukturen i era lokala. Detta ger en pålitlig alternativ för överföring av stora mängder data. Mer information finns i [dokumentation om Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>Bearbeta data som lagras i Data Lake Storage Gen2
När data är tillgängliga i Data Lake Storage Gen2 kan du köra analys på dessa data med hjälp av stöds stordata-program. För närvarande kan du använda Azure HDInsight och Azure Databricks för att köra data analysis-jobb på data som lagras i Data Lake Storage Gen2.

![Analysera data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "analysera data i Data Lake Storage Gen2")


## <a name="download-data-from-data-lake-storage-gen2"></a>Hämta data från Data Lake Storage Gen2
Du kanske också vill ladda ned eller flytta data från Azure Data Lake Storage Gen2 för scenarier som:

* Flytta data till andra databaser gränssnittet med dina befintliga pipelines för databearbetning. Du kanske exempelvis vill flytta data från Data Lake Storage Gen2 till Azure SQL Database eller en lokal SQL Server.
* Ladda ned data till den lokala datorn för bearbetning i IDE-miljöer när du skapar program prototyper.

![Egress data från Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Egress data från Data Lake Storage Gen2")

I sådana fall kan använda du någon av följande alternativ:

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>Visualisera data i Data Lake Storage Gen2
Du kan använda en blandning av tjänster för att skapa visuella representationer av data som lagras i Data Lake Storage Gen2.

![Visualisera data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "visualisera data i Data Lake Storage Gen2")

* Du kan börja med hjälp av [Azure Data Factory för att flytta data från Data Lake Storage Gen2 till Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Därefter kan du [integrera Power BI med Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) att skapa visuell representation av data.
