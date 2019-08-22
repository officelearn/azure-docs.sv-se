---
title: Indexera CSV-blobar med Azure Search BLOB-indexerare – Azure Search
description: Crawla CSV-blobar i Azure Blob Storage för full texts ökning med hjälp av ett Azure Search index. Indexerare automatiserar data inmatning för valda data källor som Azure Blob Storage.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656764"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexera CSV-blobar med Azure Search BLOB-indexeraren

> [!Note]
> delimitedText parsing-läge är i för hands version och är inte avsett för användning i produktion. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>

Som standard parsar [Azure Search BLOB-indexera](search-howto-indexing-azure-blob-storage.md) avgränsade text-blobbar som ett enda text segment. Men med blobbar som innehåller CSV-data vill du ofta behandla varje rad i blobben som ett separat dokument. Till exempel kan du använda följande avgränsade text för att dela upp den i två dokument, som innehåller "ID", "datePublished" och "Tags"-fält: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

I den här artikeln får du lära dig hur du tolkar CSV-blobbar med en Azure Search BLOB- `delimitedText` indexerby som anger tolknings läget. 

> [!NOTE]
> Följ konfigurations rekommendationerna för indexeraren i [en-till-många-indexering](search-howto-index-one-to-many-blobs.md) för att skriva ut flera Sök dokument från en Azure-blob.

## <a name="setting-up-csv-indexing"></a>Konfigurera CSV-indexering
Om du vill indexera CSV-blobbar skapar eller uppdaterar du en indexare- `delimitedText` definition med tolknings läget på en [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer) -begäran:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`anger att den första (icke-tomma) raden i varje BLOB innehåller rubriker.
Om blobbar inte innehåller en inledande rubrik rad, ska rubrikerna anges i indexerings konfigurationen: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Du kan anpassa avgränsnings tecken med hjälp av `delimitedTextDelimiter` konfigurations inställningen. Exempel:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> För närvarande stöds endast UTF-8-kodning. Om du behöver stöd för andra kodningar kan du rösta på det på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> När du använder avgränsat text tolknings läge förutsätter Azure Search att alla blobbar i data källan kommer att vara CSV. Om du behöver stöd för en blandning av CSV-och icke-CSV-blobbar i samma data källa, bör du rösta på den på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
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

## <a name="help-us-make-azure-search-better"></a>Hjälp oss att göra Azure Search bättre
Om du har funktions förfrågningar eller idéer om förbättringar kan du ange dina ininformation på [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

