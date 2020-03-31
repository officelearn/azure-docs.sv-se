---
title: Så här arbetar du med sökresultat
titleSuffix: Azure Cognitive Search
description: Strukturera och sortera sökresultat, få ett antal dokument och lägg till innehållsnavigering i sökresultaten i Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481610"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Så här arbetar du med sökresultat i Azure Cognitive Search
Den här artikeln innehåller vägledning om hur du implementerar standardelement på en sökresultatsida, till exempel antal, dokumenthämtning, sorteringsorder och navigering. Sidrelaterade alternativ som bidrar med data eller information till sökresultaten anges i [sökdokumentbegäranden](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) som skickas till din Azure Cognitive Search-tjänst. 

I REST API innehåller begäranden ett GET-kommando, sökväg och frågeparametrar som informerar tjänsten om vad som begärs och hur du formulerar svaret. I .NET SDK är motsvarande API [Klassen DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Om du snabbt vill skapa en söksida för din klient kan du utforska följande alternativ:

+ Använd [programgeneratorn](search-create-app-portal.md) i portalen för att skapa en HTML-sida med ett sökfält, fasterad navigering och resultatområde.
+ Skapa en funktionell klient genom att följa med i självstudien [Skapa din första app i C#.](tutorial-csharp-create-first-app.md)

Flera kodexempel inkluderar ett webbside-gränssnitt, som du hittar här: [New York City Jobs demoapp,](https://aka.ms/azjobsdemo) [JavaScript-exempelkod med en live demo-webbplats](https://github.com/liamca/azure-search-javascript-samples)och [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> En giltig begäran innehåller ett antal element, till exempel en `api-version`tjänst-URL och sökväg, HTTP-verb och så vidare. För korthet, trimmade vi exemplen för att markera bara syntax som är relevant för sidnumrering. Mer information om syntax för begäran finns i [Azure Cognitive Search REST API:er](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Totalt antal träffar och sidantal

Att visa det totala antalet resultat som returneras från en fråga och sedan returnera dessa resultat i mindre segment är grundläggande för praktiskt taget alla söksidor.

![][1]

I Azure Cognitive Search `$count`använder `$top`du `$skip` parametrarna och parametrarna för att returnera dessa värden. I följande exempel visas en exempelbegäran för totala träffar på ett `@odata.count`index som kallas "onlinekatalog", returnerad som :

    GET /indexes/online-catalog/docs?$count=true

Hämta dokument i grupper om 15 och visa även den totala träffar, med början på första sidan:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Sidnumrerande resultat `$top` `$skip`kräver `$top` både och , där anger hur `$skip` många artiklar som ska returneras i en batch och anger hur många artiklar som ska hoppa. I följande exempel visar varje sida de följande 15 objekten, som `$skip` indikeras av de inkrementella hoppen i parametern.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

På en sökresultatsida kanske du vill visa en miniatyrbild, en delmängd av fälten och en länk till en fullständig produktsida.

 ![][2]

I Azure Cognitive Search `$select` använder du och en [begäran om sök-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) för att implementera den här upplevelsen.

Så här returnerar du en delmängd av fälten för en sida vid sida-layout:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Avbildningar och mediefiler är inte direkt sökbara och bör lagras i en annan lagringsplattform, till exempel Azure Blob-lagring, för att minska kostnaderna. I indexet och dokumenten definierar du ett fält som lagrar URL-adressen för det externa innehållet. Du kan sedan använda fältet som en bildreferens. URL:en till bilden ska finnas i dokumentet.

Om du vill hämta en produktbeskrivningssida för en **onClick-händelse** använder du [Uppslagsdokument](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) för att skicka in nyckeln till dokumentet som ska hämtas. Nyckelns datatyp är `Edm.String`. I det här exemplet är det *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortera efter relevans, betyg eller pris

Sortera order som ofta är standard för relevans, men det är vanligt att göra alternativa sorteringsorder lättillgängliga så att kunderna snabbt kan blanda om befintliga resultat till en annan rangordning.

 ![][3]

I Azure Cognitive Search baseras sortering på uttrycket, `$orderby` för `"Sortable": true.` alla `$orderby` fält som indexeras som en sats är ett OData-uttryck. Information om syntax finns i [OData-uttryckssyntax för filter och order-by-satser](query-odata-filter-orderby-syntax.md).

Relevans är starkt förknippat med bedömningsprofiler. Du kan använda standardbedömningen, som bygger på textanalys och statistik för att rangordna ordning på alla resultat, med högre poäng som går till dokument med fler eller starkare matchningar på en sökterm.

Alternativa sorteringsorder associeras vanligtvis med **onClick-händelser** som anropar tillbaka till en metod som skapar sorteringsordningen. Med tanke på det här sidelementet:

 ![][4]

Du skapar en metod som accepterar det valda sorteringsalternativet som indata och returnerar en ordnad lista för de villkor som är associerade med det alternativet.

 ![][5]

> [!NOTE]
> Standardbedömningen är tillräcklig för många scenarier, men vi rekommenderar att du baserar relevans på en anpassad bedömningsprofil i stället. En anpassad bedömningsprofil ger dig ett sätt att marknadsföra objekt som är mer fördelaktiga för ditt företag. Mer information finns [i Lägga till bedömningsprofiler.](index-add-scoring-profiles.md)
>

## <a name="hit-highlighting"></a>Träffmarkering

Du kan använda formatering för att matcha termer i sökresultaten, vilket gör det enkelt att upptäcka matchningen. Instruktioner för träffmarkering finns på [frågebegäran](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Formatering tillämpas på hela termfrågor. Frågor om partiella termer, till exempel fuzzy search eller jokerteckensökning som resulterar i frågeexpansion i motorn, kan inte använda träffmarkering.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> Tjänster som skapas efter den 15 juli 2020 kommer att ge en annan belysningsupplevelse. Tjänster som skapats före det datumet ändras inte i markeringsbeteendet. Med den här ändringen returneras endast fraser som matchar den fullständiga frasfrågan. Det är också möjligt att ange den fragmentstorlek som returneras för högdager.
>
> När du skriver klientkod som implementerar träffmarkering bör du vara medveten om den här ändringen. Observera att detta inte påverkar dig om du inte skapar en helt ny söktjänst.

## <a name="faceted-navigation"></a>Aspektbaserad navigering

Söknavigering är vanligt på en resultatsida, som ofta finns längst upp på en sida eller överst på en sida. I Azure Cognitive Search ger aspektbaserad navigering självstyrd sökning baserat på fördefinierade filter. Mer information finns [i Fasterad navigering i Azure Cognitive Search.](search-faceted-navigation.md)

## <a name="filters-at-the-page-level"></a>Filter på sidnivå

Om lösningsdesignen innehöll dedikerade söksidor för specifika typer av innehåll (till exempel ett online-butiksprogram som har avdelningar listade högst upp på sidan) kan du infoga ett [filteruttryck](search-filters.md) tillsammans med en **onClick-händelse** för att öppna en sida i förfiltrerade tillstånd.

Du kan skicka ett filter med eller utan ett sökuttryck. Följande begäran filtrerar till exempel på varumärkesnamn och returnerar endast de dokument som matchar den.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Mer information om `$filter` uttryck finns i [Sökdokument (Azure Cognitive Search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

## <a name="see-also"></a>Se även

- [REST-API:er för Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice)
- [Index Verksamhet](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Dokumentåtgärder](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Fasterad navigering i Azure Cognitive Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
