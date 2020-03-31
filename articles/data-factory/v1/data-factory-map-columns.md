---
title: Mappa datauppsättningskolumner i Azure Data Factory
description: Lär dig hur du mappar källkolumner till målkolumner.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6eb7012e28319ee6cc86de5ee56090743d681068
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923874"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mappa källdatauppsättningskolumner till måldatauppsättningskolumner
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Kolumnmappning kan användas för att ange hur kolumner som anges i "strukturen" för källtabellmapp mappning till kolumner som anges i "struktur" av sink tabellen. Egenskapen **columnMapping** är tillgänglig i avsnittet **typeProperties** i kopieringsaktiviteten.

Kolumnmappning stöder följande scenarier:

* Alla kolumner i källdatauppsättningsstrukturen mappas till alla kolumner i sink-datauppsättningsstrukturen.
* En delmängd av kolumnerna i källdatauppsättningsstrukturen mappas till alla kolumner i sink-datauppsättningsstrukturen.

Följande är felvillkor som resulterar i ett undantag:

* Antingen färre kolumner eller fler kolumner i "strukturen" i sinktabellen än vad som anges i mappningen.
* Dubblettmappning.
* SQL-frågeresultatet har inget kolumnnamn som anges i mappningen.

> [!NOTE]
> Följande exempel är för Azure SQL och Azure Blob men gäller för alla datalager som stöder rektangulära datauppsättningar. Justera datauppsättning och länkade tjänstedefinitioner i exempel så att de pekar på data i relevant datakälla.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exempel 1 – kolumnmappning från Azure SQL till Azure blob
I det här exemplet har indatatabellen en struktur och pekar på en SQL-tabell i en Azure SQL-databas.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

I det här exemplet har utdatatabellen en struktur och pekar på en blob i en Azure-bloblagring.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Följande JSON definierar en kopieringsaktivitet i en pipeline. Kolumnerna från källan mappas till kolumner i sink **(columnMappings**) med hjälp av egenskapen **Translator.**

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Kolumnmappningsflöde:**

![Mappningsflöde för kolumn](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exempel 2 – kolumnmappning med SQL-fråga från Azure SQL till Azure blob
I det här exemplet används en SQL-fråga för att extrahera data från Azure SQL i stället för att bara ange tabellnamnet och kolumnnamnen i avsnittet "struktur". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
I det här fallet mappas frågeresultaten först till kolumner som anges i "struktur" av källan. Därefter mappas kolumnerna från källans "struktur" till kolumner i sink "struktur" med regler som anges i kolumnMappningar.  Anta att frågan returnerar 5 kolumner, ytterligare två kolumner än de som anges i källans "struktur".

**Mappningsflöde för kolumn**

![Kolumnmappningsflöde-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Nästa steg
Se artikeln för en handledning om hur du använder Kopiera aktivitet: 

- [Kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
