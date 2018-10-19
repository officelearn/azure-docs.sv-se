---
title: Indexera JSON-blobar med Azure Search blob-indexeraren
description: Indexera JSON-blobar med Azure Search blob-indexeraren
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: a4689093508c3287e60da9d4668393e71211fbdd
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405710"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexera JSON-blobar med Azure Search blob-indexeraren
Den här artikeln visar hur du konfigurerar ett Azure Search blob-indexeraren för att extrahera strukturerat innehåll från JSON-blobar i Azure Blob storage.

JSON-blobar i Azure Blob storage är vanligtvis antingen ett enda JSON-dokument eller en JSON-matris. Blob-indexeraren i Azure Search kan parsa antingen konstruktion, beroende på hur du ställer in den **parsingMode** parametern på begäran.

| JSON-dokument | parsingMode | Beskrivning | Tillgänglighet |
|--------------|-------------|--------------|--------------|
| En per blob | `json` | Parsar JSON-blobar som ett enda segment med text. Varje JSON-blob blir ett enskilt dokument i Azure Search. | Allmänt tillgängliga i både [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) och [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API: er. |
| Flera per blob | `jsonArray` | Tolkar en JSON-matris i blob där varje element i matrisen blir en separat Azure Search-dokument.  | I förhandsversion, i [REST api-version =`2017-11-11-Preview` ](search-api-2017-11-11-preview.md) och [.NET SDK Preview](https://aka.ms/search-sdk-preview). |

> [!Note]
> Förhandsversion av API: er är avsedda för testning och utvärdering och ska inte användas i produktionsmiljöer.
>

## <a name="setting-up-json-indexing"></a>Ställa in JSON-indexering
Indexera JSON-blobar liknar extraheringen vanligt dokument i ett arbetsflöde för tre delar som är gemensamma för alla indexerare i Azure Search.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Det första steget är att tillhandahålla anslutningsinformation för datakällan används av indexeraren. Datakällans typ som anges här som `azureblob`, avgör vilka data extrahering beteenden anropas av indexeraren. För JSON blob-indexering är definition av datakällan samma för både JSON-dokument och matriser. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Steg 2: Skapa en målsökindex 

Indexerare är kopplad till ett indexschema. Om du använder API: et (i stället för portalen) kan du förbereda ett index i förväg så att du kan ange det på indexeraren igen. 

> [!Note]
> Indexerare som exponeras i portalen via den **Import** åtgärden för ett begränsat antal allmänt tillgängliga indexerare. Importarbetsflödet kan ofta ofta att konstruera ett preliminärt index baserat på metadata i källan. Mer information finns i [importera data till Azure Search i portalen](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Steg 3: Konfigurera och köra indexeraren

Fram till nu har definitioner för datakällan och indexet parsingMode oberoende. Men i steg 3 för indexerarkonfiguration sökvägen skiljer sig beroende på hur du vill att JSON-blob innehåll att parsas och strukturerade i ett Azure Search-index.

När du anropar indexeraren gör du följande:

+ Ange den **parsingMode** parameter `json` (för att indexera varje blob som ett enda dokument) eller `jsonArray` (om din blobbarna innehåller JSON-matriser och du behöver varje element i en matris ska betraktas som ett separat dokument).

+ Du kan också använda **fältmappningar** att välja vilka egenskaper i JSON-dokumentet källa används för att fylla i sökindexet mål. För JSON-matriser om matrisen finns som en lägre nivå egenskap kan du ange en dokumentroten som anger där matrisen är placerad i blobben.

> [!IMPORTANT]
> När du använder `json` eller `jsonArray` parsningsläge, Azure Search förutsätter att alla blobar i datakällan innehåller JSON. Om du vill hantera en blandning av JSON och icke-JSON-blobar i samma datakälla kan berätta för oss på [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Hur du Parsar enda JSON-blobar

Som standard [Azure Search blob-indexeraren](search-howto-indexing-azure-blob-storage.md) Parsar JSON-blobar som ett enda segment med text. Ofta vill du bevara strukturen för JSON-dokument. Anta exempelvis att du har följande JSON-dokument i Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Indexerardefinitionen för enskild JSON-blobar

Med Azure Search blob-indexeraren, parsas ett JSON-dokument som liknar det föregående exemplet till en enda Azure Search-dokument. Indexeraren läser in ett index genom att matcha ”text”, ”datePublished” och ”taggar” från källan mot identiskt namngivna och skrivna målfälten.

Konfigurationen tillhandahålls i brödtexten i en indexerare-åtgärd. Kom ihåg att datakällobjektet som tidigare definierats anger och informationen om datakällan. Dessutom måste målindex också finnas som en tom behållare i din tjänst. Schema och parametrar är valfria, men om du utelämnar dem indexeraren körs direkt, med hjälp av `json` som parsning läge.

En fullständigt angiven begäran kan se ut på följande sätt:

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

Enligt vad som anges, krävs inte fältmappningar. Får ett index med ”text” ”, datePublished och” taggar ”-fält, blobben som indexeraren kan hämta korrekt mappning utan ett fält som mappar finns i begäran.

## <a name="how-to-parse-json-arrays-preview"></a>Så här att parsa JSON-matriser (förhandsversion)

Du kan också välja förhandsversionsfunktionen för JSON-matris. Den här funktionen är användbar när blobbarna innehåller en *matris av JSON-objekt*, och du vill att varje element blir en separat Azure Search-dokument. Till exempel med följande JSON-blob kan du kan fylla i ditt Azure Search-index med tre separata dokument, var och en med fälten ”id” och ”text”.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Indexerarens definition för en JSON-matris

För en JSON-matris indexeraren begäran använder förhandsversionen av API: et och `jsonArray` parser. Det här är bara två matris-specifika kraven för att indexera JSON-blobar.

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

Observera återigen att fältmappningar inte krävs. Får ett index med ”id” och ”text”, härleda blob-indexeraren korrekt mappning utan en mappning Fältlista.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Kapslad JSON-matriser
Vad händer om du vill indexera en matris av JSON-objekt, men matrisen är kapslade någonstans i dokumentet? Du kan välja vilken egenskap innehåller en matris med hjälp av den `documentRoot` konfigurationsegenskapen. Exempel: om dina blobar ska se ut så här:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Använd den här konfigurationen för att indexera matrisen i den `level2` egenskapen:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Använder fältmappningar för att skapa söka efter dokument

När källa och mål fälten inte är justerade perfekt, kan du definiera ett avsnitt för mappning av fält i begärandetexten för explicit-fälten associationer.

För närvarande kan Azure Search inte kan indexera godtyckliga JSON-dokument direkt, eftersom den stöder endast primitiva datatyper, sträng-matriser och GeoJSON punkter. Du kan dock använda **fältmappningar** Välj delar av JSON-dokument och ”lyfta” dem till översta fälten för search-dokumentet. Läs om grunderna i fält-mappningar i [fältmappningar för Azure Search-indexerare](search-indexer-field-mappings.md).

Gå tillbaka till vårt exempel JSON-dokument:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Anta att en search-index med följande fält: `text` av typen `Edm.String`, `date` av typen `Edm.DateTimeOffset`, och `tags` av typen `Collection(Edm.String)`. Lägg märke till skillnaden mellan ”datePublished” i källan och `date` i indexet. Använd följande fältmappningar för att mappa din JSON till det önskade format:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Namnen på källan i mappningarna anges med den [JSON-visare](http://tools.ietf.org/html/rfc6901) notation. Du börjar med ett snedstreck att referera till roten i JSON-dokument och sedan välja önskad egenskap (på valfri nivå av inkapsling) via vanlig snedstreck kommaavgränsade sökväg.

Du kan även gå till enskilda matriselement med hjälp av ett Nollbaserat index. Till exempel använda en fältmappning så här för att välja det första elementet i matrisen ”taggar” från exemplet ovan:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Om ett källa fältnamn i en mappning fältsökväg refererar till en egenskap som inte finns i JSON, hoppas mappningen utan fel. Det gör du så att vi kan stödja dokument med ett annat schema (vilket är ett vanligt användningsfall). Eftersom det finns ingen validering, måste du tänka på att undvika skrivfel i din specifikation för mappning av fält.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Exempel: Indexeraren begäran med fältmappningar

I följande exempel är en fullständigt angivna indexeraren nyttolast, inklusive fältmappningar:

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
Om du har funktionsförfrågningar eller idéer om förbättringar kan nå oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Se också

+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Indexera Azure Blob Storage med Azure Search](search-howto-index-json-blobs.md)
+ [Indexera CSV-blobar med Azure Search blob-indexeraren](search-howto-index-csv-blobs.md)
+ [Självstudie: Söka efter halvstrukturerade data från Azure Blob storage ](search-semi-structured-data.md)
