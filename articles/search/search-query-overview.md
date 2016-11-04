---
title: Skicka frågor mot ditt Azure Search-index | Microsoft Docs
description: Skapa en sökfråga i Azure Search och använd sökparametrar för att filtrera och sortera sökresultat.
services: search
documentationcenter: ''
author: ashmaka

ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka

---
# Skicka frågor mot ditt Azure Search-index
> [!div class="op_single_selector"]
> * [Översikt](search-query-overview.md)
> * [Portalen](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

När du skickar sökförfrågningar till Azure Search finns det ett antal parametrar som du kan ange tillsammans med de ord som du skriver i sökrutan i programmet. Med dessa frågeparametrar kan du få djupare kontroll över textsökningsupplevelsen.

Listan nedan beskriver kortfattat vanliga användningsområden för frågeparametrar i Azure Search. Fullständig information om frågeparametrar och hur de fungerar finns på de detaljerade sidorna för [REST-API:et](https://msdn.microsoft.com/library/azure/dn798927.aspx) och [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## Typer av frågor
I Azure Search har du många alternativ för att skapa extremt kraftfulla frågor. De två viktigaste typerna av frågor som du använder är `search` och `filter`. En `search`-fråga söker efter en eller flera termer i alla *sökbara* fält i ditt index och fungerar som du förväntar dig att en sökmotor som Google eller Bing fungerar. En `filter`-fråga utvärderar ett booleskt uttryck i alla *filtrerbara* fält i ett index. Till skillnad från `search`-frågor matchar `filter`-frågor det exakta innehållet i ett fält, vilket innebär att de är skiftlägeskänsliga för strängfält.

Du kan använda sökningar och filter tillsammans eller separat. Om du använder dem tillsammans tillämpas filtret först på hela indexet och sedan utförs sökningen på resultatet av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

Syntaxen för filteruttryck är en delmängd av [OData-filterspråket](https://msdn.microsoft.com/library/azure/dn798921.aspx). För sökfrågor kan du antingen använda [den förenklade syntaxen](https://msdn.microsoft.com/library/azure/dn798920.aspx) eller [Lucene-frågesyntaxen](https://msdn.microsoft.com/library/azure/mt589323.aspx) som beskrivs nedan.

### Enkel frågesyntax
Den [enkla frågesyntaxen](https://msdn.microsoft.com/library/azure/dn798920.aspx) är standardfrågespråket som används i Azure Search. Den enkla frågesyntaxen stöder ett antal vanliga sökoperatorer som AND-, OR-, NOT-, fras-, suffix- och prioritetsoperatorer.

### Lucene-frågesyntaxen
Med [Lucene-frågesyntaxen](https://msdn.microsoft.com/library/azure/mt589323.aspx) kan du använda det populära och uttrycksfulla frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Med den här frågesyntaxen kan du enkelt utnyttja följande funktioner: [fältbegränsade frågor](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [fuzzy-sökning](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [närhetssökning](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [termförstärkning](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [sökning med reguljära uttryck](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [sökning med jokertecken](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [syntaxgrunderna](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax) och [frågor med booleska operatorer](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).

## Ordna resultaten
När du får resultat från en sökfråga kan du begära att Azure Search visar resultaten ordnade efter värden i ett visst fält. Som standard ordnar Azure Search sökresultaten baserat på rangordningen för varje dokuments sökpoäng, som härleds från [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Om du vill att Azure Search ska returnera resultaten ordnade efter ett annat värde än sökpoängen kan du använda `orderby`-sökparametern. Du kan ange värdet för `orderby`-parametern så att den tar med fältnamn och anrop till [`geo.distance()`-funktionen](https://msdn.microsoft.com/library/azure/dn798921.aspx) för geospatiala värden. Varje uttryck kan åtföljas av `asc` om resultaten ska visas i stigande ordning eller `desc` om de ska visas i fallande ordning. Standardinställningen är stigande ordning.

## Sidindelning
I Azure Search kan du enkelt implementera sidindelning av sökresultaten. Genom att använda `top`- och `skip`-parametrarna kan du enkelt skicka sökförfrågningar som returnerar hela uppsättningen med sökresultat i hanterbara, ordnade delmängder som lägger grunden till bra sökgränssnittsmetoder. När du tar emot dessa mindre delmängder med resultat kan du också se antalet dokument i den fullständiga uppsättningen sökresultat.

Mer information om hur du använder sidindelning för sökresultat finns i artikeln [Sidindela sökresultat i Azure Search](search-pagination-page-layout.md).

## Träffmarkering
I Azure Search kan du enkelt framhäva exakt den del av sökresultatet som matchar sökfrågan med hjälp av parametrarna `highlight`, `highlightPreTag` och `highlightPostTag`. Du kan ange vilka *sökbara* fält vars matchade text ska framhävas samt ange de exakta strängtaggarna som ska läggas till i början och slutet av den matchade texten som Azure Search returnerar.

<!--HONumber=Sep16_HO3-->


