---
title: Indexera CSV-blobar med Azure Search blob-indexeraren | Microsoft Docs
description: Lär dig hur du indexera CSV-blobar med Azure Search
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: b1f97b5e9542e32096bb060bce40e7b9620d0f49
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406083"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexera CSV-blobar med Azure Search blob-indexeraren
Som standard [Azure Search blob-indexeraren](search-howto-indexing-azure-blob-storage.md) Parsar avgränsad text blobbar som ett enda segment med text. Men med BLOB-objekt som innehåller CSV-data, du ofta behandlar varje rad i bloben som ett separat dokument. Till exempel med följande avgränsad text kan du parsa den till två dokument, som var och en som innehåller ”id”, ”datePublished” och ”taggar” fält: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

I den här artikeln får lära du dig att tolka CSV-blobar med en Azure Search blob-indexeraren. 

> [!IMPORTANT]
> Den här funktionen är för närvarande i offentlig förhandsversion och ska inte användas i produktionsmiljöer. Mer information finns i [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 
> 

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

Indexerare:

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

