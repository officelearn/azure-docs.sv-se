---
title: Fråga JSON-filer med SQL på begäran (för hands version)
description: I det här avsnittet beskrivs hur du läser JSON-filer med SQL på begäran i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0db022ff4fdf092c2286d2da333df2ea23406569
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692719"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Fråga JSON-filer med SQL på begäran (för hands version) i Azure Synapse Analytics

I den här artikeln får du lära dig hur du skriver en fråga med SQL på begäran (för hands version) i Azure Synapse Analytics. Frågans mål är att läsa JSON-filer.

## <a name="prerequisites"></a>Krav

Läs följande artiklar innan du läser resten av den här artikeln:

- [Installation vid första tiden](query-data-storage.md#first-time-setup)
- [Förutsättningar](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Exempel på JSON-filer

Avsnittet nedan innehåller exempel skript för att läsa JSON-filer. Filer lagras i en *JSON* -behållare, mapp *böcker*och innehåller en post i en bok med följande struktur:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>Läsa JSON-filer

Om du vill bearbeta JSON-filer med JSON_VALUE och [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)måste du läsa JSON-filen från Storage som en enda kolumn. Följande skript läser filen *book1. JSON* som en enda kolumn:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Du läser hela JSON-filen som en enskild rad eller kolumn. Så, FIELDTERMINATOR, FIELDQUOTE och ROWTERMINATOR är inställda på 0x0B.

## <a name="query-json-files-using-json_value"></a>Fråga JSON-filer med JSON_VALUE

Frågan nedan visar hur du använder [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta skalära värden (title, Publisher) från en bok med titeln *Probabilistic och statistiska metoder i Cryptology, en introduktion av valda artiklar*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Fråga JSON-filer med JSON_QUERY

Följande fråga visar hur du använder [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att hämta objekt och matriser (författare) från en bok med titeln *Probabilistic och statistiska metoder i Cryptology, en introduktion av valda ämnen*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Fråga JSON-filer med openjson

Följande fråga använder [openjson](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Den kommer att hämta objekt och egenskaper i en bok *med titeln Probabilistic och statistiska metoder i Cryptology, en introduktion av valda artiklar*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Nästa steg

Nästa artiklar i den här serien visar hur du:

- [Fråga mappar och flera filer](query-folders-multiple-csv-files.md)
- [Skapa och använda vyer](create-use-views.md)
