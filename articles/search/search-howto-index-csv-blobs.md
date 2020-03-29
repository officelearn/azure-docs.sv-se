---
title: Sök över CSV-blobbar
titleSuffix: Azure Cognitive Search
description: Extrahera och importera CSV från Azure Blob-lagring med hjälp av avgränsningslägetexttolkning.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122329"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Indexerar CSV-blobbar med avgränsattexttolkningsläge och Blob-indexerare i Azure Cognitive Search

Som standard [tolkar Azure Cognitive Search blob indexerer](search-howto-indexing-azure-blob-storage.md) avgränsade textblobar som en enda textbit. Men med blobbar som innehåller CSV-data vill du ofta behandla varje rad i bloben som ett separat dokument. Med tanke på följande avgränsade text kanske du vill tolka den i två dokument, som var och en innehåller "id", "datePublished" och "tags"-fält: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

I den här artikeln får du lära dig hur du tolkar CSV-blobbar `delimitedText` med en Azure Cognitive Search blob indexerare genom att ange tolkningsläge. 

> [!NOTE]
> Följ konfigurationsrekommendationerna för indexerare i [en-till-många-indexering](search-howto-index-one-to-many-blobs.md) för att mata ut flera sökdokument från en Azure-blob.

## <a name="setting-up-csv-indexing"></a>Ställa in CSV-indexering
Om du vill indexera CSV-blobbar skapar `delimitedText` eller uppdaterar du en indexeringsdefinition med tolkningsläget på en [Create Indexer-begäran:](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`anger att den första (icke-tomma) raden i varje blob innehåller rubriker.
Om blobbar inte innehåller en inledande rubrikrad ska rubrikerna anges i indexeringskonfigurationen: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Du kan anpassa tecknet avgränsare med konfigurationsinställningen. `delimitedTextDelimiter` Ett exempel:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> För närvarande stöds endast UTF-8-kodningen. Om du behöver stöd för andra kodningar, rösta på den på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> När du använder det avgränsade texttolkningsläget förutsätter Azure Cognitive Search att alla blobbar i datakällan kommer att vara CSV. Om du behöver stödja en blandning av CSV och icke-CSV blobbar i samma datakälla, vänligen rösta för det på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Begär exempel
Sätta ihop allt detta, här är de kompletta nyttolast exempel. 

Datasource: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexerare:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Cognitive Search bättre
Om du har funktionsförfrågningar eller idéer om förbättringar anger du dina synpunkter på [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

