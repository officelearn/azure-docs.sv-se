---
title: Indexera blobbar som innehåller flera Sök index dokument från Azure Blob-indexeraren för full texts ökning – Azure Search
description: Crawla Azure-blobbar för text innehåll med hjälp av Azure Search BLOB-indexeraren. Varje Blob kan innehålla ett eller flera Azure Search index dokument.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182295"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexera blobbar som producerar flera Sök dokument
Som standard behandlar en BLOB-indexeraren innehållet i en blob som ett enda Sök dokument. Vissa **parsingMode** -värden stöder scenarier där en enskild BLOB kan resultera i flera Sök dokument. De olika typerna av **parsingMode** som gör det möjligt för en indexerare att extrahera fler än ett Sök dokument från en BLOB är:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>En-till-många-dokument nyckel
Varje dokument som visas i ett Azure Search-index identifieras unikt av en dokument nyckel. 

När inget tolknings läge anges, och om det inte finns någon explicit mappning för nyckel fältet i index Azure Search automatiskt [Mappa](search-indexer-field-mappings.md) `metadata_storage_path` egenskapen som nyckel. Den här mappningen säkerställer att varje BLOB visas som ett separat sökdokument.

När du använder något av de tolknings lägen som anges ovan mappar en blob till "många" sökdokument, vilket gör att en dokument nyckel endast baseras på BLOB-metadata som är olämpliga. För att undvika den här begränsningen kan Azure Search skapa en "en-till-många"-dokument nyckel för varje enskild enhet som extraheras från en blob. Den här egenskapen heter `AzureSearch_DocumentKey` och läggs till i varje enskild entitet som extraheras från blobben. Värdet för den här egenskapen är unikt för varje enskild entitet _över blobbar_ och entiteterna visas som separata Sök dokument.

När inga explicita fält mappningar för nyckel index fältet har angetts `AzureSearch_DocumentKey` mappas som standard till den med hjälp av `base64Encode` fält mappnings funktionen.

## <a name="example"></a>Exempel
Anta att du har en index definition med följande fält:
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

När du skapar en indexerare och anger **parsingMode** till `jsonLines` -utan att ange några explicita fält mappningar för nyckel fältet, kommer följande mappning att tillämpas implicit
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Den här installationen leder till Azure Search indexet som innehåller följande information (Base64-kodat ID förkortat för det kortfattat)

| id | temperatur | tryck | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Anpassad fält mappning för index nyckel fält

Om du antar samma index definition som i föregående exempel, säger du att BLOB-behållaren har blobbar med följande struktur:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

När du skapar en indexerare med `delimitedText` **parsingMode**kan det vara naturligt att skapa en fält mappnings funktion till nyckel fältet enligt följande:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Den här mappningen kommer dock _inte_ att resultera i 4 dokument som visas i indexet, `recordid` eftersom fältet inte är unikt i _blobbar_. Därför rekommenderar vi att du använder den implicita fält mappning som används från `AzureSearch_DocumentKey` egenskapen till nyckel index fältet för "1-till-många"-tolknings lägen.

Om du vill skapa en explicit fält mappning kontrollerar du att _sourceField_ är distinkt för varje enskild entitet **i alla blobbar**.

> [!NOTE]
> Den metod som används `AzureSearch_DocumentKey` av för att säkerställa unikhet per extraherad entitet kan ändras och därför bör du inte förlita dig på dess värde för programmets behov.

## <a name="see-also"></a>Se också

+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Indexera Azure-Blob Storage med Azure Search](search-howto-index-json-blobs.md)
+ [Indexera CSV-blobar med Azure Search BLOB-indexeraren](search-howto-index-csv-blobs.md)
+ [Indexera JSON-blobbar med Azure Search BLOB-indexeraren](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>Nästa steg
* Mer information om Azure Search finns på [sidan Sök tjänst](https://azure.microsoft.com/services/search/).