---
title: Ladda upp stora datauppsättningar till Azure Data Lake Storage Gen1 – offlinemetoder
description: Använda tjänsten Import/Export för att kopiera data från Azure Blob-lagring till Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839301"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Använda Azure Import/Export-tjänsten för offlinekopia av data till DataSjölagringgend1

I den här artikeln får du lära dig hur du kopierar stora datauppsättningar (>200 GB) till Data Lake Storage Gen1 med hjälp av offlinekopieringsmetoder, till exempel [Azure Import/Export-tjänsten](../storage/common/storage-import-export-service.md). Den fil som används som exempel i den här artikeln är 339 420 860 416 byte eller cirka 319 GB på disken. Låt oss kalla den här filen 319GB.tsv.

Azure Import/Export-tjänsten hjälper dig att överföra stora mängder data säkrare till Azure Blob-lagring genom att skicka hårddiskar till ett Azure-datacenter.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du ha följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure-lagringskonto**.
* **Ett Azure Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Förbereda data

Innan du använder tjänsten Importera/exportera bryter du datafilen som ska överföras till **kopior som är mindre än 200 GB** stora. Importverktyget fungerar inte med filer som är större än 200 GB. I den här artikeln delar vi upp filen i bitar på 100 GB vardera. Du kan göra detta genom att använda [Cygwin](https://cygwin.com/install.html). Cygwin stöder Linux-kommandon. I så fall använder du följande kommando:

    split -b 100m 319GB.tsv

Den delade åtgärden skapar filer med följande namn.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Gör diskarna klara med data

Följ instruktionerna i [Använda Azure Import/Export-tjänsten](../storage/common/storage-import-export-service.md) (under avsnittet **Förbered dina enheter)** för att förbereda dina hårddiskar. Här är den övergripande sekvensen:

1. Skaffa en hårddisk som uppfyller kravet som ska användas för Azure Import/Export-tjänsten.
2. Identifiera ett Azure-lagringskonto där data kopieras när de har levererats till Azure-datacentret.
3. Använd [Azure Import/Export Tool](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), ett kommandoradsverktyg. Här är ett exempel utdrag som visar hur du använder verktyget.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Se [Använda Azure Import/Export-tjänsten](../storage/common/storage-import-export-service.md) för fler exempelkodavsnitt.
4. Med kommandot Föregående skapas en journalfil på den angivna platsen. Använd den här journalfilen för att skapa ett importjobb från [Azure-portalen](https://portal.azure.com).

## <a name="create-an-import-job"></a>Skapa ett importjobb

Du kan nu skapa ett importjobb med hjälp av instruktionerna i [Använda azure import/export-tjänsten](../storage/common/storage-import-export-service.md) (under avsnittet **Skapa jobbet Importera).** För det här importjobbet, med annan information, anger du även den journalfil som skapats när diskenheterna förbereds.

## <a name="physically-ship-the-disks"></a>Leverera diskarna fysiskt

Du kan nu fysiskt leverera diskarna till ett Azure-datacenter. Där kopieras data över till azure storage-blobbar som du angav när du skapade importjobbet. När du skapar jobbet kan du nu gå tillbaka till ditt importjobb och uppdatera spårningsnumret om du valde att ange spårningsinformationen senare.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Kopiera data från blobbar till Data Lake Storage Gen1

När statusen för importjobbet visar att det är slutfört kan du kontrollera om data är tillgängliga i azure storage-blobbar som du har angett. Du kan sedan använda en mängd olika metoder för att flytta dessa data från blobbar till Azure Data Lake Storage Gen1. Alla tillgängliga alternativ för att ladda upp data finns [i Intag av data i DataSjölagring gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

I det här avsnittet ger vi dig JSON-definitionerna som du kan använda för att skapa en Azure Data Factory-pipeline för kopiering av data. Du kan använda dessa JSON-definitioner från [Azure-portalen](../data-factory/tutorial-copy-data-portal.md) eller [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Länkad tjänst för källa (Azure Storage-blob)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Mållänkade tjänsten (Gen1 för lagring av datasjö)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Indatauppsättning

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Datauppsättning för utdata

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Pipeline (kopieringsaktivitet)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Mer information finns i [Flytta data från Azure Storage-blob till Azure Data Lake Storage Gen1 med Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Rekonstruera datafilerna i Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vi började med en fil som var 319 GB och bröt ned den i filer av mindre storlek så att den kunde överföras med hjälp av Azure Import/Export-tjänsten. Nu när data finns i Azure Data Lake Storage Gen1 kan vi rekonstruera filen till dess ursprungliga storlek. Du kan använda följande Azure PowerShell-cmdlets för att göra det.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Nästa steg

* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
