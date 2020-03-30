---
title: Datascenarier som involverar Azure Data Lake Storage Gen2 | Microsoft-dokument
description: Förstå de olika scenarier och verktyg som använder vilka data som kan intas, bearbetas, laddas ned och visualiseras i Data Lake Storage Gen2 (tidigare känt som Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b0ebe6cb505fa2a145dd3cbb94398912f2933a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77369709"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Använda Azure Data Lake Storage Gen2 för stora datakrav

Det finns fyra viktiga steg i stordatabehandling:

> [!div class="checklist"]
> * Intag av stora mängder data i ett datalager, i realtid eller i omgångar
> * Behandling av data
> * Ladda ner data
> * Visualisera data

I den här artikeln beskrivs alternativen och verktygen för varje bearbetningsfas.

En fullständig lista över Azure-tjänster som du kan använda med Azure Data Lake Storage Gen2 finns i [Integrera Azure Data Lake Storage med Azure-tjänster](data-lake-storage-integrate-with-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Övr data i Data Lake Storage Gen2

I det här avsnittet beskrivs de olika datakällorna och de olika sätt på vilka dessa data kan förtäras till ett Data Lake Storage Gen2-konto.

![Inta data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Inta data i Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc-data

Detta representerar mindre datauppsättningar som används för att prototypera ett stordataprogram. Det finns olika sätt att inta ad hoc-data beroende på datakällan. 

Här är en lista över verktyg som du kan använda för att använda ad hoc-data.

| Datakälla | Inta den med |
| --- | --- |
| Lokal dator |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)<br><br>[DistCp körs på HDInsight-kluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Strömmade data

Detta representerar data som kan genereras av olika källor såsom applikationer, enheter, sensorer, etc. Dessa data kan intas i Data Lake Storage Gen2 med en mängd olika verktyg. Dessa verktyg samlar vanligtvis in och bearbetar data händelse för händelse i realtid och skriver sedan händelserna i batchar i Data Lake Storage Gen2 så att de kan bearbetas ytterligare.

Här är en lista över verktyg som du kan använda för att inta strömmade data.

|Verktyg | Riktlinjer |
|---|--|
|Azure Stream Analytics|[Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Utgående till Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Azure HDInsight Storm | [Skriv till Apache Hadoop HDFS från Apache Storm på HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relationsdata

Du kan också hämta data från relationsdatabaser. Under en viss tidsperiod samlar relationsdatabaser in enorma mängder data som kan ge viktiga insikter om de bearbetas via en stordatapipeline. Du kan använda följande verktyg för att flytta sådana data till Data Lake Storage Gen2.

Här är en lista över verktyg som du kan använda för att få tillgång till relationsdata.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webbserverloggdata (ladda upp med anpassade program)

Den här typen av datauppsättning anropas specifikt eftersom analys av webbserverloggdata är ett vanligt användningsfall för stordataprogram och kräver stora volymer loggfiler som ska överföras till Data Lake Storage Gen2. Du kan använda något av följande verktyg för att skriva egna skript eller program för att ladda upp sådana data.

Här är en lista över verktyg som du kan använda för att använda webbserverloggdata.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

För att ladda upp webbserverloggdata, och även för att ladda upp andra typer av data (t.ex. sociala sentimentdata), är det en bra metod för att skriva egna anpassade skript /program eftersom det ger dig flexibiliteten att inkludera din datauppladdning komponent som en del av din större big data-program. I vissa fall kan den här koden ha formen av ett skript eller ett enkelt kommandoradsverktyg. I andra fall kan koden användas för att integrera stordatabehandling i ett affärsprogram eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster

De flesta HDInsight-klustertyper (Hadoop, HBase, Storm) stöder Data Lake Storage Gen2 som en datalagringslagringsplats. HDInsight-kluster får åtkomst till data från Azure Storage Blobbar (WASB). För bättre prestanda kan du kopiera data från WASB till ett Data Lake Storage Gen2-konto som är associerat med klustret. Du kan använda följande verktyg för att kopiera data.

Här är en lista över verktyg som du kan använda för att få in data som är associerade med HDInsight-kluster.

|Verktyg | Riktlinjer |
|---|--|
|Apache DistCp | [Använda DistCp för att kopiera data mellan Azure Storage Blobbar och Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy-verktyget | [Överföra data med AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopiera data till eller från Azure Data Lake Storage Gen2 med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i lokala kluster eller IaaS Hadoop-kluster

Stora mängder data kan lagras i befintliga Hadoop-kluster, lokalt på datorer som använder HDFS. Hadoop-klustren kan finnas i en lokal distribution eller vara i ett IaaS-kluster på Azure. Det kan finnas krav på att kopiera sådana data till Azure Data Lake Storage Gen2 för en engångsmetod eller på ett återkommande sätt. Det finns olika alternativ som du kan använda för att uppnå detta. Nedan finns en lista över alternativ och tillhörande kompromisser.

| Metod | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använda Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Storage Gen2 |[ADF stöder HDFS som datakälla](../../data-factory/connector-hdfs.md) |ADF ger direkt stöd för HDFS och förstklassig hantering och övervakning av slutkund |Kräver att datahanteringsgateway distribueras lokalt eller i IaaS-klustret |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Storage Gen2 med lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Storage Gen2 med: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp körs på HDInsight kluster](data-lake-storage-use-distcp.md)</li></ul> |Du kan använda verktyg med öppen källkod. |Flerstegsprocess som involverar flera tekniker |

### <a name="really-large-datasets"></a>Riktigt stora datamängder

För att ladda upp datauppsättningar som sträcker sig i flera terabyte kan det ibland vara långsamt och dyrt att använda de metoder som beskrivs ovan. I sådana fall kan du använda Azure ExpressRoute.  

Med Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och infrastruktur i dina lokaler. Detta ger ett tillförlitligt alternativ för att överföra stora mängder data. Mer information finns i [Azure ExpressRoute-dokumentationen](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Bearbeta data

När data är tillgängliga i Data Lake Storage Gen2 kan du köra analyser på dessa data med hjälp av de big data-program som stöds. 

![Analysera data i Gen2 för lagring av datasjö](./media/data-lake-storage-data-scenarios/analyze-data.png "Analysera data i Gen2 för lagring av datasjö")

Här är en lista över verktyg som du kan använda för att köra dataanalysjobb på data som lagras i Data Lake Storage Gen2.

|Verktyg | Riktlinjer |
|---|--|
|Azure HDInsight | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Snabbstart: Analysera data i Azure Data Lake Storage Gen2 med hjälp av Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Självstudiekurs: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualisera datan

Använd Power BI-kopplingen för att skapa visuella representationer av data som lagras i Data Lake Storage Gen2. Se [Analysera data i Azure Data Lake Storage Gen2 med hjälp av Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Ladda ner data

Du kanske också vill hämta eller flytta data från Azure Data Lake Storage Gen2 för scenarier som:

* Flytta data till andra databaser för att samverka med dina befintliga databehandlingspipelier. Du kanske till exempel vill flytta data från Data Lake Storage Gen2 till Azure SQL Database eller lokal SQL Server.

* Hämta data till din lokala dator för bearbetning i IDE-miljöer medan du skapar programprototyper.

![Utgående data från Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Utgående data från Data Lake Storage Gen2")

Här är en lista över verktyg som du kan använda för att hämta data från Data Lake Storage Gen2.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Använda DistCp för att kopiera data mellan Azure Storage Blobbar och Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Azure Lagringsutforskaren|[Använda Azure Storage Explorer för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|AzCopy-verktyget|[Överföra data med AzCopy- och Blob-lagring](../common/storage-use-azcopy-blobs.md)|
