---
title: Data scenarier som involverar Azure Data Lake Storage Gen2 | Microsoft Docs
description: Förstå de olika scenarier och verktyg som använder vilka data som kan matas in, bearbetas, hämtas och visualiseras i Data Lake Storage Gen2 (tidigare kallat Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 68e1bcfeaa998b0698554fd93fd7ed2e88a29739
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143002"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Använda Azure Data Lake Storage Gen2 för Big data-krav

Det finns fyra viktiga steg i stor data bearbetning:

> [!div class="checklist"]
> * Mata in stora mängder data i ett data lager i real tid eller i batchar
> * Bearbetar data
> * Hämtar data
> * Visualisera data

Börja med att skapa ett lagrings konto och en behållare. Ge sedan åtkomst till data. De första avsnitten i den här artikeln hjälper dig att utföra dessa uppgifter. I de återstående avsnitten ska vi markera alternativen och verktygen för varje bearbetnings fas.

## <a name="create-a-data-lake-storage-gen2-account"></a>Skapa ett Data Lake Storage Gen2 konto

Ett Data Lake Storage Gen2 konto är ett lagrings konto som har ett hierarkiskt namn område. 

Information om hur du skapar [en finns i snabb start: Skapa ett Azure Data Lake Storage Gen2 lagrings](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)konto.

## <a name="create-a-container"></a>Skapa en container

Här är en lista med verktyg som du kan använda för att skapa en behållare för dina filer.

|Verktyg | Riktlinjer |
|---|--|
|Azure Lagringsutforskaren | [Skapa en behållare med hjälp av Storage Explorer](data-lake-storage-explorer.md#create-a-container) |
|AzCopy | [Skapa en BLOB-behållare eller fil resurs med hjälp av AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Kommando rads gränssnitt för Hadoop container (HDFS) med HDInsight |[Skapa en behållare med hjälp av HDFS med HDInsight](data-lake-storage-use-hdfs-data-lake-storage.md#create-a-container) |
|Kod i en Azure Databricks Notebook|[Skapa en lagrings konto behållare (Scala)](data-lake-storage-quickstart-create-databricks-account.md#create-storage-account-container) <br><br> [Skapa en behållare och montera den (python)](data-lake-storage-use-databricks-spark.md#create-a-container-and-mount-it)|

Det är enklast att skapa fil system med hjälp av Storage Explorer eller AzCopy. Det tar lite mer arbete att skapa fil system med hjälp av HDInsight och Databricks. Men om du planerar att använda HDInsight-eller Databricks-kluster för att bearbeta dina data ändå, kan du skapa dina kluster först och använda HDFS CLI för att skapa fil system.  

## <a name="grant-access-to-the-data"></a>Bevilja åtkomst till data

Ställ in rätt åtkomst behörigheter för ditt konto och data i ditt konto innan du börjar mata in data.

Det finns tre sätt att bevilja åtkomst:

* Tilldela en av dessa roller till en användare, grupp, användare hanterad identitet eller tjänstens huvud namn:

  [Storage BLOB data Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Storage BLOB data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Storage BLOB data-ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Använd en SAS-token (signatur för delad åtkomst).

* Använd en lagrings konto nyckel.

Den här tabellen visar hur du beviljar åtkomst för varje Azure-tjänst eller-verktyg.

|Verktyg | Bevilja åtkomst | Riktlinjer |
|---|--|---|
|Storage Explorer| Tilldela en roll till användare och grupper | [Tilldela roller som administratör och icke-administratör till användare med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Tilldela en roll till användare och grupper <br>**eller**<br> Använda en SAS-token| [Tilldela roller som administratör och icke-administratör till användare med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Skapa enkelt en SAS för att ladda ned en fil från Azure Storage – med Azure Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Tilldela en roll till en användare som tilldelats en hanterad identitet | [Skapa ett HDInsight-kluster med Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Tilldela en roll till en användardefinierad hanterad identitet<br>**eller**<br> Tilldela en roll till ett huvud namn för tjänsten<br>**eller**<br> Använd en lagrings konto nyckel | [Egenskaper för länkad tjänst](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Tilldela en roll till en användare som tilldelats en hanterad identitet | [Skapa ett HDInsight-kluster med Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Tilldela en roll till ett huvud namn för tjänsten | [Anvisningar: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Information om hur du beviljar åtkomst till vissa filer och mappar finns i de här artiklarna.

* [Ange behörigheter för fil-och katalog nivå med Azure Storage Explorer med Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Åtkomst kontrol listor på filer och kataloger](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Information om hur du konfigurerar andra säkerhets aspekter finns [Azure Data Lake Storage Gen2 säkerhets guide](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Mata in data

I det här avsnittet beskrivs de olika data källorna och de olika sätt som data kan matas in i ett Data Lake Storage Gen2-konto.

Mata ![in data i data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Mata in data i data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc-data

Detta representerar mindre data uppsättningar som används för prototypering av stor data program. Det finns olika sätt att mata in ad hoc-data beroende på data källan. 

Här är en lista med verktyg som du kan använda för att mata in ad hoc-data.

| Datakälla | Mata in den med |
| --- | --- |
| Lokal dator |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy-verktyg](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy-verktyg](../common/storage-use-azcopy-v10.md)<br><br>[DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Strömmade data

Detta representerar data som kan genereras av olika källor, till exempel program, enheter, sensorer osv. Dessa data kan matas in i Data Lake Sorage Gen2 med en rad olika verktyg. Dessa verktyg samlar vanligt vis in och bearbetar data baserat på händelse i real tid och skriver sedan händelserna i batchar till Data Lake Storage Gen2 så att de kan bearbetas ytterligare.

Här är en lista med verktyg som du kan använda för att mata in strömmade data.

|Verktyg | Riktlinjer |
|---|--|
|Azure HDInsight Storm | [Skriv till Apache Hadoop HDFS från Apache Storm på HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relationsdata

Du kan också källdata från Relations databaser. Under en viss tids period samlar Relations databaser in enorma data mängder som kan tillhandahålla viktiga insikter om de bearbetas via en stor datapipeline. Du kan använda följande verktyg för att flytta sådana data till Data Lake Storage Gen2.

Här är en lista med verktyg som du kan använda för att mata in relations data.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Logg data för webb server (Ladda upp med anpassade program)

Den här typen av data uppsättning kallas särskilt för att analysera webb serverns loggdata är ett vanligt användnings fall för stora data program och kräver att stora mängder loggfiler laddas upp till Data Lake Storage Gen2. Du kan använda något av följande verktyg för att skriva egna skript eller program för att överföra sådana data.

Här är en lista med verktyg som du kan använda för att mata in data från webb server loggar.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

För att överföra data från webb server loggar och även för överföring av andra typer av data (t. ex. sociala sentiment-data) är det en lämplig metod för att skriva egna anpassade skript/program eftersom det ger dig flexibiliteten att inkludera data överförings komponenten som en del av ditt större stora data program. I vissa fall kan den här koden ha formen av ett skript eller ett enkelt kommando rads verktyg. I andra fall kan koden användas för att integrera stor data bearbetning i ett affärs program eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster

De flesta typer av HDInsight-kluster (Hadoop, HBase, Storm) stöder Data Lake Storage Gen2 som lagrings plats för data lagring. HDInsight-kluster får åtkomst till data från Azure Storage blobbar (WASB). För bättre prestanda kan du kopiera data från WASB till ett Data Lake Storage Gen2-konto som är kopplat till klustret. Du kan använda följande verktyg för att kopiera data.

Här är en lista med verktyg som du kan använda för att mata in data som är associerade med HDInsight-kluster.

|Verktyg | Riktlinjer |
|---|--|
|Apache DistCp | [Använd DistCp för att kopiera data mellan Azure Storage blobbar och Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy-verktyg | [Överföra data med AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Kopiera data till eller från Azure Data Lake Storage Gen2 med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i lokala eller IaaS Hadoop-kluster

Stora mängder data kan lagras i befintliga Hadoop-kluster, lokalt på datorer som använder HDFS. Hadoop-klustren kan finnas i en lokal distribution eller vara i ett IaaS-kluster på Azure. Det kan finnas krav på att kopiera sådana data till Azure Data Lake Storage Gen2 för ett engångs sätt eller på ett återkommande sätt. Det finns olika alternativ som du kan använda för att åstadkomma detta. Nedan visas en lista med alternativ och tillhör ande kompromisser.

| Metoden | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använd Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Storage Gen2 |[ADF stöder HDFS som en data Källa](../../data-factory/connector-hdfs.md) |ADF tillhandahåller direkt support för HDFS och den första klassens slut punkt till slut punkt-hantering och övervakning |Kräver att Data Management Gateway distribueras lokalt eller i IaaS-klustret |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Storage Gen2 med lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Storage Gen2 med: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy-verktyg](../common/storage-use-azcopy-v10.md)</li><li>[Apache-DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)</li></ul> |Du kan använda verktyg med öppen källkod. |Multi-Step-process som omfattar flera tekniker |

### <a name="really-large-datasets"></a>Faktiskt stora data uppsättningar

För att ladda upp data uppsättningar som sträcker sig över flera terabyte, kan det ibland vara långsamt och kostsamt att använda de metoder som beskrivs ovan. I sådana fall kan du använda Azure-ExpressRoute.  

Med Azure ExpressRoute kan du skapa privata anslutningar mellan Azures Data Center och infrastruktur på din plats. Detta ger ett tillförlitligt alternativ för överföring av stora mängder data. Läs mer i [Azure ExpressRoute-dokumentationen](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Bearbeta data

När data är tillgängliga i Data Lake Storage Gen2 kan du köra analyser av dessa data med hjälp av Big data-program som stöds. 

![Analysera data i data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analysera data i data Lake Storage Gen2")

Här är en lista med verktyg som du kan använda för att köra data analys jobb på data som lagras i Data Lake Storage Gen2.

|Verktyg | Riktlinjer |
|---|--|
|Azure HDInsight | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Snabbstart: Analysera data i Azure Data Lake Storage Gen2 med Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Självstudier: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualisera datan

Du kan använda en blandning av tjänster för att skapa visuella representationer av data som lagras i Data Lake Storage Gen2.

![Visualisera data i data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualisera data i data Lake Storage Gen2")

* Du kan börja med att använda [Azure Data Factory för att flytta data från data Lake Storage Gen2 till Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Därefter kan du [integrera Power BI med Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) för att skapa visuell representation av data.

## <a name="download-the-data"></a>Hämta data

Du kanske också vill hämta eller flytta data från Azure Data Lake Storage Gen2 för scenarier som:

* Flytta data till andra databaser till gränssnitt med dina befintliga pipeliner för data bearbetning. Du kanske till exempel vill flytta data från Data Lake Storage Gen2 till Azure SQL Database eller lokalt SQL Server.

* Ladda ned data till den lokala datorn för bearbetning i IDE-miljöer när du skapar program prototyper.

![Utgående data från data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Utgående data från data Lake Storage Gen2")

Här är en lista med verktyg som du kan använda för att hämta data från Data Lake Storage Gen2.

|Verktyg | Riktlinjer |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Använd DistCp för att kopiera data mellan Azure Storage blobbar och Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
