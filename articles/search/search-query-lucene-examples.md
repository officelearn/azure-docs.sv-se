---
title: Lucene fråga exempel för Azure Search | Microsoft Docs
description: Lucene frågesyntaxen för fuzzy sökning, närhet sökning, termen förstärkning, reguljära uttryck och sökning med jokertecken.
author: LiamCa
manager: jlembicz
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: liamca
ms.openlocfilehash: 46e03834cb307ea103a8794616f6f38227881272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190098"
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Lucene fråga syntaxexemplen för att skapa frågor i Azure Search
När man skapar frågor för Azure Search kan du använda antingen standard [enkel frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) eller alternativt [Lucene Frågeparsern i Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Frågeparsern Lucene stöder mer komplex fråga konstruktioner fältet omfång frågor, fuzzy sökning, närhet sökning, termen förstärkning och reguljärt uttryck för sökning.

I den här artikeln kan du gå igenom exemplen visar frågeåtgärder som är tillgängliga när du använder den fullständiga syntaxen.

## <a name="viewing-the-examples-in-jsfiddle"></a>Visa exemplen i JSFiddle

Alla exemplen i den här artikeln är körbara frågor som körs mot en förinstallerade sökindex i [JSFiddle](https://jsfiddle.net), Kodredigerare online för att testa skript och HTML. 

Om du vill köra dem, högerklickar du på frågan exempel URL: er att öppna JSFiddle i ett nytt webbläsarfönster.

> [!NOTE]
> I följande exempel utnyttjar en sökindex som består av jobb tillgängliga baserat på en datamängd som tillhandahålls av den [stad New York OpenData](https://nycopendata.socrata.com/) initiativ. Dessa data ska inte ses aktuella eller slutförd. Indexet är på en sandbox-tjänst som tillhandahålls av Microsoft. Du behöver inte en Azure-prenumeration eller Azure Search försöka frågorna.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Hur du anropa fullständig Lucene parsning

Alla exemplen i den här artikeln anger den **queryType = full** sökparameter, som anger att den fullständiga syntaxen hanteras av Lucene Frågeparsern. 

**Exempel 1** --högerklickar du på frågan följande fragment för att öppna den i en ny Webbläsarsida som läser in JSFiddle och kör frågan:

* [& queryType = fullständig & Sök = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

I nytt fönster i webbläsaren visas JavaScript käll- och HTML-utdata sida vid sida. Skriptet refererar till en fullständig fråga (inte bara fragment, som visas i länken). Den fullständiga frågan som visas i URL: er för varje exempel. 

Den här frågan returnerar dokument från indexet New York City jobb (nycjobs, har lästs in på en sandbox-tjänst). Frågan anger planeringsaspekter, endast företag titlar returneras. Den fullständiga underliggande frågan är följande:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Den **searchFields** parametern begränsar sökningen till bara företag rubrikfält. Den **queryType** är inställd på **fullständig**, vilket gör att Azure sökning Frågeparsern Lucene för den här frågan.

> [!NOTE]
> Bakgrundsinformation om frågebearbetning finns [hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md). Mer information om sökparametrarna finns [Sök dokument (Azure Söktjänsts-REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Fielded frågeåtgärden
Du kan ändra exemplen i den här artikeln genom att ange en **fieldname:searchterm** konstruktionen för att definiera en fielded frågeåtgärden där fältet är ett enstaka ord och sökordet är också ett ord eller en fras, eventuellt med Booleska operatorer. Följande är några exempel:

* business_title:(senior NOT junior)
* status: (”New York” och ”nya Jersey”)

Se till att placera flera strängar inom citattecken om du vill att båda strängar som ska utvärderas som en enda enhet, som det här fallet söker efter två olika orter i Platsfältet. Kontrollera också operatorn versal som du ser med inte och och.

Det fält som anges i **fieldname:searchterm** måste vara sökbara fält. Se [Create Index (Azure Söktjänsts-REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) information om hur indexattribut används i fältdefinitioner.

**Exempel 2** --högerklickar du på följande fråga kodavsnitt som den här frågan söker för företag med termen erfarna i dem, men inte oerfarna:

* [& queryType = fullständig & Sök = business_title:senior inte oerfarna](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Fuzzy Sök-exempel
En fuzzy sökning matchningar har hittats i villkor som har en liknande konstruktion. Per [Lucene dokumentationen](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), fuzzy sökningar baseras på [Damerau Levenshtein avstånd](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Om du vill göra en fuzzy sökning, bifoga tilde ”~” symbol i slutet av ett ord med en valfri parameter, ett värde mellan 0 och 2, som anger avståndet redigera. Till exempel ”blå ~” eller ”blå ~ 1” returnerar blått, blå och ibland sammanlänkande.

**Exempel 3** --högerklickar du på följande kodavsnitt i frågan. Den här frågan söker efter jobb med termen-koppla (där det är felstavat):

* [& queryType = fullständig & Sök = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Fuzzy frågor är inte [analyseras](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), vilket kan vara konstigt om du räknar härrör eller lemmatisering. Lexikaliskt analys utförs endast på fullständiga villkor (en term fråga eller en frasfråga). Frågetyper med ofullständiga villkor (prefix frågan, jokertecken frågan, regex-fråga, fuzzy fråga) läggs direkt till trädet frågan kringgå fasen analys. Endast omvandling utförs på ofullständiga sökord lowercasing.
>

## <a name="proximity-search-example"></a>Närhet Sök-exempel
Närhet sökningar används för att hitta villkoren som är nära varandra i ett dokument. Infoga tilde ”~” symbol i slutet av en fras följt av antalet ord som skapar närhet gräns. Till exempel ”hotell flygplats” ~ 5 hittar villkoren hotell och flygplats inom 5 ord från varandra i ett dokument.

**Exempel 4** --högerklickar du på frågan. Sök efter jobb med termen ”erfarna analytiker” där det avgränsas med mer än ett ord:

* [& queryType = fullständig & Sök = business_title: ”erfarna analytiker” ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exempel 5** --prova igen att ta bort ord mellan termen ”erfarna analytiker”.

* [& queryType = fullständig & Sök = business_title: ”erfarna analytiker” ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Termen förstärkning exempel
Termen förstärkning avser rangordning dokumentet senare om den innehåller boosted termen i förhållande till dokument som inte innehåller termen. Detta skiljer sig från bedömningen profiler i att bedömningsprofil profiler öka vissa fält i stället för på specifika villkor. I följande exempel hjälper visar skillnaderna.

Överväg att bedömningsprofilen som förstärker matchar i ett visst fält som **genre** i musicstoreindex exempel. Termen förstärkning kan användas för att ytterligare förbättra vissa Sök termer som är högre än andra. Till exempel ”Berg ^ 2 elektronisk” förbättras dokument som innehåller sökorden i den **genre** fält som är högre än andra sökbara fält i indexet. Dessutom kommer dokument som innehåller söktermen ”Berg” att rangordnas högre än andra söktermen ”elektronisk” på grund av termen förstärkningen värde (2).

Använd hatt, om du vill öka en term ”^”, symbol med en faktor förstärkningen (ett nummer) i slutet av termen som du söker. Ju högre förstärkningen-faktor, mer relevant ska vara i förhållande till andra söktermer. Som standard är förstärkningen faktorn 1. Även om förstärkningen faktorn måste vara positivt, kan det vara mindre än 1 (till exempel 0,2).

**Exempel 6** --högerklickar du på frågan. Sök efter jobb med termen ”datorn analytiker” där det finns inga resultat med både ord dator och analytiker ännu analytiker jobb är högst upp i resultaten.

* [& queryType = fullständig & Sök = business_title:computer analytiker](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exempel 7** --försök igen, den här gången förstärkning innebär med termen datorn över termen analytiker om båda orden inte finns.

* [& queryType = fullständig & Sök = business_title:computer ^ 2 analytiker](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Reguljärt uttryck exempel
Ett reguljärt uttryck hittas matchar baserat på innehållet mellan snedstreck ”/”, som beskrivs i den [klassen RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exempel 8** --högerklickar du på frågan. Sök efter jobb med antingen termen chef eller sitt första år.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

URL-Adressen för det här exemplet kommer inte att återge korrekt på sidan. Kopiera Webbadressen nedan och klistra in den i webbläsaren URL-adressen som en lösning: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Jokertecken Sök-exempel
Du kan använda allmänt erkända syntax för flera (\*) eller enstaka jokertecken med tecken (?). Observera frågeparsern Lucene stöder användning av dessa symboler med en enda term och inte en fras.

**Exempel 9** --högerklickar du på frågan. Sök efter jobb som innehåller prefixet 'prog' som omfattar business titlar med villkor som programming programmerare i den.

* [& queryType = fullständiga & $select = business_title & Sök = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

Du kan inte använda en * eller? symbolen som det första tecknet i en sökning.

## <a name="next-steps"></a>Nästa steg
Försök att ange Frågeparsern Lucene i din kod. Följande länkar beskriver hur du ställer in sökfrågor för både .NET och REST-API. Länkarna med enkel syntax standard så du måste tillämpa det du lärt dig från den här artikeln för att ange den **queryType**.

* [Fråga ditt Azure Search Index med .NET SDK](search-query-dotnet.md)
* [Fråga ditt Azure Search Index med hjälp av REST-API](search-query-rest-api.md)

## <a name="see-also"></a>Se också

 [Hur full textsökning fungerar i Azure Search](search-lucene-query-architecture.md)