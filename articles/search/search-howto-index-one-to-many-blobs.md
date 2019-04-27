---
title: Index BLOB-objekt som innehåller flera Sök indexera dokument från Azure Blob-indexeraren för fulltextsökning – Azure Search
description: Crawla Azure-blobar för textinnehåll med Azure Search Blob-indexeraren. Varje blob kan innehålla ett eller flera Azure Search-index dokument.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871207"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexera blobar producera flera söka efter dokument
Som standard behandlar en blob-indexeraren innehållet i en blob som ett enda search-dokument. Vissa **parsingMode** värden stöder scenarier där en enskild blob kan resultera i flera söka efter dokument. De olika typerna av **parsingMode** som tillåter en indexerare för att extrahera som är mer än en sökning dokument från en blob är:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` parsningsläge finns i offentlig förhandsversion och ska inte användas i produktionsmiljöer. Mer information finns i [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 

## <a name="one-to-many-document-key"></a>En-till-många dokumentnyckeln
Varje dokument som visas i ett Azure Search-index identifieras unikt genom en dokumentnyckeln. 

När inget parsning läge har angetts, och om det finns ingen uttrycklig koppling för nyckelfältet i Azure Search-index automatiskt [mappar](search-indexer-field-mappings.md) den `metadata_storage_path` egenskapen som nyckel. Den här mappningen säkerställer att varje blob visas som en distinkt dokumentsökningsoperationer.

När du använder någon av parsningslägen som anges ovan, mappar en blob till ”många” söka efter dokument, vilket gör en dokumentnyckeln enbart baserat på blob-metadata som är olämpligt. Om du vill lösa det här villkoret är Azure Search kan utforma en ”en-till-många” dokumentnyckeln för varje enskild entitet som extraheras från en blob. Den här egenskapen har namnet `AzureSearch_DocumentKey` och har lagts till för varje enskild entitet som extraheras från blob. Värdet för den här egenskapen garanteras vara unikt för varje enskild entitet _över blobbar_ och entiteterna visas som separata söka efter dokument.

Som standard när ingen explicit fältmappningar för fältet nyckelindex har angetts i `AzureSearch_DocumentKey` är mappad till den, med hjälp av den `base64Encode` fältmappning funktion.

## <a name="example"></a>Exempel
Anta att du har en Indexdefinition med följande fält:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Och blob-behållare har blobar med följande struktur:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

När du skapar en indexerare och ange den **parsingMode** till `jsonLines` – utan att ange ett explicit fält mappningar för nyckelfältet följande mappning används implicit
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Den här konfigurationen leder till Azure Search-index som innehåller följande information (base64-kodad id kortats ned kortfattat)

| id | temperatur | tryck | tidsstämpel |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Anpassat fältmappning för index nyckelfältet

Under förutsättning att samma indexdefinitionen som i föregående exempel, säg din blobbehållare har blobar med följande struktur:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

När du skapar en indexerare med `delimitedText` **parsingMode**, kanske det känns naturligt att konfigurera en mappning av entitetsfält funktion till nyckelfältet enligt följande:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Men den här mappningen ska _inte_ resultera i 4 dokument visas i indexet, eftersom den `recordid` fältet är inte unikt _över blobbar_. Därför rekommenderar vi att du gör användning av implicit fältmappningen tillämpas från den `AzureSearch_DocumentKey` egenskapen till fältet nyckelindex för ”en-till-många” parsningslägen.

Om du vill konfigurera en explicit fältmappning, kontrollera att den _sourceField_ skiljer sig för varje enskild entitet **över alla blobbar**.

> [!NOTE]
> Den metod som används av `AzureSearch_DocumentKey` för att säkerställa en unikhet per extraherade enhet kan ändras och därför inte lita på dess värde för programmets behov.

## <a name="see-also"></a>Se också

+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Indexera Azure Blob Storage med Azure Search](search-howto-index-json-blobs.md)
+ [Indexera CSV-blobar med Azure Search blob-indexeraren](search-howto-index-csv-blobs.md)
+ [Indexera JSON-blobar med Azure Search blob-indexeraren](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>Nästa steg
* Läs mer om Azure Search i den [söktjänstsidan](https://azure.microsoft.com/services/search/).