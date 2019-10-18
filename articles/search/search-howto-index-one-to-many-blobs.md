---
title: Indexera en BLOB i många Sök index dokument från Azure Blob-indexeraren för full texts ökning
description: Crawla Azure-blobbar för text innehåll med hjälp av Azure Search BLOB-indexeraren. Varje Blob kan ge ett eller flera Azure Search index dokument.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: 585d1e64ae124dce8cb0d4165ecbf0f503560405
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533695"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexera blobbar för att skapa flera Sök dokument
Som standard behandlar en BLOB-indexeraren innehållet i en blob som ett enda Sök dokument. Vissa **parsingMode** -värden stöder scenarier där en enskild BLOB kan resultera i flera Sök dokument. De olika typerna av **parsingMode** som gör det möjligt för en indexerare att extrahera fler än ett Sök dokument från en BLOB är:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>En-till-många-dokument nyckel
Varje dokument som visas i ett Azure Search-index identifieras unikt av en dokument nyckel. 

När inget tolknings läge har angetts, och om det inte finns någon explicit mappning för nyckel fältet i index Azure Search automatiskt [mappas](search-indexer-field-mappings.md) `metadata_storage_path`-egenskapen som nyckel. Den här mappningen säkerställer att varje BLOB visas som ett separat sökdokument.

När du använder något av de tolknings lägen som anges ovan mappar en blob till "många" sökdokument, vilket gör att en dokument nyckel endast baseras på BLOB-metadata som är olämpliga. För att undvika den här begränsningen kan Azure Search skapa en "en-till-många"-dokument nyckel för varje enskild enhet som extraheras från en blob. Den här egenskapen heter `AzureSearch_DocumentKey` och läggs till i varje enskild enhet som extraheras från blobben. Värdet för den här egenskapen är unikt för varje enskild entitet _över blobbar_ och entiteterna visas som separata Sök dokument.

Som standard, när inga explicita fält mappningar för nyckel index fältet har angetts, mappas `AzureSearch_DocumentKey` till den med hjälp av den `base64Encode` fält mappnings funktionen.

## <a name="example"></a>Exempel
Anta att du har en index definition med följande fält:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Och blob-behållaren har blobbar med följande struktur:

_Blob1. JSON_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. JSON_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

När du skapar en indexerare och anger **parsingMode** till `jsonLines` – utan att ange några explicita fält mappningar för nyckel fältet, tillämpas följande mappning implicit
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Den här installationen leder till Azure Search indexet som innehåller följande information (Base64-kodat ID förkortat för det kortfattat)

| id | temperatur | tryck | tidsstämpel |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Anpassad fält mappning för index nyckel fält

Om du antar samma index definition som i föregående exempel, säger du att BLOB-behållaren har blobbar med följande struktur:

_Blob1. JSON_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. JSON_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

När du skapar en indexerare med `delimitedText` **parsingMode**kan det vara naturligt att skapa en fält mappnings funktion till nyckel fältet enligt följande:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Den här mappningen kommer dock _inte_ att resultera i 4 dokument som visas i indexet, eftersom fältet `recordid` inte är unikt i _blobbar_. Därför rekommenderar vi att du använder den implicita fält mappningen som används från egenskapen `AzureSearch_DocumentKey` till nyckel index fältet för "1-till-många"-tolknings lägen.

Om du vill skapa en explicit fält mappning kontrollerar du att _sourceField_ är distinkt för varje enskild entitet **i alla blobbar**.

> [!NOTE]
> Den metod som används av `AzureSearch_DocumentKey` för att säkerställa unika värden per extraherad entitet kan ändras och därför bör du inte förlita dig på dess värde för programmets behov.

## <a name="next-steps"></a>Nästa steg

Om du inte redan är bekant med den grundläggande strukturen och arbets flödet för BLOB-indexering bör du granska [indexeringen av Azure-Blob Storage med Azure Search](search-howto-index-json-blobs.md) först. Läs följande artiklar om du vill ha mer information om att parsa lägen för olika typer av BLOB-Conten.

> [!div class="nextstepaction"]
> [INDEXERA CSV-blobbar](search-howto-index-csv-blobs.md) 
> [Indexera JSON-blobbar](search-howto-index-json-blobs.md)