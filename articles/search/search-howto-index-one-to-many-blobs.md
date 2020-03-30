---
title: Indexblobar som innehåller flera dokument
titleSuffix: Azure Cognitive Search
description: Crawla Azure-blobbar efter textinnehåll med Hjälp av Azure Congitive Search Blob-indexeraren, där varje blob kan ge ett eller flera sökindexdokument.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112263"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexera blobbar för att skapa flera sökdokument
Som standard behandlar en blob-indexerare innehållet i en blob som ett enda sökdokument. Vissa **parsingMode-värden** stöder scenarier där en enskild blob kan resultera i flera sökdokument. De olika typer av **parsingMode** som gör att en indexerare kan extrahera mer än ett sökdokument från en blob är:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>1:1-dokumentnyckel
Varje dokument som visas i ett Azure Cognitive Search-index identifieras unikt av en dokumentnyckel. 

När inget tolkningsläge har angetts och om det inte finns någon explicit mappning [maps](search-indexer-field-mappings.md) för `metadata_storage_path` nyckelfältet i indexet mappar Azure Cognitive Search automatiskt egenskapen som nyckel. Den här mappningen säkerställer att varje blob visas som ett separat sökdokument.

När du använder något av tolkningslägena som anges ovan mappas en blob till "många" sökdokument, vilket gör en dokumentnyckel enbart baserad på blobmetadata olämplig. För att övervinna den här begränsningen kan Azure Cognitive Search generera en "en-till-många"-dokumentnyckel för varje enskild entitet som extraherats från en blob. Den här `AzureSearch_DocumentKey` egenskapen namnges och läggs till varje enskild entitet som extraheras från blobben. Värdet för den här egenskapen är garanterat unikt för varje enskild entitet _över blobbar_ och entiteterna visas som separata sökdokument.

Som standard mappas fältmappningen för nyckelindexfältet `AzureSearch_DocumentKey` när inga explicita fältmappningar för nyckelindexfältet anges med hjälp av `base64Encode` fältmappningsfunktionen.

## <a name="example"></a>Exempel
Anta att du har en indexdefinition med följande fält:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Och blob-behållaren har blobbar med följande struktur:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

När du skapar en indexerare och ställer `jsonLines` in **parsingMode** till - utan att ange några explicita fältmappningar för nyckelfältet, kommer följande mappning att tillämpas implicit
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Den här inställningen resulterar i Azure Cognitive Search index som innehåller följande information (base64 kodade id förkortas för korthet)

| id | temperatur | tryck | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Anpassad fältmappning för indexnyckelfält

Om du antar samma indexdefinition som föregående exempel säger du att blob-behållaren har blobbar med följande struktur:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

När du skapar en `delimitedText` indexerare med **parsingMode**kan det kännas naturligt att ställa in en fältmappningsfunktion till nyckelfältet enligt följande:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Den här mappningen resulterar dock _inte_ i att 4 dokument visas i indexet, eftersom `recordid` fältet inte är unikt över _blobbar_. Därför rekommenderar vi att du använder den implicita `AzureSearch_DocumentKey` fältmappningen som tillämpas från egenskapen till nyckelindexfältet för "en-till-många"-tolkningslägen.

Om du vill skapa en explicit fältmappning kontrollerar du att _sourceField_ är distinkt för varje enskild entitet **för alla blobbar**.

> [!NOTE]
> Den metod `AzureSearch_DocumentKey` som används genom att säkerställa unikhet per extraherad entitet kan komma att ändras och därför bör du inte förlita dig på det värde för ditt programs behov.

## <a name="next-steps"></a>Nästa steg

Om du inte redan är bekant med den grundläggande strukturen och arbetsflödet för blob-indexering bör du först granska [Indexering av Azure Blob Storage med Azure Cognitive Search.](search-howto-index-json-blobs.md) Mer information om tolkningslägen för olika blob-innehållstyper finns i följande artiklar.

> [!div class="nextstepaction"]
> [Indexering av CSV-blobbar](search-howto-index-csv-blobs.md)
> [Indexering av JSON-blobbar](search-howto-index-json-blobs.md)
