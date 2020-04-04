---
title: Så här arbetar du med sökresultat
titleSuffix: Azure Cognitive Search
description: Strukturera och sortera sökresultat, få ett antal dokument och lägg till innehållsnavigering i sökresultaten i Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: df80668f5e4a31d6247e9e9806e3de0667fd9036
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656015"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Så här arbetar du med sökresultat i Azure Cognitive Search

I den här artikeln beskrivs hur du får ett frågesvar som kommer tillbaka med ett totalt antal matchande dokument, sidnumrerade resultat, sorterade resultat och träffmarkerade termer.

Strukturen för ett svar bestäms av parametrar i frågan: [Sökdokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) i REST API, eller [DocumentSearchResult-klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) i .NET SDK.

## <a name="result-composition"></a>Resultatsammansättning

Ett sökdokument kan bestå av ett stort antal fält, men vanligtvis behövs bara ett fåtal för att representera varje dokument i resultatuppsättningen. Lägg `$select=<field list>` till i en frågebegäran för att ange vilka fält som ska visas i svaret. Ett fält måste tillskrivas som **hämtningsbart** i indexet för att inkluderas i ett resultat. 

Fält som fungerar bäst inkluderar de som kontrasterar och skiljer mellan dokument, vilket ger tillräckligt med information för att bjuda in ett klicksvar från användarens sida. På en e-handelsplats kan det vara ett produktnamn, beskrivning, varumärke, färg, storlek, pris och betyg. För det inbyggda exemplet på hotell-sample-index kan det vara fält i följande exempel:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Om du vill inkludera bildfiler i ett resultat, till exempel ett produktfoto eller en logotyp, lagrar du dem utanför Azure Cognitive Search, men inkluderar ett fält i indexet för att referera till bild-URL:en i sökdokumentet. Exempel index som stöder bilder i resultaten inkluderar **realestate-sample-us** demo, med i denna [snabbstart,](search-create-app-portal.md)och [New York City Jobs demo app](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Växla resultat

Som standard returnerar sökmotorn upp till de första 50 matchningarna, vilket bestäms av sökpoängen om frågan är fulltextsökning eller i en godtycklig ordning för exakta matchningsfrågor.

Om du vill returnera ett `$top` annat `$skip` antal matchande dokument lägger du till och parametrar i frågebegäran. I följande lista förklaras logiken.

+ Lägg `$count=true` till för att få ett antal matchande dokument i ett index.

+ Returnera den första uppsättningen med 15 matchande dokument plus ett antal totalt antal matchningar:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Returnera den andra uppsättningen, hoppa över de första 15 `$top=15&$skip=15`för att få nästa 15: . Gör samma sak för den tredje uppsättningen av 15:`$top=15&$skip=30`

Resultaten av sidnumrerade frågor är inte garanterade att vara stabila om det underliggande indexet ändras. Växlingen ändrar `$skip` värdet för varje sida, men varje fråga är oberoende och fungerar på den aktuella vyn av data som den finns i indexet vid frågetid (med andra ord finns det ingen cachelagring eller ögonblicksbild av resultat, till exempel de som finns i en databas för allmänt ändamål).
 
Följande är ett exempel på hur du kan få dubbletter. Anta ett index med fyra dokument:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Anta nu att du vill att resultaten ska returneras två åt gången, sorterade efter betyg. Du skulle köra den här frågan för `$top=2&$skip=0&$orderby=rating desc`att få den första sidan med resultat: , producera följande resultat:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
Anta att ett femte dokument läggs till i indexet `{ "id": "5", "rating": 4 }`mellan frågeanrop: .  Kort därefter kör du en fråga för att `$top=2&$skip=2&$orderby=rating desc`hämta den andra sidan: och få dessa resultat:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Observera att dokument 2 hämtas två gånger. Detta beror på att det nya dokumentet 5 har ett större värde för klassificering, så det sorteras före dokument 2 och landar på första sidan. Även om det här beteendet kan vara oväntat är det typiskt för hur en sökmotor beter sig.

## <a name="ordering-results"></a>Ordna resultaten

För fulltextsökningsfrågor rangordnas resultaten automatiskt efter ett sökresultat, beräknat baserat på termfrekvens och närhet i ett dokument, med högre poäng som går till dokument som har fler eller starkare matchningar på en sökterm. 

Sökpoäng förmedlar allmän känsla av relevans, vilket återspeglar styrkan i matchen jämfört med andra dokument i samma resultatuppsättning. Poängen är inte alltid konsekventa från en fråga till en annan, så när du arbetar med frågor kan du märka små avvikelser i hur sökdokument ordnas. Det finns flera förklaringar till varför detta kan inträffa.

| Orsak | Beskrivning |
|-----------|-------------|
| Datavolatilitet | Indexinnehållet varierar när du lägger till, ändrar eller tar bort dokument. Termfrekvenser ändras när indexuppdateringar bearbetas med tiden, vilket påverkar sökpoängen för matchande dokument. |
| Flera repliker | För tjänster som använder flera repliker utfärdas frågor mot varje replik parallellt. Indexstatistiken som används för att beräkna en sökresultat beräknas per replik, med resultat kopplade och ordnade i frågesvaret. Repliker är mestadels speglar av varandra, men statistiken kan variera på grund av små skillnader i tillstånd. En replik kan till exempel ha tagit bort dokument som bidrar till deras statistik, som slogs samman från andra repliker. Vanligtvis är skillnader i statistik per replik mer märkbara i mindre index. |
| Identiska poäng | Om flera dokument har samma poäng kan någon av dem visas först.  |

### <a name="consistent-ordering"></a>Konsekvent beställning

Med tanke på flex i resultatordning, kanske du vill utforska andra alternativ om konsekvens är ett programkrav. Den enklaste metoden är att sortera efter ett fältvärde, till exempel betyg eller datum. För scenarier där du vill sortera efter ett visst fält, till exempel [ `$orderby` ](query-odata-filter-orderby-syntax.md)en klassificering eller ett datum, kan du uttryckligen definiera ett uttryck som kan tillämpas på alla fält som är indexerade som **sorterbara**.

Ett annat alternativ är att använda en [anpassad bedömningsprofil](index-add-scoring-profiles.md). Poängsättningsprofiler ger dig större kontroll över rangordningen av objekt i sökresultaten, med möjlighet att öka matchningar som finns i specifika fält. Den ytterligare poängsättningslogiken kan hjälpa till att åsidosätta mindre skillnader mellan repliker eftersom sökpoängen för varje dokument är längre ifrån varandra. Vi rekommenderar [rankningsalgoritmen](index-ranking-similarity.md) för den här metoden.

## <a name="hit-highlighting"></a>Träffmarkering

Med träffmarkering avses textformatering (till exempel fet eller gul markering) som används för att matcha termen i ett resultat, vilket gör det enkelt att upptäcka matchen. Instruktioner för träffmarkering finns på [frågebegäran](https://docs.microsoft.com/rest/api/searchservice/search-documents). Sökmotorn omsluter den matchande termen `highlightPreTag` i `highlightPostTag`taggar och , och koden hanterar svaret (till exempel med hjälp av ett fetstilt teckensnitt).

Formatering tillämpas på hela termfrågor. I följande exempel är termerna "sandy", "sand", "stränder", "beach" som finns i fältet Beskrivning märkta för markering. Frågor om partiella termer, till exempel fuzzy search eller jokerteckensökning som resulterar i frågeexpansion i motorn, kan inte använda träffmarkering.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> Tjänster som skapas efter den 15 juli 2020 kommer att ge en annan belysningsupplevelse. Tjänster som skapats före det datumet ändras inte i markeringsbeteendet. Med den här ändringen returneras endast fraser som matchar den fullständiga frasfrågan. Det är också möjligt att ange den fragmentstorlek som returneras för högdager.
>
> När du skriver klientkod som implementerar träffmarkering bör du vara medveten om den här ändringen. Observera att detta inte påverkar dig om du inte skapar en helt ny söktjänst.

## <a name="next-steps"></a>Nästa steg

Om du snabbt vill skapa en söksida för klienten bör du tänka på följande alternativ:

+ [Application Generator](search-create-app-portal.md), i portalen, skapar en HTML-sida med ett sökfält, fasterad navigering och resultatområde som innehåller bilder.
+ [Skapa din första app i C#](tutorial-csharp-create-first-app.md) är en handledning som bygger en funktionell klient. Exempelkod visar sidnumrerade frågor, tryckmarkering och sortering.

Flera kodexempel inkluderar ett webbside-gränssnitt, som du hittar här: [New York City Jobs demoapp,](https://aka.ms/azjobsdemo) [JavaScript-exempelkod med en live demo-webbplats](https://github.com/liamca/azure-search-javascript-samples)och [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).