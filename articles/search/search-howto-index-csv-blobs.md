---
title: Indexera CSV-blobar med Azure Search Blob-indexeraren – Azure Search
description: Crawla CSV-blobar i Azure Blob storage för fulltextsökning med hjälp av ett Azure Search-index. Indexerare automatisera datainmatning för valda datakällor som Azure Blob storage.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: dce19e750c5546ddd5cac18411dcbb4a603e5c45
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216109"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexera CSV-blobar med Azure Search blob-indexeraren
Som standard [Azure Search blob-indexeraren](search-howto-indexing-azure-blob-storage.md) Parsar avgränsad text blobbar som ett enda segment med text. Men med BLOB-objekt som innehåller CSV-data, du ofta behandlar varje rad i bloben som ett separat dokument. Till exempel med följande avgränsad text kan du parsa den till två dokument, som var och en som innehåller ”id”, ”datePublished” och ”taggar” fält: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

I den här artikeln får lära du dig att tolka CSV-blobar med en Azure Search blob-indexeraren. 

> [!NOTE]
> Följ rekommendationerna indexeraren konfiguration i [indexering en-till-många](search-howto-index-one-to-many-blobs.md) att mata ut flera söka efter dokument från en Azure-blob.

## <a name="setting-up-csv-indexing"></a>Konfigurera CSV-indexering
Att indexera CSV-blobar, skapa eller uppdatera en indexerardefinition med den `delimitedText` parsningsläge:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Mer information om API: et för skapa indexerare finns [skapa et indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` Anger att den första raden (icke-tomma) för varje blob innehåller rubriker.
Om blobbarna inte innehåller ett inledande rubrikrad, anges sidhuvuden i indexeraren konfigurationen: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Du kan anpassa avgränsare tecken med den `delimitedTextDelimiter` konfigurationsinställning. Exempel:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> För närvarande stöds endast i UTF-8-kodning. Om du behöver stöd för andra kodningar rösta fram den på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> När du använder avgränsad text parsningsläge förutsätter Azure Search att alla blobar i datakällan kommer att CSV. Om du vill hantera en blandning av CSV- och icke-CSV-blobbar i samma datakälla rösta [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exempel på begäran
Placera det alla tillsammans, här är fullständig nyttolast-exemplen. 

Datakälla: 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar eller idéer om förbättringar kan du ange dina kommentarer om [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

