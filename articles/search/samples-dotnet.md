---
title: .NET-exempel
titleSuffix: Azure Cognitive Search
description: Hitta Azure Kognitiv sökning demo C#-kod exempel som använder .NET-klient bibliotek.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d068365cc8197a579c0b043d3fff2da3d54eb803
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687211"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Kod exempel för .NET (C#) för Azure Kognitiv sökning

Lär dig mer om C#-kod exemplen som demonstrerar funktionerna i Azure Kognitiv sökning. De primära databaserna är följande:

| Lagringsplats | Beskrivning |
|------------|-------------|
| [Azure-SDK – för-NET/SDK/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Exempel som producerats av Azure SDK-teamet som levereras med Azure.Search.Documents-klient biblioteket i SDK: n. Du kan också granska [enhets testerna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) för klient biblioteket för att se hur olika API: er anropas. |
| [Azure-samples/Azure-Search-dotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Exempel som medföljer instruktions artiklar i dokumentationen, inklusive [hur du använder .net-klient biblioteket](search-howto-dotnet-sdk.md).|
| [Azure-samples/search-dotNet-kom igång](https://github.com/Azure-Samples/search-dotnet-getting-started) | Exempel som medföljer snabb starter och självstudier i dokumentationen.|

> [!Tip]
> Prova [exempel webbläsaren](/samples/browse/?languages=csharp&products=azure-cognitive-search) för att söka efter Microsofts kod exempel i GitHub, filtrerat efter produkt, tjänst och språk.

## <a name="net-sdk-samples"></a>.NET SDK-exempel

Azure SDK för .NET innehåller många exempel och en [exempel på README](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) som beskriver var och en. Listan nedan visas för din bekvämlighet.

| Exempel | Beskrivning |
|---------|-------------|
| ["Hello World", synkront](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Visar hur du skapar en klient, autentiserar och hanterar fel med hjälp av synkrona metoder.|
| ["Hello World", asynkront](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Visar hur du skapar en klient, autentiserar och hanterar fel med hjälp av asynkrona metoder.  |
| [Åtgärder på tjänst nivå](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Visar hur du skapar index, indexerare, data källor, färdighetsuppsättningar och synonym Maps. Det här exemplet visar också hur du hämtar tjänst statistik och hur du frågar ett index.  |
| [Index åtgärder](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Visar hur du utför en åtgärd på ett befintligt index, i det här fallet får du ett antal dokument som lagras i indexet.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Visar en metod för att arbeta med data typer som inte stöds.  |
| [Indexera dokument (push-modell)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | "Push" modell indexering, där du skickar en JSON-nyttolast till ett index för en tjänst.   |
| [Exempel på krypterings nyckel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Visar hur du använder en kundhanterad krypterings nyckel för att lägga till ett extra skydds lager med känsligt innehåll.  |

## <a name="documentation-samples"></a>Dokumentationsexempel

Följande exempel har en associerad artikel i [Azure kognitiv sökning-dokumentationen](https://docs.microsoft.com/azure/search/).

| Exempel | Beskrivning |
|---------|-------------|
| [Start](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Källkod för [snabb start: skapa ett sökindex ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Käll kod för [att använda .net-klient biblioteket](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Synonym listor används för frågans expansion, vilket ger matchnings bara termer som är externa i ett index. Det här exemplet ingår i [exemplet: Lägg till synonymer i C#](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Käll koden bakom indexerare-relaterade kodfragment i olika artiklar. Det här exemplet visar hur du konfigurerar en indexerare som har ett schema, fält mappningar och parametrar.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Käll kod för [hur du konfigurerar Kundhanterade nycklar för data kryptering](search-security-manage-encryption-keys.md) |
| [Skapa din första app i C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Källkod för [Självstudier: skapa din första Sökapp](tutorial-csharp-create-first-app.md). De flesta exempel är konsol program, det här MVC-exemplet använder en webb sida för att visa exempel på hotell indexet, som demonstrerar grundläggande sökning, sid brytning, Autoavsluta och föreslagna frågor, ansikts och filter. |
| [flera data källor](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Källkod för [Självstudier: index från flera data källor](tutorial-multiple-data-sources.md). |
|  [optimera data indexering](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Källkod för [Självstudier: optimera indexering med push-API](tutorial-optimize-indexing-push-api.md).  |
| [självstudie – AI – berikning](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Källkod för [Självstudier: AI-genererat sökbart innehåll från Azure-blobbar med .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Fristående exempel och lösningar

| Exempel | Beskrivning |
|---------|-------------|
| [Azure-Search – Power-färdigheter](https://github.com/Azure-Samples/azure-search-power-skills)  | Källkod för förbrukade anpassade kunskaper som du kan använda i dina vunna lösningar.  |
| [Lösningsaccelerator för kunskapsutvinning](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Innehåller mallar, stödfiler och analys rapporter som hjälper dig att prototypa en lösning för kunskaps utvinning från slut punkt till slut punkt.  |
| [Covid-19 Sök i app-lagringsplatsen](https://github.com/liamca/covid19search) | Käll kods lagrings plats för den Kognitiv sökning baserade [Covid-19 Sökappen](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Läs mer om [JFK-lösningen](https://www.microsoft.com/ai/ai-lab-jfk-files). |