---
title: "Fråga ditt Azure Search-index | Microsoft Docs"
description: "Skapa en sökfråga i Azure Search och använd sökparametrar för att filtrera och sortera sökresultat."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: ashmaka
ms.openlocfilehash: a22b82829df4659681940267e64c98d345453958
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="query-your-azure-search-index"></a>Skicka frågor mot ditt Azure Search-index
> [!div class="op_single_selector"]
> * [Översikt](search-query-overview.md)
> * [Portalen](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

När du skickar sökförfrågningar till Azure Search finns det ett antal parametrar som du kan ange tillsammans med de ord som du skriver i sökrutan i programmet. Med dessa frågeparametrar kan du få djupare kontroll över [textsökningsupplevelsen](search-lucene-query-architecture.md).

Listan nedan beskriver kortfattat vanliga användningsområden för frågeparametrar i Azure Search. Fullständig information om frågeparametrar och hur de fungerar finns på de detaljerade sidorna för [REST-API:et](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) och [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Typer av frågor
I Azure Search har du många alternativ för att skapa extremt kraftfulla frågor. De två viktigaste typerna av frågor som du använder är `search` och `filter`. En `search`-fråga söker efter en eller flera termer i alla *sökbara* fält i ditt index och fungerar som du förväntar dig att en sökmotor som Google eller Bing fungerar. En `filter`-fråga utvärderar ett booleskt uttryck i alla *filtrerbara* fält i ett index. Till skillnad från `search`-frågor matchar `filter`-frågor det exakta innehållet i ett fält, vilket innebär att de är skiftlägeskänsliga för strängfält.

Du kan använda sökningar och filter tillsammans eller separat. Om du använder dem tillsammans tillämpas filtret först på hela indexet och sedan utförs sökningen på resultatet av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

Syntaxen för filteruttryck är en delmängd av [OData-filterspråket](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). För sökfrågor kan du antingen använda [den förenklade syntaxen](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) eller [Lucene-frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) som beskrivs nedan.

### <a name="simple-query-syntax"></a>Enkel frågesyntax
Den [enkla frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) är standardfrågespråket som används i Azure Search. Den enkla frågesyntaxen stöder ett antal vanliga sökoperatorer som AND-, OR-, NOT-, fras-, suffix- och prioritetsoperatorer.

### <a name="lucene-query-syntax"></a>Lucene-frågesyntaxen
Med [Lucene-frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) kan du använda det populära och uttrycksfulla frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Med den här frågesyntaxen kan du enkelt utnyttja följande funktioner: [fältbegränsade frågor](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [fuzzy-sökning](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [närhetssökning](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [termförstärkning](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [sökning med reguljära uttryck](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [sökning med jokertecken](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [syntaxgrunderna](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) och [frågor med booleska operatorer](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Ordna resultaten
När du får resultat från en sökfråga kan du begära att Azure Search visar resultaten ordnade efter värden i ett visst fält. Som standard ordnar Azure Search sökresultaten baserat på rangordningen för varje dokuments sökpoäng, som härleds från [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Om du vill att Azure Search ska returnera resultaten ordnade efter ett annat värde än sökpoängen kan du använda `orderby`-sökparametern. Du kan ange värdet för `orderby`-parametern så att den tar med fältnamn och anrop till [`geo.distance()`-funktionen](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) för geospatiala värden. Varje uttryck kan åtföljas av `asc` om resultaten ska visas i stigande ordning eller `desc` om de ska visas i fallande ordning. Standardinställningen är stigande ordning.

## <a name="paging"></a>Sidindelning
I Azure Search kan du enkelt implementera sidindelning av sökresultaten. Genom att använda `top`- och `skip`-parametrarna kan du enkelt skicka sökförfrågningar som returnerar hela uppsättningen med sökresultat i hanterbara, ordnade delmängder som lägger grunden till bra sökgränssnittsmetoder. När du tar emot dessa mindre delmängder med resultat kan du också se antalet dokument i den fullständiga uppsättningen sökresultat.

Mer information om hur du använder sidindelning för sökresultat finns i artikeln [Sidindela sökresultat i Azure Search](search-pagination-page-layout.md).

## <a name="hit-highlighting"></a>Träffmarkering
I Azure Search kan du enkelt framhäva exakt den del av sökresultatet som matchar sökfrågan med hjälp av parametrarna `highlight`, `highlightPreTag` och `highlightPostTag`. Du kan ange vilka *sökbara* fält vars matchade text ska framhävas samt ange de exakta strängtaggarna som ska läggas till i början och slutet av den matchade texten som Azure Search returnerar.

## <a name="try-out-query-syntax"></a>Testa frågesyntax

Det bästa sättet att förstå skillnader i syntaxen är genom att skicka frågor och granska resultatet.

+ Använd [Search Explorer](search-explorer.md) i Azure Portal. Genom att distribuera [exempel indexet](search-get-started-portal.md) kan du fråga indexet inom minuter med hjälp av verktyg i portalen.

+ Använd [Fiddler](search-fiddler.md) eller Chrome Postman för att skicka frågor till ett index som du har laddat upp till din söktjänst. Båda verktygen stöder REST-anrop till en HTTP-slutpunkt. 