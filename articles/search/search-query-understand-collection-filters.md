---
title: Förstå OData-samlings filter – Azure Search
description: Förstå hur OData Collection-filter fungerar i Azure Search frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 5c3a0205f5a9ac5115e78f1bc11f70b2c50a9714
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647413"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Att förstå OData-samlingens filter i Azure Search

För att [filtrera](query-odata-filter-orderby-syntax.md) på samlings fält i Azure Search kan du använda [ `any` operatorerna `all` och](search-query-odata-collection-operators.md) tillsammans med Lambda- **uttryck**. Lambda-uttryck är booleska uttryck som refererar till en **intervall variabel**. Operatorerna `all` `for` och är likvärdiga med en loop i de flesta programmeringsspråk, med variabeln Range som tar roll för loop-variabeln och lambda-uttrycket som bröd texten i slingan. `any` Range-variabeln tar på det aktuella värdet för samlingen under iterationen av loopen.

Minst så fungerar det konceptuellt. I verkligheten implementerar Azure Search filter på ett mycket annorlunda sätt för hur `for` slingor fungerar. Vi rekommenderar att skillnaden är osynlig för dig, men i vissa fall är det inte. Slut resultatet är att det finns regler som du måste följa när du skriver lambda-uttryck.

I den här artikeln förklaras varför reglerna för samlings filter finns genom att utforska hur Azure Search kör dessa filter. Om du skriver avancerade filter med komplexa lambda-uttryck kan du hitta den här artikeln för att skapa din förståelse av vad som är möjligt med filter och varför.

