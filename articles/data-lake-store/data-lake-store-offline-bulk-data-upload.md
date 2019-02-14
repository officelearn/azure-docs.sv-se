---
title: Överföra stora mängder data till Azure Data Lake Storage Gen1 genom att använda offline metoder | Microsoft Docs
description: Verktyget AdlCopy för att kopiera data från Azure Storage BLOB till Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 98cad0873c4ba687948dc404abc19655319bdc36
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232275"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Använda Azure Import/Export-tjänsten för offlinekopia av data till Azure Data Lake Storage Gen1
I den här artikeln får du veta hur du kopierar enorma datamängder (> 200 GB) i Azure Data Lake Storage Gen1 med offlinekopia metoder som de [tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md). Filen används som exempel i den här artikeln är mer specifikt 339,420,860,416 byte eller cirka 319 GB på disken. Vi kan kalla den här filen 319GB.tsv.

Azure Import/Export-tjänsten hjälper dig att överföra stora mängder data mer säkert till Azure Blob storage genom att skicka hårddiskenheter till ett Azure-datacenter.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar måste du ha följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure storage-konto**.
* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Förbereda data

Innan du använder Import/Export-tjänsten kan dela filen som ska överföras **till kopior som är mindre än 200 GB** i storlek. Importverktyget fungerar inte med filer som är större än 200 GB. I de här självstudierna dela vi filen i segment på 100 GB vardera. Du kan göra detta med hjälp av [Cygwin](https://cygwin.com/install.html). Cygwin har stöd för Linux-kommandon. I det här fallet använder du följande kommando:

    split -b 100m 319GB.tsv

Split-åtgärden skapar filer med följande namn.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Förbereda diskar med data
Följ instruktionerna i [med hjälp av tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) (under den **förbereda dina enheter** avsnitt) att förbereda dina hårddiskar. Här är den övergripande sekvensen:

1. Skaffa en hårddisk som uppfyller kraven som ska användas för tjänsten Azure Import/Export.
2. Identifiera Azure-lagringskonton dit data ska kopieras när det har levererats till Azure-datacentret.
3. Använd den [Azure Import/Export-verktyget](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), ett kommandoradsverktyg. Här är ett exempel kodfragment som visar hur du använder verktyget.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Se [med hjälp av tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) för fler exempel kodfragment.
4. Föregående kommando skapar en journalfil på den angivna platsen. Använda den här journalfil för att skapa ett importjobb från den [Azure-portalen](https://portal.azure.com).

## <a name="create-an-import-job"></a>Skapa ett importjobb
Nu kan du skapa ett importjobb med hjälp av anvisningarna i [med hjälp av tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) (under den **skapa importjobbet** avsnittet). För den här importjobb med annan information dessutom journalfil som skapats under förberedelserna för diskenheter.

## <a name="physically-ship-the-disks"></a>Fysiskt skicka tillbaka diskarna
Du kan nu fysiskt leverera diskarna till ett Azure-datacenter. Där kan kopieras data över till Azure Storage-blob som du angav när du skapade importjobbet. Dessutom när du skapar jobbet, kan om du har valt för att tillhandahålla spårningsinformation senare, du nu gå tillbaka till din importjobbet och uppdatera Spårningsnumret.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiera data från Azure Storage BLOB till Azure Data Lake Storage Gen1
När status för importjobbet visar att den är slutförd, kan du kontrollera om data finns i Azure Storage-blobbar som du har angett. Du kan sedan använda olika metoder för att flytta data från blobarna till Azure Data Lake Storage Gen1. Alla tillgängliga alternativ för att ladda upp data, se [mata in data i Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

I det här avsnittet ger vi dig med JSON-definitioner som du kan använda för att skapa ett Azure Data Factory-pipeline för att kopiera data. Du kan använda dessa JSON-definitioner från den [Azure-portalen](../data-factory/tutorial-copy-data-portal.md) eller [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Källa länkade tjänsten (Azure Storage blob)
```
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

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>Rikta länkade tjänsten (Azure Data Lake Storage Gen1)
```
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
### <a name="input-data-set"></a>Inkommande datauppsättning
```
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
### <a name="output-data-set"></a>Datauppsättningen för utdata
```
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
### <a name="pipeline-copy-activity"></a>Pipelinen (kopieringsaktiviteten)
```
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
Mer information finns i [flytta data från Azure Storage blob till Azure Data Lake Storage Gen1 med Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Rekonstruera datafiler i Azure Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vi igång med en fil som 319 GB och avbrutits ner den till filer som är mindre storlek så att det kunde överföras med hjälp av Azure Import/Export-tjänsten. Nu när data är i Azure Data Lake Storage Gen1 kan rekonstruera vi filen till sin ursprungliga storlek. Du kan använda följande Azure PowerShell-cmdletar för att göra detta.

```
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
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
