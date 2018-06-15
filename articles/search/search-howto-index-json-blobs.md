---
title: Indexering JSON-blobbar med Azure Search blob indexeraren
description: Indexering JSON-blobbar med Azure Search blob indexeraren
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 752df29200a5e020ccf10f511ae2f02c0d72bd48
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363010"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexering JSON-blobbar med Azure Search blob indexeraren
Den här artikeln visar hur du konfigurerar en indexerare med Azure Search blob för att extrahera strukturerade innehåll från JSON-blobbar i Azure Blob storage.

JSON-blobbar i Azure Blob storage är vanligtvis ett enskilt JSON-dokument eller en JSON-matris. Blob-indexerare i Azure Search kan parsa antingen konstruktion, beroende på hur du ställer in den **parsingMode** parameter på begäran.

| JSON-dokumentet | parsingMode | Beskrivning | Tillgänglighet |
|--------------|-------------|--------------|--------------|
| En per blob | `json` | Parsar JSON-blobbar som ett enda segment av text. Varje JSON-blob blir ett enda Azure Search-dokument. | Allmänt tillgänglig i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API: er. |
| Flera per blob | `jsonArray` | Parsar en JSON-matris i blob där varje element i matrisen blir ett separat Azure Search-dokument.  | I Förhandsgranska i [REST api-version =`2017-11-11-Preview` ](search-api-2017-11-11-preview.md) och [.NET SDK Preview](https://aka.ms/search-sdk-preview). |

> [!Note]
> API: er för förhandsversionen är avsedd för testning och utvärdering och ska inte användas i produktionsmiljöer.
>

## <a name="setting-up-json-indexing"></a>Ställa in JSON indexering
Indexering JSON-blobbar liknar uppackningen vanligt dokument i ett arbetsflöde för tre delar som är gemensamma för alla indexerare i Azure Search.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Det första steget är att ange anslutningsinformation för datakällan används av indexeraren. Datakällans typ som anges här som `azureblob`, avgör vilka data extrahering beteenden anropas av indexeraren. För JSON-blob indexering, är datakälla definitionen är samma för både JSON-dokument och matriser. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Steg 2: Skapa ett mål för sökindex 

Indexerare paras ihop med en indexeringsschema. Om du använder API: N (i stället för portalen) kan du förbereda ett index i förväg så att du kan ange den på indexeringsåtgärden. 

> [!Note]
> Indexerare exponeras i portalen via den **importera** åtgärd för ett begränsat antal allmänt tillgänglig indexerare. Importera arbetsflödet kan ofta ofta skapa en preliminär index baserat på metadata i källan. Mer information finns i [importera data till Azure Search i portalen](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Steg 3: Konfigurera och köra indexeraren

Fram till nu har definitioner för datakällan och index parsingMode oberoende. Men i steg 3 för konfiguration av indexerare sökvägen skiljer sig beroende på hur du vill JSON-blob innehåll att parsas och strukturerad i ett Azure Search-index.

Vid anrop av indexerare, gör du följande:

+ Ange den **parsingMode** parameter till `json` (att indexera varje blob som ett enskilt dokument) eller `jsonArray` (om dina blobbar innehåller JSON-matriser och du måste varje element i en matris ska behandlas som ett separat dokument).

+ Du kan också använda **fältet mappningar** att välja vilka egenskaper för datakälla JSON-dokumentet som används för att fylla i mål-sökindexet. För JSON-matriser om matrisen finns som en lägre nivå egenskap, kan du ange en dokumentroten som anger om matrisen är placerad i blobben.

> [!IMPORTANT]
> När du använder `json` eller `jsonArray` parsning läge Azure Search förutsätter att alla blobbar i datakällan innehåller JSON. Om du behöver stöd för en blandning av JSON och icke-JSON-blobbar i samma datakälla berätta för oss på [våra UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Så här tolkar du enda JSON-blobbar

Som standard [Azure blob sökindexeraren](search-howto-indexing-azure-blob-storage.md) Parsar JSON-blobbar som ett enda segment av text. Ofta vill du bevara strukturen för JSON-dokument. Anta att du har följande JSON-dokument i Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Indexerare definition för enskild JSON-BLOB

Använda Azure blob sökindexeraren, tolkas en JSON-dokumentet som liknar föregående exempel i ett enda Azure Search-dokument. Indexeraren läser in ett index som matchar ”text”, ”datePublished” och ”-taggar” från källan mot identiskt namngivna och skrivna målfält.

Konfigurationen har angetts i brödtexten för en indexerare-åtgärd. Kom ihåg att datakällobjektet som tidigare definierats anger och information om datakällan. Mål-index måste dessutom också finnas som en tom behållare i din tjänst. Schemat och parametrar är valfria, men om du utelämnar dem indexeraren körs omedelbart, med hjälp av `json` som tolkning läge.

En fullständigt angiven begäran kan se ut så här:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Som nämnts, krävs inte fältmappningar. Få ett index med ”text” ”, datePublished och” etiketter ”fält, blob indexeraren kan härleda korrekt mappning utan ett fält mappningen finns i begäran.

## <a name="how-to-parse-json-arrays-preview"></a>Så här tolkar du JSON-matriser (förhandsgranskning)

Du kan också välja förhandsgranskningsfunktion för JSON-matris. Den här funktionen är användbart när blobbar innehåller en *matris av JSON-objekt*, och du vill att varje element ska bli ett separat Azure Search-dokument. Till exempel följande JSON-blob får du kan fylla i ditt Azure Search index med tre separata dokument med fälten ”id” och ”text”.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Indexerare definitionen för en JSON-matris

För en JSON-matris indexeraren begäran använder förhandsversionen API och `jsonArray` parsern. Detta är de bara två matris-specifika krav för indexering JSON-blobbar.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Igen och Observera att fältmappningar inte krävs. Givet ett index med fälten ”id” och ”text”, härleda blob indexeraren korrekt mappning utan en mappning Fältlista.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Kapslade JSON-matriser
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

## <a name="using-field-mappings-to-build-search-documents"></a>Använder fältmappningar för att bygga Sök dokument

När källa och mål-fält inte är justerade perfekt, kan du definiera en mappning fältavsnitt i begärandetexten för explicit-fält kopplingarna.

För närvarande kan Azure Search indexera godtyckliga JSON-dokument direkt, eftersom den stöder endast primitiva datatyper, string-matriser och GeoJSON punkter. Du kan dock använda **fältet mappningar** att välja delar av JSON-dokument och ”lyfta” dem till översta fält i Sök-dokument. Läs om fältet mappningar grunderna i [fältet avbildningar i Azure Search indexerare](search-indexer-field-mappings.md).

Gå tillbaka till våra exempel JSON-dokumentet:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Anta en sökindex med följande fält: `text` av typen `Edm.String`, `date` av typen `Edm.DateTimeOffset`, och `tags` av typen `Collection(Edm.String)`. Observera uppgifterna ”datePublished” i källan och `date` i indexet. Om du vill mappa din JSON till önskad form, används följande fältmappningar:

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

## <a name="example-indexer-request-with-field-mappings"></a>Exempel: Indexeraren begäran med fältmappningar

Följande exempel är en fullständigt angiven indexeraren nyttolast, inklusive fältmappningar:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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


## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar om eller förslag på förbättringar kan nå oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Se också

+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Indexering Azure Blob Storage med Azure Search](search-howto-index-json-blobs.md)
+ [Indexering CSV blobbar med Azure Search blob indexeraren](search-howto-index-csv-blobs.md)
+ [Självstudier: Sök halvstrukturerade data från Azure Blob storage ](search-semi-structured-data.md)