Information om vad reglerna för samlings filter är, inklusive exempel finns i [Felsöka OData Collection filter i Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Varför samlings filter är begränsade

Det finns tre bakomliggande orsaker till varför inte alla filter funktioner stöds för alla typer av samlingar:

1. Endast vissa operatörer stöds för vissa data typer. Det är till exempel inte meningsfullt att jämföra de booleska värdena `true` och `false` använda `lt`, `gt`, och så vidare.
1. Azure Search stöder inte **korrelerad sökning** i fält av typen `Collection(Edm.ComplexType)`.
1. Azure Search använder inverterade index för att köra filter över alla typer av data, inklusive samlingar.

Den första orsaken är bara en följd av hur OData-språket och EDM-typ systemet definieras. De sista två förklaras i detalj i resten av den här artikeln.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelerad kontra korrelerad sökning

När du tillämpar flera filter villkor för en samling komplexa objekt, korreleras villkoren eftersom de gäller för *varje objekt i samlingen*. Följande filter kommer till exempel att returnera hotell som har minst ett Deluxe-rum med en hastighet som är lägre än 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Om filtreringenvar korrelerad kan filtret ovan returnera hotell där ett rum är Deluxe och ett annat rum har en bas taxa som är mindre än 100. Det skulle inte vara meningsfullt eftersom båda satserna i lambda-uttrycket gäller samma intervall variabel, nämligen `room`. Detta är anledningen till att sådana filter korreleras.

För full texts ökning finns det dock inget sätt att referera till en speciell intervall variabel. Om du använder fältet sökning för att utfärda en [fullständig Lucene-fråga](query-lucene-syntax.md) som den här:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Du kan få tillbaka hotell igen om ett rum är Deluxe och ett annat rum står "stads vy" i beskrivningen. Till exempel kan dokumentet nedan `Id` `1` matcha frågan:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Orsaken är att `Rooms/Type` alla analyserade villkor `Rooms/Type` för fältet i hela dokumentet och på samma sätt `Rooms/Description`som visas i tabellerna nedan.

Hur `Rooms/Type` lagras för full texts ökning:

| Villkor i`Rooms/Type` | Dokument-ID |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Hur `Rooms/Description` lagras för full texts ökning:

| Villkor i`Rooms/Description` | Dokument-ID |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| modernt | 1 |
| förstoring | 1 |
| Motel | 2 |
| rummet | 1, 2 |
| standard | 1 |
| uppsättning | 1 |
| visa | 1 |

Så till skillnad från filtret ovan, vilket i princip betyder att "matcha dokument där ett `Type` rum är lika med" Deluxe-rummet "och **att samma rum** har `BaseRate` mindre än 100", säger Sök frågan "matcha `Rooms/Type` dokument där termen" Deluxe "och `Rooms/Description` har frasen" stads vy ". Det finns inget begrepp för enskilda rum vars fält kan korreleras i det senare fallet.

> [!NOTE]
> Om du vill se stöd för korrelerad sökning som lagts till Azure Search kan du rösta på [röst posten för den här användaren](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Inverterade index och samlingar

Du kanske har märkt att det finns mycket färre begränsningar för Lambda-uttryck i komplexa samlingar än för enkla samlingar som `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`och så vidare. Detta beror på att Azure Search lagrar komplexa samlingar som faktiska samlingar av under dokument, medan enkla samlingar inte lagras som samlingar alls.

Du kan till exempel välja ett filter bara sträng samlings `seasons` fält som i ett index för en online-återförsäljare. Vissa dokument som överförs till det här indexet kan se ut så här:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Värdena i `seasons` fältet lagras i en struktur som kallas **inverterat index**, vilket ser ut ungefär så här:

| Term | Dokument-ID |
| --- | --- |
| upphängning | 1, 2 |
| Summer | 1 |
| kopplade | 1, 2 |
| vinter | 2, 3 |

Den här data strukturen är utformad för att svara på en fråga med hög hastighet: I vilka dokument visas en specifik term? Att besvara den här frågan fungerar mer som en vanlig likhets kontroll än en slinga över en samling. Detta är varför för sträng samlingar, Azure Search endast tillåter `eq` en jämförelse operator i ett lambda-uttryck för. `any`

Skapa upp från likhet, härnäst ska vi titta på hur det är möjligt att kombinera flera likhets kontroller i samma intervall variabel med `or`. Det fungerar tack vare algebra och [disfördelnings egenskapen för kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Det här uttrycket:

    seasons/any(s: s eq 'winter' or s eq 'fall')

motsvarar att:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

och var och en av `any` de två del uttrycken kan köras effektivt med det inverterade indexet. Detta uttryck är också tack vare [negations lagstiftningen för kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation):

    seasons/all(s: s ne 'winter' and s ne 'fall')

motsvarar att:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Därför är det möjligt att använda `all` med `ne` och `and`.

> [!NOTE]
> Även om informationen ligger utanför det här dokumentets omfattning, utökas samma principer till [avstånds-och skärnings test för samlingar med geo-spatiala punkter](search-query-odata-geo-spatial-functions.md) . Detta är varför, i `any`:
>
> - `geo.intersects`kan inte vara negationt
> - `geo.distance`måste jämföras med `lt` eller`le`
> - uttryck måste kombineras med `or`, inte`and`
>
> De omvända reglerna gäller för `all`.

En större mängd uttryck är tillåtna vid filtrering av samlingar med `lt`data typer som stöder operatorerna `le`, `gt`, och `ge` , till `Collection(Edm.Int32)` exempel. Mer specifikt kan du använda `and` `or` och i `any`, förutsatt att de underliggande jämförelse uttrycken kombineras till **intervall jämförelser** med `and`, som sedan kombineras med. `or` Den här strukturen av booleska uttryck kallas [Disjunctive normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), annars kallat "ORS of ANDs". Lambda-uttryck för `all` för dessa data typer måste däremot vara i [Conjunctive normal form (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), annars kallat "ANDs of ORS". Azure Search tillåter jämförelser av sådana intervall eftersom det kan köra dem med inverterade index effektivt, precis som det kan göra snabba sökning efter strängar.

Sammanfattnings vis är reglerna för tummen för vad som tillåts i ett lambda-uttryck:

- I `any`är *positiva kontroller* alltid tillåtna, t. ex. likhet, intervall jämförelser `geo.intersects`, `geo.distance` eller jämfört `lt` med `le` eller (Tänk på "nära") som likhet när det kommer att kontrol lera avstånd).
- Inuti `any`tillåtsalltid. `or` Du kan bara `and` använda för data typer som kan uttrycka intervall kontroller och endast om du använder ORS av ANDs (DNF).
- I `all`är reglerna inverterade – endast *negativa kontroller* tillåts, du kan använda `and` Always och du kan endast använda `or` för intervall kontroller som uttryckts som ANDs av ORS (CNF).

I praktiken är dessa de typer av filter som du är mest troligt att använda ändå. Det är fortfarande bra att förstå gränserna för vad som är möjligt trots detta.

Ett exempel på vilka typer av filter som är tillåtna och vilka som inte är, finns i [så här skriver du giltiga samlings filter](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Nästa steg  

- [Felsöka OData Collection filter i Azure Search](search-query-troubleshoot-collection-filters.md)
- [Filter i Azure Search](search-filters.md)
- [OData uttrycks språk översikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Search](search-query-odata-syntax-reference.md)
- [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
