---
title: Mappa dataset kolumner i Azure Data Factory | Microsoft Docs
description: "Lär dig hur du mappar källkolumner till mål-kolumner."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ae092308c5d2579a5b513657787ae6dbbfadaf05
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mappa källkolumner dataset till mål-dataset kolumner
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). 

Kolumnmappningen kan användas för att ange hur kolumner som anges i ”strukturen” för mappning av källan till kolumner anges i ”struktur” sink-tabellen. Den **columnMapping** egenskapen är tillgänglig i den **typeProperties** avsnittet för aktiviteten kopiera.

Kolumnmappningen har stöd för följande scenarier:

* Alla kolumner i datauppsättning källstrukturen mappas till alla kolumner i datauppsättningsstrukturen mottagare.
* En delmängd med kolumner i datauppsättningsstrukturen källan mappas till alla kolumner i datauppsättningsstrukturen mottagare.

Följande är felvillkor som resulterar i ett undantag:

* Färre kolumner eller fler kolumner i ”struktur” sink tabell än anges i mappningen.
* Duplicera mappning.
* Resultat av SQL-fråga har inte ett kolumnnamn som anges i mappningen.

> [!NOTE]
> Följande exempel gäller för alla datalager som har stöd för rektangulär datauppsättningar är för Azure SQL och Azure Blob. Justera dataset och länkade tjänstdefinitioner i exempel så att den pekar till data i den aktuella datakällan.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exempel 1 – kolumnen mappning från Azure SQL till Azure-blob
I det här exemplet indatatabellen har en struktur och pekar på en SQLtabell i Azure SQL-databas.

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

I det här exemplet utdatatabellen har en struktur och att den leder till en blobb i ett Azure blob storage.

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

Följande JSON definierar en kopia aktivitet i en pipeline. Kolumner från källan som är mappade till kolumnerna i kanalmottagare (**columnMappings**) med hjälp av den **översättare** egenskapen.

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

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exempel 2 – kolumnen mappning med SQL-frågan från Azure SQL till Azure-blob
I det här exemplet används en SQL-fråga för att extrahera data från Azure SQL i stället för att ange namnet på tabellen och kolumnnamnen i avsnittet ”struktur”. 

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
I det här fallet mappas först resultatet av frågan till kolumner som anges i ”struktur” i datakällan. Därefter mappas kolumner från källan ”struktur” till kolumner i sink ”struktur” med regler som angetts i columnMappings.  Anta att frågan returnerar 5 kolumner, två fler kolumner än de som anges i källan ”struktur”.

**Kolumnen mappning flöde**

![Kolumnen mappning flödet-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Nästa steg
Se artikeln en genomgång om hur du använder Kopieringsaktiviteten: 

- [Kopiera data från Blob Storage till SQL-databas](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
