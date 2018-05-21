---
title: Indexering CSV blobbar med Azure blob sökindexeraren | Microsoft Docs
description: Lär dig hur du CSV-blobbar med Azure Search index
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: bf65ab7858ba792418e325e7a025ee1bd88bbb27
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexering CSV blobbar med Azure Search blob indexeraren
Som standard [Azure blob sökindexeraren](search-howto-indexing-azure-blob-storage.md) Parsar avgränsade text blobbar som ett enda segment av text. Men med blobbar som innehåller CSV-data, vill du ofta behandla varje rad i blob som ett separat dokument. Till exempel följande avgränsad text får du kanske vill parsa till två dokument, var och en som innehåller ”id”, ”datePublished” och ”taggar” fält: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

I den här artikeln får lära du dig att tolka CSV blobbar med en indexerare för Azure Search-blob. 

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och ska inte användas i produktionsmiljöer. Mer information finns i [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 
> 

## <a name="setting-up-csv-indexing"></a>Konfigurera CSV-indexering
Att indexera CSV blobbar, skapa eller uppdatera definition av en indexerare med det `delimitedText` parsning läge:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Mer information om API: et för skapa indexeraren kolla [skapa indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` Anger att den första raden (icke-tomma) för varje blobb innehåller huvuden.
Om BLOB får inte innehålla en inledande rubrikraden, anges sidhuvuden i indexeraren konfigurationen: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Du kan anpassa den avgränsare tecken med hjälp av den `delimitedTextDelimiter` konfigurationsinställning. Exempel:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> För närvarande stöds endast UTF-8-kodning. Om du behöver stöd för andra kodningar rösta [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> När du använder avgränsad text parsning läge förutsätter Azure Search att alla blobbar i datakällan kommer att CSV. Om du behöver stöd för en blandning av CSV- och icke-CSV-blobbar i samma datakälla du rösta [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exempel på begäran
Om detta alla tillsammans, här är fullständig nyttolast-exempel. 

Datakällan: 

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
Om du har funktionsförfrågningar om eller förslag på förbättringar, ange dina kommentarer om [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

