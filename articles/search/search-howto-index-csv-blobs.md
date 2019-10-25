---
title: Indexera CSV-blobar med Azure Kognitiv sökning BLOB-indexeraren
titleSuffix: Azure Cognitive Search
description: Crawla CSV-blobar i Azure Blob Storage för full texts ökning med hjälp av ett Azure Kognitiv sökning-index. Indexerare automatiserar data inmatning för valda data källor som Azure Blob Storage.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 18d0eb704deba80bf83b5cae0a598f47181700f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793784"
---
# <a name="how-to-index-csv-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Så här indexerar du CSV-blobar med hjälp av en BLOB-indexerare i Azure Kognitiv sökning 

> [!Note]
> delimitedText parsing-läge är i för hands version och är inte avsett för användning i produktion. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>

Som standard parsar [Azure kognitiv sökning BLOB-indexeraren](search-howto-indexing-azure-blob-storage.md) avgränsade text-blobbar som ett enda text segment. Men med blobbar som innehåller CSV-data vill du ofta behandla varje rad i blobben som ett separat dokument. Till exempel kan du använda följande avgränsade text för att dela upp den i två dokument, som innehåller "ID", "datePublished" och "Tags"-fält: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

I den här artikeln får du lära dig hur du tolkar CSV-blobbar med en Azure Kognitiv sökning BLOB-indexerby anger `delimitedText` tolknings läge. 

> [!NOTE]
> Följ konfigurations rekommendationerna för indexeraren i [en-till-många-indexering](search-howto-index-one-to-many-blobs.md) för att skriva ut flera Sök dokument från en Azure-blob.

## <a name="setting-up-csv-indexing"></a>Konfigurera CSV-indexering
Om du vill indexera CSV-blobbar skapar eller uppdaterar du en indexare-definition med `delimitedText` tolknings läge på en [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer) -begäran:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` anger att den första (icke-tomma) raden i varje BLOB innehåller rubriker.
Om blobbar inte innehåller en inledande rubrik rad, ska rubrikerna anges i indexerings konfigurationen: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Du kan anpassa avgränsnings tecken med konfigurations inställningen `delimitedTextDelimiter`. Exempel:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> För närvarande stöds endast UTF-8-kodning. Om du behöver stöd för andra kodningar kan du rösta på det på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> När du använder avgränsat text tolknings läge förutsätter Azure Kognitiv sökning att alla blobbar i data källan kommer att vara CSV. Om du behöver stöd för en blandning av CSV-och icke-CSV-blobbar i samma data källa, bör du rösta på den på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exempel på begäran
Vi lägger samman allt här är de fullständiga nytto Last exemplen. 

DataSource 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexer

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Kognitiv sökning bättre
Om du har funktions förfrågningar eller idéer om förbättringar kan du ange dina ininformation på [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

