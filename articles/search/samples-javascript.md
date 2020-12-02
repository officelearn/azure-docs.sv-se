---
title: JavaScript-exempel
titleSuffix: Azure Cognitive Search
description: Hitta exempel på JavaScript-kod exempel för Azure Kognitiv sökning demo som använder Azure .NET SDK för Java Script.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 234c70fffb6f353c670d23624cc446fdaf6bd886
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498973"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>JavaScript-kod exempel för Azure Kognitiv sökning

Lär dig mer om exempel på JavaScript-kod som demonstrerar funktionerna i Azure Kognitiv sökning. De primära databaserna är följande:

| Lagringsplats | Description |
|------------|-------------|
| [Azure-SDK-för-JS/tree/master/SDK/Sök/Sök-dokument](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Exempel som producerats av Azure SDK-teamet som levereras med Azure.Search.Documents-klient biblioteket i SDK: n. Du kan också granska [enhets testerna](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) för klient biblioteket för att se hur olika API: er anropas. |
| [Azure-samples/Azure-Search-JavaScript-samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Kod exempel som följer med instruktions artiklar, inklusive [snabb start: skapa ett Sök index i Java Script](search-get-started-javascript.md).|

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=csharp&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="javascript-sdk-samples"></a>JavaScript SDK-exempel

Azure SDK för Java innehåller flera exempel och en [komma igång-sida](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) som omfattar paket installation, klient konfiguration och fel sökning. Sidan beskriver även följande exempel kategorier, som visas här för din bekvämlighet.

| Exempel | Description |
|---------|-------------|
| [index](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [Sök index](search-what-is-an-index.md). Den här exempel kategorin innehåller också ett exempel på tjänst statistik. |
| [dataSourceConnections (för indexerare)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [indexerare](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Visar hur du skapar, uppdaterar, hämtar, listar, återställer och tar bort [indexerare](search-indexer-overview.md).|
| [Färdigheter](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) som är kopplade till indexerare och som utför AI-baserad anrikning under indexering. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [synonym Maps](search-synonyms.md).  |
| [Frågor](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Visar frågekörningen mot ett skrivskyddat offentligt index som Microsoft är värd för.  |

## <a name="typescript-samples"></a>TypeScript-exempel

SDK innehåller också TypeScript-exempel som visas här för din bekvämlighet.

| Exempel | Description |
|---------|-------------|
| [index](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [Sök index](search-what-is-an-index.md). Den här exempel kategorin innehåller också ett exempel på tjänst statistik. |
| [dataSourceConnections (för indexerare)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [indexerare](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Visar hur du skapar, uppdaterar, hämtar, listar, återställer och tar bort [indexerare](search-indexer-overview.md).|
| [Färdigheter](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) som är kopplade till indexerare och som utför AI-baserad anrikning under indexering. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Visar hur du skapar, uppdaterar, hämtar, visar och tar bort [synonym Maps](search-synonyms.md).  |
| [Frågor](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Visar frågekörningen mot ett skrivskyddat offentligt index som Microsoft är värd för.  |

## <a name="documentation-samples"></a>Dokumentationsexempel

Följande exempel har en associerad artikel i [Azure kognitiv sökning-dokumentationen](./index.yml).

| Exempel | Description | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Källkod för [snabb start: skapa ett Sök index i Java Script](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Fristående exempel

| Exempel | Description |
|---------|-------------|
| [Azure-Search-reakta-Template](https://github.com/dereklegenzoff/azure-search-react-template) | Reagera på mall för Azure Kognitiv sökning (github.com) |