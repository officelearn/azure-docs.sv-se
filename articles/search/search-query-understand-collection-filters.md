---
title: Förstå OData-samlingsfilter
titleSuffix: Azure Cognitive Search
description: Lär dig mekaniken i hur OData-samlingsfilter fungerar i Azure Cognitive Search-frågor, inklusive begränsningar och beteenden som är unika för samlingar.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113064"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Förstå OData-samlingsfilter i Azure Cognitive Search

Om du vill [filtrera på](query-odata-filter-orderby-syntax.md) samlingsfält i Azure Cognitive Search kan du använda [ `any` operatorerna och `all` ](search-query-odata-collection-operators.md) tillsammans med **lambda-uttryck**. Lambda-uttryck är booleska uttryck som refererar till en **intervallvariabel**. `any` Operatorerna `all` och är analoga `for` med en slinga i de flesta programmeringsspråk, med intervallvariabeln som tar rollen som loopvariabel och lambda-uttrycket som slingans kropp. Intervallvariabeln tar på "nuvarande" värdet för samlingen under iteration av slingan.

Åtminstone det är så det fungerar konceptuellt. I verkligheten implementerar Azure Cognitive Search filter på `for` ett helt annat sätt än loopar fungerar. Helst skulle denna skillnad vara osynlig för dig, men i vissa situationer är det inte. Slutresultatet är att det finns regler du måste följa när du skriver lambda uttryck.

I den här artikeln förklarar vi varför reglerna för samlingsfilter finns genom att utforska hur Azure Cognitive Search kör dessa filter. Om du skriver avancerade filter med komplexa lambda uttryck, kan du hitta den här artikeln till hjälp för att bygga din förståelse för vad som är möjligt i filter och varför.

