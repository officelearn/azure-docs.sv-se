---
title: Indexering JSON-blobbar med Azure Search blob indexeraren
description: Indexering JSON-blobbar med Azure Search blob indexeraren
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/07/2017
ms.author: eugenesh
ms.openlocfilehash: bf4d3a517e1308a142d21cffff64f3c6e104eb62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexering JSON-blobbar med Azure Search blob indexeraren
Den här artikeln visar hur du konfigurerar Azure blob sökindexeraren om du vill extrahera strukturerade innehåll från BLOB som innehåller JSON.

## <a name="scenarios"></a>Scenarier
Som standard [Azure blob sökindexeraren](search-howto-indexing-azure-blob-storage.md) Parsar JSON-blobbar som ett enda segment av text. Ofta vill du bevara strukturen för JSON-dokument. Till exempel få JSON-dokumentet

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Du kanske vill tolka i Azure Search-dokument med ”text”, ”datePublished” och ”taggar” fält.

När dina blobbar också innehålla en **matris av JSON-objekt**, kanske du vill att varje element i matrisen så att den blir ett separat Azure Search-dokument. Till exempel få en blob med JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Du kan fylla i ditt Azure Search index med tre separata dokument med fälten ”id” och ”text”.

> [!IMPORTANT]
> JSON-matris parsning funktioner är för närvarande under förhandsgranskning. Det är bara tillgängliga i REST-API som använder version **2016-09-01-Preview**. Kom ihåg, förhandsgranska API: er är avsedd för testning och utvärdering och ska inte användas i produktionsmiljöer.
>
>

## <a name="setting-up-json-indexing"></a>Ställa in JSON indexering
Indexering JSON-blobbar liknar uppackningen vanligt dokument. Börja med att skapa datakällan exakt på vanligt sätt: 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Skapa sedan sökindex mål om du inte redan har ett. 

Slutligen skapar du en indexerare och anger den `parsingMode` parameter till `json` (att indexera varje blob som ett enskilt dokument) eller `jsonArray` (om dina blobbar innehåller JSON-matriser och du måste varje element i en matris ska behandlas som ett separat dokument):

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Om det behövs, Använd **fältet mappningar** att hämta egenskaperna för käll-JSON-dokumentet som används för att fylla dina mål sökindex som visas i nästa avsnitt.

> [!IMPORTANT]
> När du använder `json` eller `jsonArray` parsning läge Azure Search förutsätter att alla blobbar i datakällan innehåller JSON. Om du behöver stöd för en blandning av JSON och icke-JSON-blobbar i samma datakälla berätta för oss på [våra UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>Använder fältmappningar för att bygga Sök dokument
För närvarande kan Azure Search indexera godtyckliga JSON-dokument direkt, eftersom den stöder endast primitiva datatyper, string-matriser och GeoJSON punkter. Du kan dock använda **fältet mappningar** att välja delar av JSON-dokument och ”lyfta” dem till översta fält i Sök-dokument. Läs om fältet mappningar grunderna i [Azure Search indexeraren fältmappningar överbrygga skillnaderna mellan datakällor och sökindexen](search-indexer-field-mappings.md).

Kom tillbaka till våra exempel JSON-dokumentet:

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Anta att du har en sökindex med följande fält: `text` av typen `Edm.String`, `date` av typen `Edm.DateTimeOffset`, och `tags` av typen `Collection(Edm.String)`. Om du vill mappa din JSON till önskad form, används följande fältmappningar:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Namnen på datakällan i mappningarna anges med den [JSON pekaren](http://tools.ietf.org/html/rfc6901) notation. Du börjar med ett snedstreck att referera till roten i JSON-dokumentet och sedan välja önskad egenskap (på valfri nivå av kapsling) via vanlig snedstreck kommaavgränsade sökväg.

Du kan också referera till enskilda matriselement med hjälp av ett Nollbaserat index. Till exempel för att hämta det första elementet i matrisen ”taggar” från exemplet ovan, använda en avbildning så här:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Om fältet källnamnet i en sökväg för mappning av fältet refererar till en egenskap som inte finns i JSON, ignoreras mappningen utan fel. Det gör du så att vi kan stödja dokument med ett annat schema (vilket är ett vanligt användningsfall). Eftersom det finns ingen verifiering, måste du var noga med för att undvika skrivfel i din specifikation för mappning av fält.
>
>

Om JSON-dokument innehåller endast enkla översta egenskaper, kanske du inte behöver fältmappningar alls. Till exempel om din JSON ser ut så här, mappas översta egenskaper ”text”, ”datePublished” och ”-taggar” direkt till motsvarande fält i sökindexet:

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

Här är en fullständig indexeraren nyttolast med fältmappningar:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## <a name="indexing-nested-json-arrays"></a>Indexering kapslade JSON-matriser
Vad händer om du inte vill att indexera en matris av JSON-objekt, men att matris är kapslat någonstans i dokumentet? Du kan välja vilken egenskap innehåller en matris med hjälp av den `documentRoot` konfigurationsegenskapen. Om exempelvis dina blobbar se ut så här:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

använda den här konfigurationen för att indexera matrisen som ingår i den `level2` egenskapen:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar om eller förslag på förbättringar kan nå oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).
