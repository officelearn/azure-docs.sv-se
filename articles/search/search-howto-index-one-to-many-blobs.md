---
title: Indexera blobbar som innehåller flera dokument
titleSuffix: Azure Cognitive Search
description: Crawla Azure-blobbar för text innehåll med hjälp av Azure Kognitiv sökning BLOB-indexeraren, där varje BLOB kan ge ett eller flera Sök index dokument.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1f93ae8a017c889f6c465b3ccbbb66382577e871
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146801"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexera blobbar för att skapa flera Sök dokument
Som standard behandlar en BLOB-indexeraren innehållet i en blob som ett enda Sök dokument. Vissa **parsingMode** -värden stöder scenarier där en enskild BLOB kan resultera i flera Sök dokument. De olika typerna av **parsingMode** som gör det möjligt för en indexerare att extrahera fler än ett Sök dokument från en BLOB är:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>En-till-många-dokument nyckel
Varje dokument som visas i ett Azure Kognitiv sökning-index identifieras unikt av en dokument nyckel. 

När inget tolknings läge anges, och om det inte finns någon explicit mappning för nyckel fältet i indexet Azure Kognitiv sökning [Mappa](search-indexer-field-mappings.md) automatiskt `metadata_storage_path` egenskapen som nyckel. Den här mappningen säkerställer att varje BLOB visas som ett separat sökdokument.

När du använder något av de tolknings lägen som anges ovan mappar en blob till "många" sökdokument, vilket gör att en dokument nyckel endast baseras på BLOB-metadata som är olämpliga. För att lösa den här begränsningen kan Azure Kognitiv sökning generera en "en-till-många"-dokument nyckel för varje enskild enhet som extraheras från en blob. Den här egenskapen heter `AzureSearch_DocumentKey` och läggs till i varje enskild entitet som extraheras från blobben. Värdet för den här egenskapen är unikt för varje enskild entitet _över blobbar_ och entiteterna visas som separata Sök dokument.

När inga explicita fält mappningar för nyckel index fältet har angetts `AzureSearch_DocumentKey` mappas som standard till den med hjälp av `base64Encode` fält mappnings funktionen.

## <a name="example"></a>Exempel
Anta att du har en index definition med följande fält:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Och blob-behållaren har blobbar med följande struktur:

_Blob1.jspå_

```json
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.jspå_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

När du skapar en indexerare och anger **parsingMode** till `jsonLines` -utan att ange några explicita fält mappningar för nyckel fältet, kommer följande mappning att tillämpas implicit

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

Den här installationen leder till Azure Kognitiv sökning-indexet som innehåller följande information (Base64-kodat ID förkortat för det kortfattat)

| id | temperatur | tryck | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Anpassad fält mappning för index nyckel fält

Om du antar samma index definition som i föregående exempel, säger du att BLOB-behållaren har blobbar med följande struktur:

_Blob1.jspå_

```json
    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.jspå_

```json
    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 
```

När du skapar en indexerare med `delimitedText` **parsingMode**kan det vara naturligt att skapa en fält mappnings funktion till nyckel fältet enligt följande:

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

Den här mappningen kommer dock _inte_ att resultera i 4 dokument som visas i indexet, eftersom `recordid` fältet inte är unikt i _blobbar_. Därför rekommenderar vi att du använder den implicita fält mappning som används från `AzureSearch_DocumentKey` egenskapen till nyckel index fältet för "1-till-många"-tolknings lägen.

Om du vill skapa en explicit fält mappning kontrollerar du att _sourceField_ är distinkt för varje enskild entitet **i alla blobbar**.

> [!NOTE]
> Den metod som används av `AzureSearch_DocumentKey` för att säkerställa unikhet per extraherad entitet kan ändras och därför bör du inte förlita dig på dess värde för programmets behov.

## <a name="next-steps"></a>Nästa steg

Om du inte redan är bekant med den grundläggande strukturen och arbets flödet för BLOB-indexering bör du granska [indexeringen av azure Blob Storage med azure kognitiv sökning](search-howto-index-json-blobs.md) först. Läs följande artiklar om du vill ha mer information om att parsa lägen för olika typer av BLOB-innehåll.

> [!div class="nextstepaction"]
> [INDEXERA CSV-blobbar](search-howto-index-csv-blobs.md) 
>  [INDEXERA JSON-blobbar](search-howto-index-json-blobs.md)
