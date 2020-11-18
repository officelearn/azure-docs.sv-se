---
title: Java-exempel
titleSuffix: Azure Cognitive Search
description: Hitta exempel på Java-kod exempel för Azure Kognitiv sökning demo som använder Azure .NET SDK för Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: babd40e0e3a3b7a254a8410313c5d4e3bb2832f0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687140"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Exempel på Java-kod för Azure Kognitiv sökning

Lär dig mer om Java-kod exemplen som demonstrerar funktionerna i Azure Kognitiv sökning. De primära databaserna är följande:

| Lagringsplats | Beskrivning |
|------------|-------------|
| [Azure-SDK-för-Java/tree/master/SDK/search/Azure-Search-Documents/src/samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Exempel som producerats av Azure SDK-teamet som levereras med Azure.Search.Documents-klient biblioteket i SDK: n. Du kan också granska [enhets testerna](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) för klient biblioteket för att se hur olika API: er anropas. |
| [Azure-samples/Azure-Search-Java-samples](https://github.com/Azure-Samples/azure-search-java-samples) | Kod exempel som medföljer instruktions artiklar. **Exempel i den här lagrings platsen har ännu inte uppdaterats till Använd Azure SDK för Java**. För närvarande anropar dessa exempel REST-API: er i Java Code.|

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=csharp&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="java-sdk-samples"></a>Java SDK-exempel

Azure SDK för Java innehåller flera exempel och en [komma igång-sida](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) som täcker paket installationen. Sidan innehåller också en mängd olika exempel. Det finns flera av de vanligaste åtgärderna i listan nedan för din bekvämlighet.

| Exempel | Beskrivning |
|---------|-------------|
| [Skapa Sök index](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Visar hur du skapar [Sök index](search-what-is-an-index.md). |
| [Skapa synonym](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Visar hur du skapar [synonym Maps](search-synonyms.md).  |
| [Skapa Sök Indexer](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Visar hur du skapar [indexerare](search-indexer-overview.md). |
| [Skapa sökning indexerare data Källa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Visar hur du skapar indexerare data källor, som krävs för indexerare-baserad indexering av [Azure-datakällor som stöds](search-indexer-overview.md#supported-data-sources). |
| [Skapa färdigheter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Visar hur du skapar [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md) som är kopplade till indexerare och som utför AI-baserad anrikning vid indexering. |
| [Läsa in dokument](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Visar hur du överför eller sammanfogar dokument till ett index i en [data import](search-what-is-data-import.md) åtgärd. |
| [Frågesyntax](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Visar hur du ställer in en [grundläggande fråga](search-query-overview.md). |

## <a name="documentation-samples"></a>Dokumentationsexempel

Följande exempel har en associerad artikel i [Azure kognitiv sökning-dokumentationen](https://docs.microsoft.com/azure/search/).

| Exempel | Beskrivning | 
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/Quickstart) | Källkod för [snabb start: skapa ett sökindex](search-get-started-javascript.md). Det här exemplet anropar REST-API: erna. |
| [Sök-Java-indexerare – demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Visar en Azure Cosmos DB-indexerare i Java. Det här exemplet anropar REST-API: erna. |