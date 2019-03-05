---
title: Lägg till förslagsställare till ett Azure Search-index
description: Gör det möjligt för fält för frågeifyllningsförslag fråga åtgärder, där föreslagna frågor består av texten från fält i ett Azure Search-index.
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fd4b29134fd45ed2888fbc81ded413ecf7286959
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308660"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Lägg till förslagsställare till ett Azure Search-index

En **förslagsställare** är en Azure Search-konstruktion som stöder den ”sökning-som-du-typen” [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) funktionen och [automatisk komplettering (förhandsversion)](search-autocomplete-tutorial.md) funktionen. Innan du kan anropa API: et för förslag, måste du definiera en **förslagsställare** i ett index för att aktivera förslag på specifika fält.

Även om en **förslagsställare** har flera egenskaper, det är främst en uppsättning fält som du aktiverar den [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions). En reseapp kanske exempelvis vill aktivera typeahead sökning på mål och städer attraktioner. Därför måste övergår alla tre fält i samlingen fält.

Du kan bara ha en **förslagsställare** resurs för varje index (mer specifikt en **förslagsställare** i den **förslagsställare** samling).

## <a name="creating-a-suggester"></a>Skapa en förslagsställare

Du kan skapa en **förslagsställare** när som helst, men påverkan på ditt index varierar beroende på fälten.

+ Nya fält läggs till en förslagsställare som en del av samma uppdatering är minst påverkar eftersom inga index återskapning krävs.
+ Befintliga fält lagts till i en förslagsställare, men ändrar fältdefinition, vilket kräver återskapa indexet.

**Förslagsställare** fungerar bäst när de används för att föreslå specifika dokument i stället för formulärdata termer eller fraser. Bästa kandidat fälten är rubriker, namn och andra relativt korta fraser som kan identifiera ett objekt. Mindre effektiva är återkommande fält, till exempel kategorier och taggar, eller mycket långa fält, till exempel beskrivningar eller kommentarer fält.

När en förslagsställare har skapats kan du lägga till den [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions) i din fråga logik för att anropa funktionen.

Egenskaper som definierar en **förslagsställare** inkluderar följande:

|Egenskap |Beskrivning|
|--------------|-----------------|
|`name`|Namnet på den **förslagsställare**. Du använder namnet på den **förslagsställare** när du anropar den [förslag &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|
|`searchMode`|Den strategi som används för att söka efter kandidat fraser. Det enda läge som stöds för närvarande är `analyzingInfixMatching`, vilket genomför flexibla matchningar av fraser i början eller mitten meningar.|
|`sourceFields`|En lista över en eller flera fält som är källan till innehållet för förslag. Endast fält av typen `Edm.String` och `Collection(Edm.String)` kan vara källor förslag. Endast fält som inte har ett anpassat språkanalysverktyg ange kan användas. |

## <a name="suggester-example"></a>Exempel på förslagsställare
En **förslagsställare** ingår i indexdefinitionen. Endast en **förslagsställare** kan finnas i den **förslagsställare** samling i den aktuella versionen, tillsammans med den **fält** samling och **scoringProfiles**.

```
{
  "name": "hotels",
  "fields": [
    . . .
  ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ],
  "scoringProfiles": [
    . . .
  ]
}

```

## <a name="see-also"></a>Se också
[Skapa Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
[Uppdatera Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  
[Förslag &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  
[Indexera operations &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  
[Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)  
[Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)
