---
title: "Indexering CSV blobbar med Azure blob sökindexeraren | Microsoft Docs"
description: "Lär dig hur du CSV-blobbar med Azure Search index"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
ms.openlocfilehash: af9da85c37211d2436c23cc05400031c661ef51e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexering CSV blobbar med Azure Search blob indexeraren
Som standard [Azure blob sökindexeraren](search-howto-indexing-azure-blob-storage.md) Parsar avgränsade text blobbar som ett enda segment av text. Men med blobbar som innehåller CSV-data, vill du ofta behandla varje rad i blob som ett separat dokument. Till exempel anges avgränsad följande: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Du kanske vill parsa till 2 dokument, var och en som innehåller ”id”, ”datePublished” och ”taggar” fält.

I den här artikeln får du lära dig hur du Parsar CSV blobbar med en indexerare för Azure Search-blob. 

> [!IMPORTANT]
> Den här funktionen är för närvarande under förhandsgranskning. Det är bara tillgängliga i REST-API som använder version **2015-02-28-Preview**. Kontrollera komma ihåg, förhandsgranska API: er är avsedd för testning och utvärdering och ska inte användas i produktionsmiljöer. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Konfigurera CSV-indexering
Att indexera CSV blobbar, skapa eller uppdatera definition av en indexerare med det `delimitedText` parsning läge:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Mer information om API: et för skapa indexeraren kolla [skapa indexeraren](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`Anger att den första raden (icke-tomma) för varje blobb innehåller huvuden.
Om BLOB får inte innehålla en inledande rubrikraden, anges sidhuvuden i indexeraren konfigurationen: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

För närvarande stöds endast UTF-8-kodning. Också endast komma `','` tecken stöds som avgränsare. Om du behöver stöd för andra kodningar eller avgränsare berätta på [våra UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> När du använder avgränsad text parsning läge förutsätter Azure Search att alla blobbar i datakällan kommer att CSV. Om du behöver stöd för en blandning av CSV- och icke-CSV-blobbar i samma datakälla berätta på [våra UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exempel på begäran
Om detta alla tillsammans, här är fullständig nyttolast-exempel. 

Datakällan: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexerare:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Hjälp oss att förbättra Azure Search
Om du har funktionsförfrågningar om eller förslag på förbättringar kan du nå oss på vår [UserVoice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search/).

