---
title: Så här arbetar du med Sök Resultat – Azure Search
description: Strukturera och sortera Sök resultat, hämta ett dokument antal och Lägg till innehålls navigering till Sök resultat i Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb86a75be464cb78a16170626bc96778d43bb8b6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67974625"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Så här arbetar du med Sök resultat i Azure Search
Den här artikeln innehåller vägledning om hur du implementerar standard element på en Sök resultat sida, till exempel totala antal, dokument hämtning, sorterings ordning och navigering. Sid-relaterade alternativ som bidrar med data eller information till dina Sök Resultat anges genom [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) förfrågningar som skickas till din Azure Search-tjänst. 

I REST API innehåller förfrågningarna GET-kommando, sökväg och frågeparametrar som informerar tjänsten vad som begärs och hur du formulerar svaret. I .NET SDK är motsvarande API [DocumentSearchResult-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Flera kod exempel innehåller ett webb klient dels gränssnitt, som du hittar här: [New York City-jobb demonstrations app](https://azjobsdemo.azurewebsites.net/) och [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> En giltig begäran innehåller ett antal element, till exempel en tjänst-URL och sökväg, HTTP-verb `api-version`, och så vidare. För det kortfattat trimmade vi exemplen för att markera bara den syntax som är relevant för sid brytning. Mer information om syntax för begäran finns i [Azure Search Service rest](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Totalt antal träffar och sid antal

Visar det totala antalet resultat som returnerades från en fråga och sedan returnerade resultaten i mindre segment, är grundläggande för nästan alla Sök sidor.

![][1]

I Azure Search använder `$count`du parametrarna, `$top`och `$skip` för att returnera dessa värden. I följande exempel visas en exempel förfrågan om totala antalet träffar i ett index som kallas "online-Catalog", `@odata.count`returnerat som:

    GET /indexes/online-catalog/docs?$count=true

Hämta dokument i grupper om 15 och Visa totalt antal träffar, från första sidan:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Sid brytnings resultat kräver `$top` både `$skip`och, `$top` där anger hur många objekt som ska returneras i en batch `$skip` och anger hur många objekt som ska hoppas över. I följande exempel visar varje sida de närmaste 15 objekten, vilket indikeras av de stegvisa hoppen i `$skip` parametern.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

På en Sök resultat sida kanske du vill visa en miniatyr bild, en delmängd av fälten och en länk till en fullständig produkt sida.

 ![][2]

I Azure Search använder `$select` du och en söknings- [API-begäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) för att implementera den här upplevelsen.

Returnera en delmängd av fält för en sida vid sida-layout:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Bilder och mediefiler är inte direkt sökbara och ska lagras på en annan lagrings plattform, till exempel Azure Blob Storage, för att minska kostnaderna. I index och dokument definierar du ett fält som lagrar URL-adressen för det externa innehållet. Du kan sedan använda fältet som en bild referens. URL: en till bilden ska vara i dokumentet.

Om du vill hämta en produkt beskrivnings sida för händelsen **onclick** använder du [Lookup-dokument](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) för att skicka in nyckeln till det dokument som ska hämtas. Nyckelns datatyp är `Edm.String`. I det här exemplet är det *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortera efter relevans, betyg eller pris

Sorterings ordningen används ofta som standard, men det är vanligt att göra alternativa sorterings beställningar tillgängliga så att kunder snabbt kan blanda befintliga resultat i en annan rangordnings ordning.

 ![][3]

I Azure Search baseras sorteringen på `$orderby` uttrycket för alla fält som indexeras som `"Sortable": true.` en `$orderby` -sats är ett OData-uttryck. Information om syntax finns i [syntax för OData-uttryck för filter och order by-satser](query-odata-filter-orderby-syntax.md).

Relevans är starkt kopplat till bedömnings profiler. Du kan använda standard poängen, som förlitar sig på text analys och statistik för att rangordna alla resultat, med högre resultat till dokument med fler eller starkare matchningar på en sökterm.

Alternativa sorterings ordningar är vanligt vis kopplade till **onclick** -händelser som anropar tillbaka till en metod som skapar sorterings ordningen. Till exempel har följande sid element angetts:

 ![][4]

Du kan skapa en metod som godkänner det valda sorterings alternativet som inmatat och returnerar en ordnad lista för de villkor som är associerade med det alternativet.

 ![][5]

> [!NOTE]
> Även om standard poängen är tillräcklig för många scenarier rekommenderar vi att du i stället vill basera relevansen på en anpassad bedömnings profil. En anpassad bedömnings profil ger dig ett sätt att öka de objekt som är mer fördelaktiga för ditt företag. Mer information finns i [lägga till bedömnings profiler](index-add-scoring-profiles.md) .
>

## <a name="faceted-navigation"></a>Aspektbaserad navigering

Sök navigering är vanligt på en resultat sida som ofta finns på sidan eller överst på en sida. I Azure Search tillhandahåller fasett navigering självriktad sökning baserat på fördefinierade filter. Mer information finns i avsnittet om [fasett-navigering i Azure Search](search-faceted-navigation.md) .

## <a name="filters-at-the-page-level"></a>Filter på sid nivå

Om din lösnings design innehåller dedikerade Sök sidor för specifika typer av innehåll (till exempel ett program för åter försäljning som innehåller avdelningar som visas överst på sidan) kan du infoga ett [filter uttryck](search-filters.md) tillsammans med en **onclick** -händelse för att öppna en sida i ett förfiltrerat tillstånd.

Du kan skicka ett filter med eller utan ett Sök uttryck. Följande begäran kommer till exempel att filtrera efter märkes namn och bara returnera de dokument som matchar det.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Mer information om `$filter` uttryck finns i [Sök efter dokument (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

## <a name="see-also"></a>Se även

- [Azure Search tjänst REST API](https://docs.microsoft.com/rest/api/searchservice)
- [Index åtgärder](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Dokument åtgärder](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Fasett-navigering i Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
