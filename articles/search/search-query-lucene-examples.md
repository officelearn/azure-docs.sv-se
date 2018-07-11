---
title: Exempel på Lucene-syntaxfråga för Azure Search | Microsoft Docs
description: Lucene-frågesyntax för fuzzy-sökning, närhetssökning, termförstärkning, sökning med reguljära uttryck och sökning med jokertecken.
author: LiamCa
manager: pablocas
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: liamca
ms.openlocfilehash: a3baa17906e3bfede8a7fc5f8a0bfbde9d2a57ce
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951030"
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exempel på Lucene-fråga för att skapa frågor i Azure Search
När konstruera frågor för Azure Search kan du använda antingen standard [enkla frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) eller alternativet [frågeparser (Lucene) i Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Den frågeparser (Lucene) stöder mer komplex fråga konstruktioner som fältbegränsade frågor, fuzzy-sökning, närhetssökning, termförstärkning och sökning med reguljära uttryck.

I den här artikeln kan du gå igenom exempel som visar frågeåtgärder som är tillgängliga när du använder den fullständiga syntaxen.

## <a name="viewing-the-examples-in-jsfiddle"></a>Visa exemplen i JSFiddle

Alla exemplen i den här artikeln är körbara frågor som körs mot en förinstallerade Search-index i [JSFiddle](https://jsfiddle.net), Kodredigerare online för att testa skript och HTML. 

Kör dem genom att högerklicka på frågan exempel URL: er att öppna JSFiddle i ett separat webbläsarfönster.

> [!NOTE]
> I följande exempel utnyttja ett sökindex som består av jobben tillgängliga baserat på en datauppsättning som tillhandahålls av den [stad New York OpenData](https://nycopendata.socrata.com/) initiativ. Dessa data ska inte betraktas aktuella eller klar. Indexet ligger på en sandbox-tjänst som tillhandahålls av Microsoft. Du behöver inte en Azure-prenumeration eller ett Azure Search för att prova de här frågorna.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Hur du anropar fullständig Lucene parsning

Alla exemplen i den här artikeln anger de **queryType = full** sökparametern, som anger att den fullständiga syntaxen hanteras av den frågeparser (Lucene). 

**Exempel 1** --högerklickar du på frågan följande kodfragment för att öppna den i en ny Webbläsarsida som läser in JSFiddle och kör frågan:

* [& queryType = full & sökning = *](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

I det nya webbläsarfönstret visas JavaScript käll- och HTML-utdata sida vid sida. Skriptet refererar till en fullständig fråga (inte bara kodavsnittet, som visas i länken). Den fullständiga frågan som visas i URL: er för varje exempel. 

Den här frågan returnerar dokument från New York City jobb indexet (nycjobs, har lästs in på en sandbox-tjänst). Frågan anger endast företag rubriker som returneras av utrymmesskäl. Den fullständiga underliggande frågan är följande:

    https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Den **searchFields** parametern begränsar sökningen till bara rubrikfältet för företag. Den **queryType** är inställd på **fullständig**, vilket instruerar Azure Search för att använda den frågeparser (Lucene) för den här frågan.

> [!NOTE]
> Bakgrundsinformation om frågebearbetning, se [hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md). Läs mer på sökparametrar [söka efter dokument (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Fielded frågeåtgärden
Du kan ändra exemplen i den här artikeln genom att ange en **fieldname:searchterm** konstruktion att definiera en fielded frågeåtgärden där fältet är ett enstaka ord och söktermen är också ett enstaka ord eller en fras, eventuellt med Booleska operatorer. Följande är några exempel:

* business_title:(senior NOT junior)
* tillstånd: (”New York” och ”ny Jersey”)

Glöm inte att placera flera strängar inom citattecken om du vill att båda strängar som ska utvärderas som en enda enhet, som i det här fallet söker efter två olika städer i Platsfältet. Kontrollera också att operatören blir versal som du ser med inte och och.

Fält som anges i **fieldname:searchterm** måste vara ett sökbart fält. Se [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) mer information om hur indexattribut används i fältdefinitioner.

**Exempel 2** --högerklickar du på följande frågeavsnitt som den här frågan söker för företag med termen senior i dem, men inte unga:

* [& queryType = full & sökning = business_title:senior inte unga](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Fuzzy-sökning-exempel
En fuzzy-sökning söker efter matchningar i termer som har en liknande konstruktion. Per [Lucene dokumentation](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), fuzzy sökningar baseras på [Damerau Levenshtein avståndet](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Om du vill göra en fuzzy-sökning, lägger du till tilde ”~” symbolen i slutet av ett enstaka ord med en valfri parameter, ett värde mellan 0 och 2, som anger avståndet redigera. Exempelvis ”blå ~” eller ”blå ~ 1” kommer att returnera blå och blues sammanlänkande.

**Exempel 3** --högerklickar du på följande frågeavsnitt. Den här frågan söker efter jobb med termen-koppla (där det är felstavat):

* [& queryType = full & sökning = business_title:asosiate ~](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Fuzzy frågor är inte [analyseras](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), vilket kan vara så konstigt om du förväntar dig ordstamsigenkänning eller lemmatisering. Lexikal analys utförs endast på fullständiga villkor (en term fråga eller en frasfråga). Frågetyper med ofullständiga villkor (prefix fråga, jokerteckenfråga, regex-fråga, fuzzy-fråga) läggs till direkt i trädet fråga kringgår analysis-steg. Endast transformeringen utförs på ofullständig sökord platsargumentet.
>

## <a name="proximity-search-example"></a>Närhet search-exempel
Närhet sökningar används för att hitta villkor som är nära varandra i ett dokument. Infoga en tilde ”~” tecken i slutet av en fras följt av antalet ord som skapar närhet gränsen. Till exempel hotell flygplats ”” ~ 5 hittar villkoren hotell och flygplats inom 5 ord från varandra i ett dokument.

**Exempel 4** --högerklickar du på frågan. Sök efter jobb med termen ”senior analytiker” där den avgränsas med mer än ett ord:

* [& queryType = full & sökning = business_title: ”senior analytiker” ~ 1](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exempel 5** – prova igen tar du bort ord mellan termen ”senior analytiker”.

* [& queryType = full & sökning = business_title: ”senior analytiker” ~ 0](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Termen boosting exempel
Termförstärkning avser rangordning ett dokument som är högre om den innehåller förbättrat termen, i förhållande till dokument som inte innehåller termen. Detta skiljer sig från poängprofiler i att bedömningsprofiler öka vissa fält i stället för på specifika villkor. I följande exempel hjälper visar skillnaderna.

Överväg en bedömningsprofil som förstärker matchar i ett visst fält som **genre** i musicstoreindex-exemplet. Termförstärkning kan användas för att ytterligare höja vissa search villkor som är högre än andra. Till exempel ”rock ^ 2 elektronisk” förbättras dokument som innehåller sökvillkor i den **genre** fält som är högre än andra sökbara fält i indexet. Dessutom kommer dokument som innehåller söktermen ”rock” att rangordnas högre än andra söktermen ”elektronisk” till följd av termen boost värdet (2).

Om du vill öka en term använder du cirkumflex ”^”, symbol med en faktor boost (ett tal) i slutet av perioden som du söker. Ju högre boost-faktor, desto viktigare blir termen i förhållande till andra söktermer. Som standard är faktorn boost 1. Även om faktorn boost måste vara positivt, kan det vara mindre än 1 (till exempel, 0.2).

**Exempel 6** --högerklickar du på frågan. Sök efter jobb med termen ”datorn analytiker” där vi se att det finns inga resultat med både ord datorn och analytiker, men analytiker jobben är högst upp på resultaten.

* [& queryType = full & sökning = business_title:computer analytiker](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exempel 7** --det igen, den här gången boosting innebär termen-dator över termen analytiker om båda ord som inte finns.

* [& queryType = full & sökning = business_title:computer ^ 2 analytiker](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Reguljärt uttryck exempel
En sökning med reguljära uttryck hittar en matchning baserat på innehållet mellan snedstreck ”/”, som beskrivs i den [klassen RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exempel 8** --högerklickar du på frågan. Sök efter jobb med antingen termen Senior eller sitt första år.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

URL-Adressen för det här exemplet kommer inte att återges korrekt på sidan. Som en lösning kan du kopiera URL nedan och klistra in den i webbläsarens URL-adress: `https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Jokertecken search-exempel
Du kan använda allmänt erkända syntax för flera (\*) eller enstaka (?) tecken jokertecken. Observera den frågeparser (Lucene) stöder användning av dessa symboler med en enda term och inte en fras.

**Exempel 9** --högerklickar du på frågan. Sök efter jobb som innehåller prefixet ”prog' som skulle inkludera rubriker för företag med villkor programming programmerare i den.

* [& queryType = full & $select = business_title & search = business_title:prog*](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

Du kan inte använda en * eller? symbol som det första tecknet i en sökning.

## <a name="next-steps"></a>Nästa steg
Försök att ange den frågeparser (Lucene) i din kod. Följande länkar beskriver hur du ställer in sökfrågor för både .NET och REST-API. Länkarna använder standard enkel syntax så behöver du tillämpa det du lärt dig från den här artikeln för att ange den **queryType**.

* [Fråga ditt Azure Search-Index med .NET SDK](search-query-dotnet.md)
* [Fråga ditt Azure Search-Index med hjälp av REST-API](search-query-rest-api.md)

## <a name="see-also"></a>Se också

 [Hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md)