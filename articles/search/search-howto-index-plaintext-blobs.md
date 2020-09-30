---
title: Sök över oformaterad text blobbar
titleSuffix: Azure Cognitive Search
description: Konfigurera en Sök indexerare för att extrahera oformaterad text från Azure-blobbar för full texts ökning i Azure Kognitiv sökning.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 417bdacc3ce8b619d5ec9618e6060ac071882471
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533933"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Så här indexerar du oformaterad text blobbar i Azure Kognitiv sökning

När du använder en [BLOB-indexerare](search-howto-indexing-azure-blob-storage.md) för att extrahera sökbar text för full texts ökning kan du anropa olika tolknings lägen för att få bättre indexerings resultat. Som standard tolkar indexeraren avgränsade text-blobbar som ett enda text segment. Men om alla blobar innehåller oformaterad text i samma kodning, kan du förbättra indexerings prestanda avsevärt genom att använda **text tolknings läge**.

## <a name="set-up-plain-text-indexing"></a>Konfigurera oformaterad text indexering

Om du vill indexera oformaterad text blobbar skapar eller uppdaterar du en indexare-definition med `parsingMode` konfigurations egenskapen till `text` på en [skapa indexerare](/rest/api/searchservice/create-indexer) -begäran:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Som standard `UTF-8` antas kodningen. Om du vill ange en annan kodning använder du `encoding` konfigurations egenskapen: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Exempel på begäran

Tolknings lägen anges i index definitions definitionen.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Hjälp oss att göra Azure Kognitiv sökning bättre

Om du har funktions förfrågningar eller idéer om förbättringar kan du ange dina ininformation på [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Om du behöver hjälp med den befintliga funktionen kan du publicera din fråga på [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Nästa steg

* [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
* [Så här konfigurerar du en BLOB-indexerare](search-howto-indexing-azure-blob-storage.md)
* [Översikt över BLOB-indexering](search-blob-storage-integration.md)