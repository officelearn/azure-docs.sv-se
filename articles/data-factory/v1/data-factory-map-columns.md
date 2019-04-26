---
title: Mappning av kolumner för datauppsättningar i Azure Data Factory | Microsoft Docs
description: Lär dig hur du mappar källkolumner till målkolumner.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1b009ac2ca42e9804b88989b55b2e73524732550
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238110"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mappa kolumner för datauppsättningar för källa till målkolumner för datauppsättningar
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Kolumnmappningen kan användas för att ange hur kolumner som anges i ”strukturen” för mappning av källan till kolumner anges i ”strukturen” för mottagaren tabell. Den **columnMapping** egenskapen är tillgänglig i den **typeProperties** delen av kopieringsaktiviteten.

Kolumnmappningen har stöd för följande scenarier:

* Alla kolumner i datauppsättning källstrukturen mappas till alla kolumner i sink dataset-struktur.
* En delmängd med kolumner i datauppsättning källstrukturen mappas till alla kolumner i sink dataset-struktur.

Här följer felvillkor som resulterar i ett undantag:

* Färre kolumner eller fler kolumner i ”strukturen” för mottagaren tabell än anges i mappningen.
* Duplicera mappning.
* SQL-frågeresultat har inte ett kolumnnamn som anges i mappningen.

> [!NOTE]
> Följande exempel är avsedda för Azure SQL och Azure Blob, men kan användas för alla datalager som har stöd för rektangulära datauppsättningar. Justera datauppsättning och definitioner för länkad tjänst i exempel så att den pekar till data i relevanta datakällan.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exempel 1 – kolumnen från Azure SQL-mappning till Azure-blob
I det här exemplet indatatabellen har en struktur och den pekar på en SQL-tabell i en Azure SQL database.

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

I det här exemplet utdatatabellen har en struktur och den pekar på en blob i Azure blob storage.

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

Följande JSON definierar en Kopieringsaktivitet i en pipeline. Kolumner från källan som är mappade till kolumnerna i mottagare (**columnMappings**) med hjälp av den **Translator** egenskapen.

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
**Kolumnen mappning flöde:**

![Kolumnen mappning flöde](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exempel 2 – kolumnen mappning med SQL-fråga från Azure SQL till Azure-blob
I det här exemplet används en SQL-fråga för att extrahera data från Azure SQL i stället för att ange tabellnamnet och kolumnnamnen i avsnittet ”struktur”. 

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
Resultatet av frågan är i det här fallet först mappade till kolumnerna som anges i ”strukturen” för källan. Därefter är kolumner från källa ”struktur” mappade till kolumnerna i kanalmottagare ”struktur” med regler som anges i columnMappings.  Anta att frågan returnerar 5 kolumner, två fler kolumner än de som anges i ”strukturen” för källan.

**Kolumnen mappning flöde**

![Kolumnen mappning av flow-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Nästa steg
Se artikeln en genomgång om hur du använder Kopieringsaktivitet: 

- [Kopiera data från Blob Storage till SQL-databas](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
