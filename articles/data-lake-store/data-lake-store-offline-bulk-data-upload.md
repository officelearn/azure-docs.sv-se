---
title: Överföra stora mängder data till Data Lake Store med hjälp av offline metoder | Microsoft Docs
description: Verktyget AdlCopy för att kopiera data från Azure Storage BLOB till Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: ee6f4ab1ac5892536d7f419c198158dc34d6f49e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Använda tjänsten Azure Import/Export för offline kopia av data till Data Lake Store
I den här artikeln får du lära dig hur att kopiera stora datamängder (> 200 GB) till ett Azure Data Lake Store med hjälp av offline kopiera metoder som den [Azure Import/Export service](../storage/common/storage-import-export-service.md). Den fil som används som exempel i den här artikeln är särskilt 339,420,860,416 byte eller om 319 GB på disken. Vi ska anropa den här filen 319GB.tsv.

Tjänsten Azure Import/Export hjälper dig att överföra stora mängder data säkrare till Azure Blob storage med leverans hårddiskar till ett Azure-datacenter.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar måste du ha följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure storage-konto**.
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Förbereder data

Innan du använder tjänsten Import/Export kan dela filen ska överföras **till kopiorna som är mindre än 200 GB** i storlek. Importverktyget fungerar inte med filer som är större än 200 GB. I den här självstudiekursen kommer vi dela upp filen mängder 100 GB. Du kan göra detta med hjälp av [Cygwin](https://cygwin.com/install.html). Cygwin har stöd för Linux-kommandon. I det här fallet använder du följande kommando:

    split -b 100m 319GB.tsv

Split-åtgärden skapar filer med följande namn.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Förbereda diskar med data
Följ instruktionerna i [med hjälp av tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) (under den **förbereda dina enheter** avsnitt) att förbereda dina hårddiskar. Här är den övergripande sekvensen:

1. Skaffa en hårddisk som uppfyller som ska användas för tjänsten Azure Import/Export.
2. Identifiera ett Azure storage-konto där data ska kopieras när den har levererats till Azure-datacenter.
3. Använd den [Azure Import/Export verktyget](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), ett kommandoradsverktyg. Här är ett exempel kodavsnitt som visar hur du använder verktyget.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Se [med hjälp av tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) för flera exempel kodavsnitt.
4. Föregående kommando skapar en journal-fil på den angivna platsen. Använda filen ändringsjournalen för att skapa ett importjobb från den [Azure-portalen](https://portal.azure.com).

## <a name="create-an-import-job"></a>Skapa ett importjobb
Du kan nu skapa ett importjobb med hjälp av anvisningarna i [med hjälp av tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) (under den **skapa importjobbet** avsnitt). För det här importjobbet med annan information, tillhandahålla journal-fil som skapats under förberedelserna för diskenheter.

## <a name="physically-ship-the-disks"></a>Fysiskt leverera diskar
Du kan nu fysiskt leverera diskar till en Azure-datacenter. Där kopieras data över till Azure Storage-blobbar som du angav när du skapar importjobbet. Dessutom när du skapar jobbet, kan om du har valt för att tillhandahålla spårningsinformation senare, du nu gå tillbaka till din importjobbet och uppdatera Spårningsnumret.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Kopiera data från Azure Storage BLOB till Azure Data Lake Store
När status för importjobbet visar att den är klar, kan du kontrollera om data är tillgängliga i Azure Storage-blobbar som du har angett. Du kan sedan använda olika metoder för att flytta data från blobbarna till Azure Data Lake Store. Alla tillgängliga alternativ för överföring av data, se [mata in data i Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

I det här avsnittet får du med JSON-definitioner som du kan använda för att skapa ett Azure Data Factory-pipelinen för att kopiera data. Du kan använda dessa JSON-definitioner från den [Azure-portalen](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), eller [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Källan länkad tjänst (Azure Storage blob)
````
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
````

### <a name="target-linked-service-azure-data-lake-store"></a>Rikta länkad tjänst (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Inkommande datauppsättning
````
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
````
### <a name="output-data-set"></a>Datamängd för utdata
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (kopieringsaktiviteten)
````
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
````
Mer information finns i [flytta data från Azure Storage blob till Azure Data Lake Store med hjälp av Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Rekonstruera datafiler i Azure Data Lake Store
Vi igång med en fil som tidigare 319 GB och bröt mot den ned till filer med en mindre storlek så att den kan överföras med hjälp av Azure Import/Export-tjänsten. Nu när data i Azure Data Lake Store, rekonstruera vi filen till den ursprungliga storleken. Du kan använda följande Azure PowerShell cmldts gör.

````
# Login to our account
Connect-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