Information om vilka regler för samlingsfilter som är, inklusive exempel, finns [i Felsöka OData-samlingsfilter i Azure Cognitive Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Varför insamlingsfilter är begränsade

Det finns tre underliggande orsaker till att inte alla filterfunktioner stöds för alla typer av samlingar:

1. Endast vissa operatörer stöds för vissa datatyper. Det är till exempel inte meningsfullt att jämföra `true` `false` booleska värden och använda `lt`, `gt`och så vidare.
1. Azure Cognitive Search stöder inte **korrelerad** `Collection(Edm.ComplexType)`sökning i fält av typen .
1. Azure Cognitive Search använder inverterade index för att köra filter över alla typer av data, inklusive samlingar.

Den första orsaken är bara en följd av hur OData-språket och EDM-typsystemet definieras. De två sista förklaras mer i detalj i resten av den här artikeln.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelerad kontra okorrelerad sökning

När du använder flera filtervillkor över en samling komplexa objekt är villkoren **korrelerade** eftersom de gäller för *varje objekt i samlingen*. Följande filter returnerar till exempel hotell som har minst ett deluxerum med ett pris som är lägre än 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Om filtrreringen inte var *okorrelerad*kan ovanstående filter returnera hotell där ett rum är deluxe och ett annat rum har ett baspris som är mindre än 100. Det skulle inte vara meningsfullt, eftersom båda klausulerna i lambda uttryck `room`gäller för samma intervall variabel, nämligen . Det är därför sådana filter är korrelerade.

För fulltextsökning finns det dock inget sätt att referera till en specifik intervallvariabel. Om du använder fältsökning för att utfärda en [fullständig Lucene-fråga](query-lucene-syntax.md) som den här:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Du kan få hotell tillbaka där ett rum är deluxe, och ett annat rum nämner "utsikt över staden" i beskrivningen. Dokumentet nedan med `Id` av `1` matchar till exempel frågan:

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

Anledningen är `Rooms/Type` att refererar till alla `Rooms/Type` analyserade termer i fältet i `Rooms/Description`hela dokumentet, och på samma sätt för , som visas i tabellerna nedan.

Så `Rooms/Type` här lagras du för fulltextsökning:

| Termen i`Rooms/Type` | Dokument-ID:er |
| --- | --- |
| Deluxe | 1, 2 |
| standard | 1 |

Så `Rooms/Description` här lagras du för fulltextsökning:

| Termen i`Rooms/Description` | Dokument-ID:er |
| --- | --- |
| Courtyard | 2 |
| city | 1 |
| Trädgård | 1 |
| Stora | 1 |
| Motel | 2 |
| rum | 1, 2 |
| standard | 1 |
| Suite | 1 |
| visa | 1 |

Så till skillnad från filtret ovan, som i `Type` princip säger "matcha dokument där `BaseRate` ett rum har lika med "Deluxe Room" `Rooms/Type` och **samma rum** `Rooms/Description` har mindre än 100", säger sökfrågan "matcha dokument där har termen "deluxe" och har frasen "stadsvy". Det finns inget begrepp om enskilda rum vars fält kan korreleras i det senare fallet.

> [!NOTE]
> Om du vill se stöd för korrelerad sökning som lagts till i Azure Cognitive Search, rösta på [det här user voice-objektet](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Inverterade index och samlingar

Du kanske har märkt att det finns mycket färre restriktioner för lambda `Collection(Edm.Int32)`uttryck `Collection(Edm.GeographyPoint)`över komplexa samlingar än det finns för enkla samlingar som , , och så vidare. Detta beror på att Azure Cognitive Search lagrar komplexa samlingar som faktiska samlingar av underdokument, medan enkla samlingar inte lagras som samlingar alls.

Tänk dig till exempel ett filterbart strängsamlingsfält som `seasons` i ett index för en onlineåterförsäljare. Vissa dokument som överförs till det här indexet kan se ut så här:

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

Värdena för `seasons` fältet lagras i en struktur som kallas ett **inverterat index**, vilket ser ut ungefär så här:

| Period | Dokument-ID:er |
| --- | --- |
| Våren | 1, 2 |
| Sommaren | 1 |
| Falla | 1, 2 |
| Vintern | 2, 3 |

Denna datastruktur är utformad för att svara på en fråga med stor hastighet: I vilka dokument visas en viss term? Att svara på den här frågan fungerar mer som en vanlig jämställdhetskontroll än en slinga över en samling. I själva verket är det därför för strängsamlingar, Azure Cognitive Search `eq` `any`tillåter bara som en jämförelse operatör inuti en lambda uttryck för .

Bygga upp från jämställdhet, nästa ska vi titta på hur det är möjligt `or`att kombinera flera jämställdhet kontroller på samma intervall variabel med . Det fungerar tack vare algebra och [distributiongenskap kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Detta uttryck:

    seasons/any(s: s eq 'winter' or s eq 'fall')

motsvarar:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

och vart och `any` ett av de två undertrycken kan utföras effektivt med det inverterade indexet. Också, tack vare [negation lagen av kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation), detta uttryck:

    seasons/all(s: s ne 'winter' and s ne 'fall')

motsvarar:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

det är därför det är `all` `ne` möjligt `and`att använda med och .

> [!NOTE]
> Även om detaljerna ligger utanför detta dokuments räckvidd sträcker sig samma principer till [avstånds- och skärningstester även för samlingar av geospatialska punkter.](search-query-odata-geo-spatial-functions.md) Det är därför, i: `any`
>
> - `geo.intersects`kan inte förnekas
> - `geo.distance`måste jämföras `lt` med eller`le`
> - uttryck måste kombineras `or`med , inte`and`
>
> De omvända `all`reglerna gäller för .

Ett bredare utbud av uttryck tillåts vid filtrering `lt`på `gt` `le`samlingar `ge` av datatyper `Collection(Edm.Int32)` som stöder operatorerna , , och, till exempel. Specifikt kan du `and` använda såväl `or` `any`som i , så länge de underliggande jämförelseuttrycken kombineras till `or` **intervalljämnlar** med `and`, som sedan kombineras ytterligare med . Denna struktur av booleska uttryck kallas [Disjunctive Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), annars känd som "ORs of ANDs". Däremot måste lambda-uttryck `all` för dessa datatyper vara i [konjunktiv normalform (CNF),](https://en.wikipedia.org/wiki/Conjunctive_normal_form)även kallade "ANDs of ORs". Azure Cognitive Search tillåter sådana intervalljämlikhet eftersom den kan köra dem med hjälp av inverterade index effektivt, precis som det kan göra snabb termsökning för strängar.

Sammanfattningsvis, här är tumreglerna för vad som är tillåtet i en lambda uttryck:

- Inuti `any`är *positiva kontroller* alltid tillåtna, som `geo.intersects`jämlikhet, intervalljämförelser, eller `geo.distance` jämförs med `lt` eller `le` (tänk på "närhet" som att vara som jämställdhet när det gäller att kontrollera avstånd).
- Inuti `any` `or` , är alltid tillåtet. Du kan `and` bara använda för datatyper som kan uttrycka intervallkontroller, och endast om du använder ORs av ANDs (DNF).
- Inuti `all`är reglerna omvända – endast *negativa kontroller* tillåts, du kan alltid använda `and` och du kan bara använda `or` för intervallkontroller uttryckta som ANDs of ORs (CNF).

I praktiken är det de typer av filter som du mest sannolikt kommer att använda ändå. Det är fortfarande bra att förstå gränserna för vad som är möjligt ändå.

Specifika exempel på vilka typer av filter som är tillåtna och vilka som inte är det finns i [Så här skriver du giltiga samlingsfilter](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Nästa steg  

- [Felsöka OData-samlingsfilter i Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)
- [Filter i Azure Cognitive Search](search-filters.md)
- [Språköversikt för OData-uttryck för Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxreferens för OData-uttryck för Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
