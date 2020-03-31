---
title: Datascenarier som involverar Data Lake Storage Gen1 | Microsoft-dokument
description: Förstå de olika scenarier och verktyg som använder vilka data som kan intas, bearbetas, laddas ned och visualiseras i Data Lake Storage Gen1 (tidigare känt som Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: a8f9b66bf9a301888f2371fb1c58a4845c2232b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536164"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Använda Azure Data Lake Storage Gen1 för stordatakrav

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Det finns fyra viktiga steg i stordatabehandling:

* Intag av stora mängder data i ett datalager, i realtid eller i omgångar
* Behandling av data
* Ladda ner data
* Visualisera data

I den här artikeln tittar vi på dessa steg med avseende på Azure Data Lake Storage Gen1 för att förstå de alternativ och verktyg som är tillgängliga för att uppfylla dina stordatabehov.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Inta data i Data Lake Storage Gen1
I det här avsnittet beskrivs de olika datakällorna och de olika sätt på vilka dessa data kan förtäras till ett Data Lake Storage Gen1-konto.

![Inta data i Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Inta data i Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Ad hoc-data
Detta representerar mindre datauppsättningar som används för att prototypera ett stordataprogram. Det finns olika sätt att inta ad hoc-data beroende på datakällan.

| Datakälla | Inta den med |
| --- | --- |
| Lokal dator |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Använda datasjöverktyg för Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp körs på HDInsight-kluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Strömmade data
Detta representerar data som kan genereras av olika källor såsom applikationer, enheter, sensorer, etc. Dessa data kan intas i Data Lake Storage Gen1 med en mängd olika verktyg. Dessa verktyg samlar vanligtvis in och bearbetar data händelse för händelse i realtid och skriver sedan händelserna i batchar i Data Lake Storage Gen1 så att de kan bearbetas ytterligare.

Följande är verktyg som du kan använda:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) - Händelser som matas in i eventhubbar kan skrivas till Azure Data Lake Storage Gen1 med hjälp av en Azure Data Lake Storage Gen1-utdata.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) – Du kan skriva data direkt till Data Lake Storage Gen1 från Storm-klustret.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – Du kan ta emot händelser från eventhubbar och sedan skriva den till Data Lake Storage Gen1 med [datasjölagringgengen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relationsdata
Du kan också hämta data från relationsdatabaser. Under en viss tidsperiod samlar relationsdatabaser in enorma mängder data som kan ge viktiga insikter om de bearbetas via en stordatapipeline. Du kan använda följande verktyg för att flytta sådana data till Data Lake Storage Gen1.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webbserverloggdata (ladda upp med anpassade program)
Den här typen av datauppsättning anropas specifikt eftersom analys av webbserverloggdata är ett vanligt användningsfall för stordataprogram och kräver stora volymer loggfiler som ska överföras till Data Lake Storage Gen1. Du kan använda något av följande verktyg för att skriva egna skript eller program för att ladda upp sådana data.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

För att ladda upp webbserverloggdata, och även för att ladda upp andra typer av data (t.ex. sociala sentimentdata), är det en bra metod för att skriva egna anpassade skript /program eftersom det ger dig flexibiliteten att inkludera din datauppladdning komponent som en del av din större big data-program. I vissa fall kan den här koden ha formen av ett skript eller ett enkelt kommandoradsverktyg. I andra fall kan koden användas för att integrera stordatabehandling i ett affärsprogram eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster
De flesta HDInsight-klustertyper (Hadoop, HBase, Storm) stöder Data Lake Storage Gen1 som en datalagringslagringsplats. HDInsight-kluster får åtkomst till data från Azure Storage Blobbar (WASB). För bättre prestanda kan du kopiera data från WASB till ett Data Lake Storage Gen1-konto som är associerat med klustret. Du kan använda följande verktyg för att kopiera data.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy-tjänst](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i lokala kluster eller IaaS Hadoop-kluster
Stora mängder data kan lagras i befintliga Hadoop-kluster, lokalt på datorer som använder HDFS. Hadoop-klustren kan finnas i en lokal distribution eller vara i ett IaaS-kluster på Azure. Det kan finnas krav på att kopiera sådana data till Azure Data Lake Storage Gen1 för en engångsmetod eller på ett återkommande sätt. Det finns olika alternativ som du kan använda för att uppnå detta. Nedan finns en lista över alternativ och tillhörande kompromisser.

| Metod | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använda Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Storage Gen1 |[ADF stöder HDFS som datakälla](../data-factory/connector-hdfs.md) |ADF ger direkt stöd för HDFS och förstklassig hantering och övervakning av slutkund |Kräver att datahanteringsgateway distribueras lokalt eller i IaaS-klustret |
| Exportera data från Hadoop som filer. Kopiera sedan filerna till Azure Data Lake Storage Gen1 med lämplig mekanism. |Du kan kopiera filer till Azure Data Lake Storage Gen1 med: <ul><li>[Azure PowerShell för Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Anpassad app med datasjölagring Gen1 SDK</li></ul> |Snabbt att komma igång. Kan göra anpassade uppladdningar |Flerstegsprocess som involverar flera tekniker. Förvaltning och övervakning kommer att växa till en utmaning över tiden med tanke på de anpassade verktygens |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Storage Gen1 med lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Storage Gen1 med: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp körs på HDInsight kluster](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Du kan använda verktyg med öppen källkod. |Flerstegsprocess som involverar flera tekniker |

### <a name="really-large-datasets"></a>Riktigt stora datamängder
För att ladda upp datauppsättningar som sträcker sig i flera terabyte kan det ibland vara långsamt och dyrt att använda de metoder som beskrivs ovan. I sådana fall kan du använda alternativen nedan.

* **Använda Azure ExpressRoute**. Med Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och infrastruktur i dina lokaler. Detta ger ett tillförlitligt alternativ för att överföra stora mängder data. Mer information finns i [Azure ExpressRoute-dokumentation](../expressroute/expressroute-introduction.md).
* **"Offline" överföring av data**. Om det inte är möjligt att använda Azure ExpressRoute av någon anledning kan du använda [Azure Import/Export-tjänsten](../storage/common/storage-import-export-service.md) för att leverera hårddiskar med dina data till ett Azure-datacenter. Dina data överförs först till Azure Storage Blobbar. Du kan sedan använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) eller [AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md) för att kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1.

  > [!NOTE]
  > När du använder tjänsten Importera/exportera bör filstorlekarna på diskarna som du levererar till Azure-datacenter inte vara större än 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Bearbeta data som lagras i Gen1 för lagring av datasjö
När data är tillgängliga i Data Lake Storage Gen1 kan du köra analyser på dessa data med hjälp av de big data-program som stöds. För närvarande kan du använda Azure HDInsight och Azure Data Lake Analytics för att köra dataanalysjobb på data som lagras i Gen1 för datasjölagring.

![Analysera data i DataSjölagring Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analysera data i DataSjölagring Gen1")

Du kan titta på följande exempel.

* [Skapa ett HDInsight-kluster med Data Lake Storage Gen1 som lagring](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Hämta data från Data Lake Storage Gen1
Du kanske också vill hämta eller flytta data från Azure Data Lake Storage Gen1 för scenarier som:

* Flytta data till andra databaser för att samverka med dina befintliga databehandlingspipelier. Du kanske till exempel vill flytta data från Data Lake Storage Gen1 till Azure SQL Database eller lokal SQL Server.
* Hämta data till din lokala dator för bearbetning i IDE-miljöer medan du skapar programprototyper.

![Utgående data från Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Utgående data från Data Lake Storage Gen1")

I sådana fall kan du använda något av följande alternativ:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Du kan också använda följande metoder för att skriva ditt eget skript / program för att hämta data från Data Lake Storage Gen1.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualisera data i Gen1 för lagring av datasjö
Du kan använda en blandning av tjänster för att skapa visuella representationer av data som lagras i Data Lake Storage Gen1.

![Visualisera data i Gen1 för lagring av datasjö](./media/data-lake-store-data-scenarios/visualize-data.png "Visualisera data i Gen1 för lagring av datasjö")

* Du kan börja med att använda [Azure Data Factory för att flytta data från Data Lake Storage Gen1 till Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Därefter kan du [integrera Power BI med Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) för att skapa visuell representation av data.
