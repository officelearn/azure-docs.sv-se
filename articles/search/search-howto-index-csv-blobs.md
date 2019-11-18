---
title: Sök över CSV-blobbar
titleSuffix: Azure Cognitive Search
description: Extrahera och importera CSV från Azure Blob Storage med delimitedText tolknings läge, för närvarande i offentlig för hands version.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2166e100f03f21c218618d19dc37ee70c6ab29ef
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113026"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Så här indexerar du CSV-blobar med delimitedText tolknings läge och blob-indexerare i Azure Kognitiv sökning 

> [!IMPORTANT] 
> DelimitedText tolknings läge finns för närvarande i en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för Portal eller .NET SDK.

Som standard parsar [Azure kognitiv sökning BLOB-indexeraren](search-howto-indexing-azure-blob-storage.md) avgränsade text-blobbar som ett enda text segment. Men med blobbar som innehåller CSV-data vill du ofta behandla varje rad i blobben som ett separat dokument. Till exempel kan du använda följande avgränsade text för att dela upp den i två dokument, som innehåller "ID", "datePublished" och "Tags"-fält: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

I den här artikeln får du lära dig hur du tolkar CSV-blobbar med en Azure Kognitiv sökning BLOB-indexerare genom att ange `delimitedText` tolknings läge. 

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

