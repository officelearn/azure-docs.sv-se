---
title: Datascenarier som omfattar Azure Data Lake Storage Gen2 | Microsoft Docs
description: Förstå de olika scenarier och verktyg med vilken data kan inhämtas, bearbetas, hämtas och visualiseras i Data Lake Storage Gen2 (kallades tidigare Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: 9deaa2f1e381dffbd85b0ee150c5782098a9db6b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006819"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Med hjälp av Azure Data Lake Storage Gen2 för stordatakrav

Det finns fyra viktiga steg i stordatabearbetning:

> [!div class="checklist"]
> * Mata in stora mängder data i ett datalager i realtid eller i batchar
> * Bearbetning av data
> * Hämta data
> * Visualisera dina data

Börja med att skapa ett lagringskonto och ett filsystem. Sedan bevilja åtkomst till data. De första avsnitt i den här artikeln hjälper dig att slutföra dessa uppgifter. I de återstående avsnitten vi belyser särskilt alternativ och verktyg för varje fas för bearbetning.

## <a name="create-a-data-lake-storage-gen2-account"></a>Skapa ett Data Lake Storage Gen2-konto

Ett Data Lake Storage Gen2-konto är ett lagringskonto som har ett hierarkiskt namnområde. 

Om du vill skapa en [Snabbstart: Skapa ett lagringskonto i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Skapa ett filsystem

En *filsystem* är en behållare för mappar och filer. Du behöver minst en av dem att börja föra in data i ditt storage-konto.  Här är en lista över verktyg som du kan använda för att skapa dem.

|Verktyg | Riktlinjer |
|---|--|
|Azure Lagringsutforskaren | [Skapa ett filsystem med Storage Explorer](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-filesystem) |
|AzCopy | [Skapa en Blob-behållare eller filresurs med hjälp av AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-blob-container-or-file-share)|
|Hadoop-filer System (HDFS) kommandoradsgränssnitt (CLI) med HDInsight |[Skapa ett filsystem med hjälp av HDFS med HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Koden i en Azure Databricks-anteckningsbok|[Skapa ett filsystem för storage-konto (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Skapa ett filsystem och montera den (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

Det är enklast att skapa filsystem med Lagringsutforskaren eller AzCopy. Det tar lite mer arbete att skapa filsystem med hjälp av HDInsight och Databricks. Men om du planerar att använda HDInsight eller Databricks-kluster för att bearbeta dina data ändå, kan sedan du skapa dina kluster först och använder HDFS-CLI till din skapa-filsystem.  

## <a name="grant-access-to-the-data"></a>Bevilja åtkomst till data

Ställa in behörigheter för lämplig åtkomst till ditt konto och data i ditt konto innan du börjar att mata in data.

Det finns tre sätt att bevilja åtkomst:

* Tilldela någon av dessa roller till en användare, grupp, användarhanterade identitet eller tjänstens huvudnamn:

  [Storage Blob Data-läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)

  [Storage Blob Data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)

  [Storage Blob Data-ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)

* Använda en signatur för delad åtkomst (SAS)-token.

* Använd en lagringskontonyckel.

Den här tabellen visar hur du beviljar åtkomst för varje Azure-tjänst eller -verktyget.

|Verktyg | Att bevilja åtkomst | Riktlinjer |
|---|--|---|
|Storage Explorer| Tilldela en roll till användare och grupper | [Tilldela användare med Azure Active Directory rollerna Administratör och icke-administratör](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Tilldela en roll till användare och grupper <br>**eller**<br> Använda en SAS-token| [Tilldela användare med Azure Active Directory rollerna Administratör och icke-administratör](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Enkelt skapa en SAS för att hämta en fil från Azure Storage – med Azure Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Tilldela en roll till en hanterad Användartilldelad identitet | [Skapar ett HDInsight-kluster med Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Tilldela en roll till en användare tilldelas-hanterad identitet<br>**eller**<br> Tilldela en roll till tjänstens huvudnamn<br>**eller**<br> Använd en lagringskontonyckel | [Länkade tjänstegenskaper](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Tilldela en roll till en hanterad Användartilldelad identitet | [Skapar ett HDInsight-kluster med Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Tilldela en roll till tjänstens huvudnamn | [Anvisningar: Använda portalen för att skapa en Azure AD-program och tjänstens huvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Se följande artiklar om du vill bevilja åtkomst till specifika filer och mappar.

* [Ange fil- och behörigheter på kolumnnivå med Azure Storage Explorer med Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Åtkomstkontrollistor för filer och kataloger](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Läs om hur du konfigurerar andra aspekter av säkerhet i [säkerhetsguiden för Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Mata in data

Det här avsnittet beskrivs de olika datakällor och de olika sätt som dessa data kan hämtas till ett Data Lake Storage Gen2-konto.

![Mata in data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "mata in data i Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc-data

Detta representerar mindre datamängder som används för prototyper ett program för stordata. Det finns flera olika sätt att mata in ad hoc-data, beroende på den datakällan. 

Här är en lista över verktyg som du kan använda för att mata in ad hoc-data.

| Datakälla | Mata in den med hjälp av |
| --- | --- |
| Lokal dator |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)<br><br>[DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Strömmade data

Detta representerar data som genereras av olika källor, till exempel program, enheter, sensorer, osv. Dessa data kan hämtas till Data Lake Sorage Gen2 av en rad olika verktyg. De här verktygen kommer vanligtvis samla in och bearbeta data regelbundet en händelse med händelse i realtid, och sedan skriva händelser i batchar i Data Lake Storage Gen2 så att de kan bearbetas ytterligare.

Här är en lista över verktyg som du kan använda för att mata in strömmade data.

|Verktyg | Riktlinjer |
|---|--|
|Azure HDInsight Storm | [Skriva till Apache Hadoop HDFS från Apache Storm på HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relationsdata

Du kan också styra data från relationsdatabaser. Samla in stora mängder data som kan få viktiga insikter om bearbetas via en big datapipeline under en viss tidsperiod, relationsdatabaser. Du kan använda följande verktyg för att flytta sådana data till Data Lake Storage Gen2.

Här är en lista över verktyg som du kan använda för att mata in relationella data.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopiera aktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Loggdata för Web server (överföring med hjälp av anpassade program)

Den här typen av datauppsättning framhävs specifikt eftersom analys av loggdata för web server är ett vanligt användningsfall för stordata-program som kräver stora mängder loggfiler som ska överföras till Data Lake Storage Gen2. Du kan använda någon av följande verktyg för att skriva egna skript eller program för att ladda upp dessa data.

Här är en lista över verktyg som du kan använda för att mata in loggdata för Web server.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopiera aktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

För att ladda upp loggdata för web server, och även för att ladda upp andra typer av data (t.ex. social sentiment data), är det en bra metod för att skriva din egen anpassade skript/program eftersom det ger dig möjlighet att ta med dina data laddar du upp komponenten som en del av ditt större stordata-program. I vissa fall kan den här koden ske i form av ett skript eller ett enkelt kommandoradsverktyg. I annat fall kan koden användas för att integrera bearbetning av stordata i ett affärsprogram eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster

Klustertyper för de flesta HDInsight (Hadoop, HBase, Storm) stöder Data Lake Storage Gen2 som en databas för lagring av data. HDInsight-kluster åtkomst till data från Azure Storage BLOB-objekt (WASB). Av prestandaskäl kan kopiera du data från WASB till ett Data Lake Storage Gen2-konto som är kopplat till klustret. Du kan använda följande verktyg för att kopiera data.

Här är en lista över verktyg som du kan använda för att mata in data som är associerade med HDInsight-kluster.

|Verktyg | Riktlinjer |
|---|--|
|Apache DistCp | [Använd DistCp för att kopiera data mellan Azure Storage-Blobbar och Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy-verktyget | [Överföra data med AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopiera data till och från Azure Data Lake Storage Gen2 med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i en lokal eller IaaS Hadoop kluster

Stora mängder data kan lagras i befintliga Hadoop-kluster lokalt på datorer med hjälp av HDFS. Hadoop-kluster kan vara i en lokal distribution eller kan vara i ett IaaS-kluster på Azure. Det kan finnas krav för att kopiera sådana data till Azure Data Lake Storage Gen2 för en direkt metod eller i ett återkommande sätt. Det finns olika alternativ som du kan använda för att uppnå detta. Nedan visas en lista över alternativ och associerade avvägningarna.

| Metoden | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använd Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Storage Gen2 |[ADF stöder HDFS som en datakälla](../../data-factory/connector-hdfs.md) |ADM ger ut inbyggda stödet för HDFS och första klassens från slutpunkt till slutpunkt-hantering och övervakning |Kräver Data Management Gateway distribueras lokalt eller i IaaS-kluster |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Storage Gen2 med lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Storage Gen2 med: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy-verktyget](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)</li></ul> |Du kan använda open source-verktyg. |Process i flera steg som omfattar flera tekniker |

### <a name="really-large-datasets"></a>Väldigt stora datauppsättningar

För att ladda upp datauppsättningar som intervall i flera terabyte kan med hjälp av de metoder som beskrivs ovan ibland vara långsam och dyrt. I sådana fall kan använda du Azure ExpressRoute.  

Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och infrastrukturen i era lokala. Detta ger en pålitlig alternativ för överföring av stora mängder data. Mer information finns i [dokumentation om Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Bearbeta data

När data är tillgängliga i Data Lake Storage Gen2 kan du köra analys på dessa data med hjälp av stöds stordata-program. 

![Analysera data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "analysera data i Data Lake Storage Gen2")

Här är en lista över verktyg som du kan använda för att köra data analysis-jobb på data som lagras i Data Lake Storage Gen2.

|Verktyg | Riktlinjer |
|---|--|
|Azure HDInsight | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Snabbstart: Analysera data i Azure Data Lake Storage Gen2 med hjälp av Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Självstudier: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualisera datan

Du kan använda en blandning av tjänster för att skapa visuella representationer av data som lagras i Data Lake Storage Gen2.

![Visualisera data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "visualisera data i Data Lake Storage Gen2")

* Du kan börja med hjälp av [Azure Data Factory för att flytta data från Data Lake Storage Gen2 till Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Därefter kan du [integrera Power BI med Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) att skapa visuell representation av data.

## <a name="download-the-data"></a>Hämta data

Du kanske också vill ladda ned eller flytta data från Azure Data Lake Storage Gen2 för scenarier som:

* Flytta data till andra databaser gränssnittet med dina befintliga pipelines för databearbetning. Du kanske exempelvis vill flytta data från Data Lake Storage Gen2 till Azure SQL Database eller en lokal SQL Server.

* Ladda ned data till den lokala datorn för bearbetning i IDE-miljöer när du skapar program prototyper.

![Egress data från Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Egress data från Data Lake Storage Gen2")

Här är en lista över verktyg som du kan använda för att hämta data från Data Lake Storage Gen2.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopiera aktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCop | [Använd DistCp för att kopiera data mellan Azure Storage-Blobbar och Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
