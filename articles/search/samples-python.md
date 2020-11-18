---
title: Python-exempel
titleSuffix: Azure Cognitive Search
description: Hitta Azure Kognitiv sökning demo python-kod exempel som använder Azure .NET SDK för python eller REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687104"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-kod exempel för Azure Kognitiv sökning

Lär dig mer om python-kod exemplen som demonstrerar funktionerna i Azure Kognitiv sökning. De primära databaserna är följande:

| Lagringsplats | Beskrivning |
|------------|-------------|
| [Azure-SDK – för python/tree/master/SDK/search/Azure-Search-Documents/samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Exempel som producerats av Azure SDK-teamet som levereras med Azure.Search.Documents-klient biblioteket i SDK: n. Du kan också granska [enhets testerna](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) för klient biblioteket för att se hur olika API: er anropas. |
| [Azure-samples/Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples) | Kod exempel som följer med instruktions artiklar, inklusive [snabb start: skapa ett Sök index i python](search-get-started-python.md).|

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=csharp&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="python-sdk-samples"></a>Python SDK-exempel

Azure SDK för python innehåller flera exempel och en [komma igång-sida](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) som innehåller nödvändiga komponenter och paket installationer. Sidan innehåller också länkar till följande exempel, som visas här för din bekvämlighet.

| Exempel | Beskrivning |
|---------|-------------|
| [Autentisera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Visar hur du konfigurerar en klient och autentiserar till tjänsten. | 
| [Index Create-Read-Update-Delete-åtgärder](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [Sök index](search-what-is-an-index.md). |
| [Indexerare skapa-läsa-uppdatera-ta bort-åtgärder](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Visar hur du skapar, uppdaterar, hämtar, listar, återställer och tar bort [indexerare](search-indexer-overview.md). |
| [Sök efter indexerare data källor](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [Synonymer](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [synonym Maps](search-synonyms.md).  |
| [Läsa in dokument](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Visar hur du överför eller sammanfogar dokument till ett index i en [data import](search-what-is-data-import.md) åtgärd. |
| [Exempelfråga](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Visar hur du ställer in en [grundläggande fråga](search-query-overview.md). |
| [Filter fråga](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Visar hur du skapar ett [filter uttryck](search-filters.md). |
| [Fasett-fråga](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Visar hur du arbetar med [ansikte](search-filters-facets.md). |

## <a name="documentation-samples"></a>Dokumentationsexempel

Följande exempel har en associerad artikel i [Azure kognitiv sökning-dokumentationen](https://docs.microsoft.com/azure/search/).

| Exempel | Beskrivning | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Källkod för [snabb start: skapa ett Sök index i python](search-get-started-python.md).  |
| [självstudie – AI – berikning](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Källkod för [Självstudier: Använd python och AI för att generera sökbart innehåll från Azure-blobbar](cognitive-search-tutorial-blob-python.md).  |
| [AzureML – anpassad – kompetens](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Käll kod till [exempel: skapa en anpassad kunskap med hjälp av python](cognitive-search-custom-skill-python.md).  |