---
title: "Hur du sidan Sökresultat i Azure Search | Microsoft Docs"
description: "Sidbrytning i Azure Search värdbaserade moln search-tjänsten på Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: a0a1d315-8624-4cdf-b38e-ba12569c6fcc
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: heidist
ms.openlocfilehash: 1054e15a2751c53aad5dbc8054c4cec41102dee9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-page-search-results-in-azure-search"></a>Arbeta med sökresultatsidor i Azure Search
Den här artikeln innehåller råd om hur du använder den Azure Söktjänsts-REST API för att implementera standardelement för en sida med sökresultat, till exempel totalt antal, hämta dokument, sorteringsordningar och navigering.

I samtliga fall som anges nedan, sidrelaterade som bidrar med data eller information på sidan med sökresultatet anges via den [Sök efter dokument](http://msdn.microsoft.com/library/azure/dn798927.aspx) förfrågningar som skickas till din Azure Search-tjänsten. Begäranden innefattar en GET-command, sökväg, och frågeparametrar som information om vad som efterfrågas tjänsten samt hur du formulera svaret.

> [!NOTE]
> En giltig begäran innehåller ett antal element, till exempel en URL: en och sökväg, HTTP-verb `api-version`och så vidare. Planeringsaspekter bort vi exempel för att fokusera på just den syntax som är relevant för sidbrytning. Finns det [Azure Söktjänsts-REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) dokumentationen för mer information om begäran-syntax.
> 
> 

## <a name="total-hits-and-page-counts"></a>Totalt antal träffar och sida räknar
Visar det totala antalet resultat som returnerats från en fråga och returnerar sedan resultaten i mindre segment är grundläggande för nästan alla sidor i sökningen.

![][1]

I Azure Search använder du den `$count`, `$top`, och `$skip` parametrar för att returnera dessa värden. I följande exempel visas ett exempel på begäran för totala träffar som `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Hämta dokument i grupper med 15, och även visar de totala träffar början vid den första sidan:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Paginating resultat kräver båda `$top` och `$skip`, där `$top` anger hur många objekt för att returnera i en batch och `$skip` anger hur många objekt om du vill hoppa över. I följande exempel visar objekt som nästa 15 varje anges av inkrementell hoppar i den `$skip` parameter.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout
På en sida med sökresultat, kanske du vill visa en miniatyrbild, en delmängd av fält och en länk till en fullständig produktsida.

 ![][2]

Du vill använda i Azure Search `$select` och sökning för att implementera den här upplevelsen.

Returnera en delmängd av fält för en sida vid sida layout:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Bilder och mediefiler är inte direkt sökbara och ska lagras i en annan plattform för lagring, till exempel Azure Blob storage att minska kostnaderna. Definiera ett fält som lagrar URL-adressen för det externa innehållet i index och dokument. Du kan sedan använda fältet som en bildreferens till en. URL till bilden som ska vara i dokumentet.

Att hämta en beskrivning produktsidan för en **onClick** händelse, Använd [sökning dokumentet](http://msdn.microsoft.com/library/azure/dn798929.aspx) att skicka i dokumentet för att hämta nyckeln. Datatypen för nyckeln är `Edm.String`. I det här exemplet är det *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortera efter relevans, klassificering eller pris
Sorteringsordningar ofta standard relevans, men det är vanligt att skapa alternativa sortera order är tillgänglig så att kunder kan snabbt blandar befintliga resultat i en annan rank ordning.

 ![][3]

I Azure Search sortering baseras på den `$orderby` uttryck för alla fält som indexeras som`"Sortable": true.`

Relevans är starkt kopplat till bedömningen profiler. Du kan använda standard bedömningen som bygger på text analys och statistik för att rangordnas ordning alla resultat med högre resultat ska dokument med flera eller starkare matchningar på en sökterm.

Alternativa sorteringsordningar är vanligtvis kopplad till **onClick** händelser som anropa en metod som skapar sorteringsordning. Till exempel få den här sidelement:

 ![][4]

Du skapar en metod som accepterar valda sorteringsalternativet som indata och returnerar en sorterad lista för de kriterier som är associerade med det alternativet.

 ![][5]

> [!NOTE]
> Standard bedömningen är tillräcklig för många scenarier, rekommenderar vi basera relevans på en anpassad bedömningsprofilen i stället. En anpassad bedömningsprofilen ger dig ett sätt att förstärka-objekt som är bättre för ditt företag. Se [lägga till en bedömningsprofilen](http://msdn.microsoft.com/library/azure/dn798928.aspx) för mer information. 
> 
> 

## <a name="faceted-navigation"></a>Aspektbaserad navigering
Sök navigering är vanliga på en resultatsida ofta finns på sidan eller längst upp på sidan. I Azure Search ger fasetterad navigering automatisk dirigerad sökningen baserat på fördefinierade filter. Se [fasetterad navigering i Azure Search](search-faceted-navigation.md) mer information.

## <a name="filters-at-the-page-level"></a>Filter på enskilda sidor
Om en lösningsdesign ingår dedikerade söksidor för specifika typer av innehåll (till exempel ett online retail program som har avdelningar som visas längst upp på sidan), kan du infoga ett filteruttryck tillsammans med en **onClick** händelsen för att öppna en sida i en filtrerad tillstånd. 

Du kan skicka ett filter med eller utan ett sökuttryck. Till exempel filtrerar följande begäran på varumärke, returnerar endast de dokument som matchar den.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Se [Sök dokument (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) för mer information om `$filter` uttryck.

## <a name="see-also"></a>Se även
* [Azure Söktjänsts-REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)
* [Indexåtgärder](http://msdn.microsoft.com/library/azure/dn798918.aspx)
* [Dokumentet åtgärder](http://msdn.microsoft.com/library/azure/dn800962.aspx)
* [Video och självstudier om Azure Search](search-video-demo-tutorial-list.md)
* [Fasetterad navigering i Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
