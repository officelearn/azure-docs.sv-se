---
title: "Scenarion för data som rör Data Lake Store | Microsoft Docs"
description: "Förstå de olika scenarier och verktyg med vilken data kan inhämtas, bearbetas, hämtas och visualiseras i ett Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 6428c6d9fcb577f18221ee48a61456c460bd8176
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Med hjälp av Azure Data Lake Store för stordata krav
Det finns fyra viktiga steg i stort databearbetning:

* Vill föra in stora mängder data i datalagret i realtid eller i batchar
* Bearbetning av data
* Hämta data
* Visualisera dina data

I den här artikeln titta vi på dessa steg med avseende på Azure Data Lake Store att förstå alternativ och verktyg som finns tillgängliga för att uppfylla dina behov av stordata.

## <a name="ingest-data-into-data-lake-store"></a>Mata in data i Data Lake Store
Det här avsnittet beskrivs de olika källorna av data och de olika sätten att data som kan inhämtas till ett Data Lake Store-konto.

![Mata in data i Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "mata in data i Data Lake Store")

### <a name="ad-hoc-data"></a>Ad hoc-data
Representerar mindre datauppsättningar som används för prototyper ett stort program. Det finns olika sätt att vill föra in ad hoc-data beroende på källan för data.

| Datakälla | Mata in den med hjälp av |
| --- | --- |
| Lokal dator |<ul> <li>[Azure Portal](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure plattformsoberoende CLI 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[Med hjälp av Data Lake-verktyg för Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp som körs på HDInsight-kluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Direktuppspelat data
Detta representerar data som genereras av olika källor, till exempel program, enheter, sensorer och så vidare. Dessa data kan inhämtas i ett Data Lake Store av olika verktyg. Dessa verktyg vanligtvis kan fånga och bearbeta data för en händelse med händelse i realtid, och sedan skriva händelser i batchar till Data Lake Store så att de kan bearbetas ytterligare.

Följande är ett verktyg som du kan använda:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -händelser som inhämtas i Händelsehubbar kan skrivas till Azure Data Lake med Azure Data Lake Store utdata.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) -du kan skriva data till Data Lake Store direkt från Storm-kluster.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – du kan ta emot händelser från Event Hubs och skriver den till Data Lake Store med hjälp av den [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relationella data
Du kan också styra data från relationsdatabaser. Samla in stora mängder data som kan ge viktiga insikter om bearbetas via en pipeline för stordata under en viss tidsperiod relationsdatabaser. Du kan använda följande verktyg för att flytta dessa data till Data Lake Store.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Loggdata för Web server (överföringen med hjälp av anpassade program)
Den här typen av datauppsättning framhävs specifikt eftersom analys av loggdata för web server är ett vanligt användningsfall för stordataprogram och kräver stora volymer av loggfiler som ska överföras till Data Lake Store. Du kan använda någon av följande verktyg för att skriva egna skript eller program för att överföra dessa data.

* [Azure plattformsoberoende CLI 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

För uppladdning av loggdata för web server, och även för uppladdning av andra typer av data (t.ex. social våra data), är det en bra metod för att skriva egna anpassade skript/program eftersom det ger dig möjlighet att inkludera överför komponenten som en del av data tillämpningsprogrammet större stordata. I vissa fall kan den här koden ta form av ett skript eller ett enkelt kommandoradsverktyg. I annat fall kan koden användas för att integrera stort databehandling i ett affärsprogram eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster
Klustertyper för de flesta HDInsight (Hadoop, HBase, Storm) stöder Data Lake Store som en databas för lagring av data. HDInsight-kluster komma åt data från Azure Storage BLOB (WASB). För bättre prestanda, kan du kopiera data från WASB till ett Data Lake Store-konto som är associerade med klustret. Du kan använda följande verktyg för att kopiera data.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i lokala eller IaaS Hadoop kluster
Stora mängder data kan lagras i befintliga Hadoop-kluster, lokalt på datorer med hjälp av HDFS. Hadoop-kluster kan vara i en lokal distribution eller kan vara i ett IaaS-kluster i Azure. Det kan finnas krav för att kopiera dessa data till Azure Data Lake Store oneoff metod eller ett återkommande sätt. Det finns olika alternativ som du kan använda för att uppnå detta. Nedan visas en lista med alternativ och associerade avvägningarna.

| Metoden | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använd Azure Data Factory (ADM) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Store |[ADF stöder HDFS som en datakälla](../data-factory/connector-hdfs.md) |ADF ger out box-stöd för HDFS och första klass från slutpunkt till slutpunkt-hantering och övervakning |Kräver att Data Management Gateway till distribuerade lokalt eller i IaaS-kluster |
| Exportera data från Hadoop som filer. Kopiera sedan filerna till Azure Data Lake Store med hjälp av lämplig mekanism. |Du kan kopiera filer till Azure Data Lake Store med hjälp av: <ul><li>[Azure PowerShell för Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure plattformsoberoende CLI 2.0 för Windows OS](data-lake-store-get-started-cli-2.0.md)</li><li>Anpassad app med hjälp av alla Data Lake Store SDK</li></ul> |Snabbt att komma igång. Kan göra anpassade överföringar |Flera steg som omfattar flera tekniker. Hantering och övervakning kommer att växa ska vara en utmaning med tanke på anpassade verktygen tiden |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera data från Azure Storage till Data Lake Store med hjälp av lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Store med hjälp av: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp körs på HDInsight-kluster](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Du kan använda verktyg med öppen källkod. |Flera steg som omfattar flera tekniker |

### <a name="really-large-datasets"></a>Mycket stora datauppsättningar
För uppladdning av datauppsättningar som finns i många terabyte intervallet, kan med hjälp av metoderna som beskrivs ovan ibland vara långsam och dyra. I sådana fall kan använda du alternativen nedan.

* **Med hjälp av Azure ExpressRoute**. Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och infrastruktur lokalt. Detta ger en tillförlitlig alternativet för att överföra stora mängder data. Mer information finns i [Azure ExpressRoute dokumentation](../expressroute/expressroute-introduction.md).
* **”Offline” att överföra data**. Om du inte är möjligt med hjälp av Azure ExpressRoute av någon anledning, kan du använda [Azure Import/Export service](../storage/common/storage-import-export-service.md) att leverera hårddiskar med dina data till en Azure-datacenter. Dina data överförs först till Azure Storage-Blobbar. Du kan sedan använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) eller [AdlCopy verktyget](data-lake-store-copy-data-azure-storage-blob.md) att kopiera data från Azure Storage-Blobbar till Data Lake Store.

  > [!NOTE]
  > När du använder tjänsten Import/Export filstorlekar på diskar som du levererar till Azure-Datacenter inte får vara större än 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Bearbeta data som lagras i Data Lake Store
Du kan köra analysis på dessa data med hjälp av stöds stordataprogram när data är tillgängliga i Data Lake Store. För närvarande kan du använda Azure HDInsight och Azure Data Lake Analytics för att köra jobb för analys av data på de data som lagras i Data Lake Store.

![Analysera data i Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "analysera data i Data Lake Store")

Du kan se följande exempel.

* [Skapa ett HDInsight-kluster med Data Lake Store som lagring](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Hämta data från Data Lake Store
Du kanske också vill hämta eller flytta data från Azure Data Lake Store för scenarier som:

* Flytta data till andra databaser gränssnittet med din befintliga pipelines för databearbetning. Du kanske vill flytta data från Data Lake Store till Azure SQL Database eller lokal SQL Server.
* Ladda ned data till den lokala datorn för bearbetning i IDE miljöer när du skapar program prototyper.

![Utgående data från Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "utgående data från Data Lake Store")

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

* Du kan starta med hjälp av [Azure Data Factory för att flytta data från Data Lake Store till Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Därefter kan du [integrera Power BI med Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) att skapa visuell representation av data.
