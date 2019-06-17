---
title: Förstå-filter för insamling av OData - Azure Search
description: För att förstå hur OData samling filter fungerar i Azure Search-frågor.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079605"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Förstå OData samling filter i Azure Search

Att [filter](query-odata-filter-orderby-syntax.md) på samlingen fält i Azure Search kan du använda den [ `any` och `all` operatörer](search-query-odata-collection-operators.md) tillsammans med **lambda-uttryck**. Lambda-uttryck är booleskt uttryck som refererar till en **intervallet variabeln**. Den `any` och `all` operatorer är detsamma som en `for` loop i de flesta programmeringsspråk, med variabeln intervallet tar loop variabeln och lambda-uttryck som en del av loopen. Variabeln intervallet tar på ”aktuella” värdet för samlingen under upprepning av loopen.

AT minst som så fungerar det begreppsmässigt. I verkligheten kan Azure Search implementerar filter i ett mycket olika sätt att hur `for` loopar arbete. Vi rekommenderar detta skulle vara osynliga för dig, men i vissa situationer som det inte. Slutresultatet är att det finns regler som du måste följa när du skriver lambda-uttryck.

Den här artikeln förklarar varför det finns regler för samling filter genom att utforska hur Azure Search köra dessa filter. Om du skriver avancerade filter med komplexa lambda-uttryck kan vara i den här artikeln till hjälp att skapa din förståelse av vad som är möjligt i filter och varför.

Information om reglerna för samlingen filter är, inklusive exempel, ser [felsökning OData samling filter i Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Varför samling filter är begränsad

Det finns tre underliggande orsaker varför inte alla filter-funktioner som stöds för alla typer av samlingar:

1. Endast vissa operatörer har stöd för vissa datatyper. Exempelvis kan det inte meningsfullt att jämföra booleska värden `true` och `false` med `lt`, `gt`och så vidare.
1. Azure Search stöder inte **korrelerade search** på fält av typen `Collection(Edm.ComplexType)`.
1. Azure Search använder inverterad index för att köra filter över alla typer av data, inklusive samlingar.

Det första skälet är bara en följd av hur OData språk och EDM-typsystemet definieras. De sista två beskrivs mer ingående i resten av den här artikeln.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelerade jämfört med uncorrelated sökning

När du använder flera filtervillkoren över en samling med komplexa objekt kriterierna är **korrelerade** eftersom de gäller för *varje objekt i samlingen*. Exempelvis returnerar följande filter hotell som har minst en deluxe rummet med en hastighet som är mindre än 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Om filtrering var *uncorrelated*, ovanstående filter kan returnera hotels där ett rum är deluxe och ett annat rum har en bas Betygsätt mindre än 100. Som inte skulle vara meningsfullt, eftersom båda-satserna för lambda-uttryck som gäller för variabeln samma intervall, nämligen `room`. Det är därför sådana filter kopplas ihop.

Men för fulltextsökning går det inte att referera till ett specifikt intervall-variabeln. Om du använder fielded search för att utfärda en [fullständiga Lucene-frågan](query-lucene-syntax.md) som den här:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Du kan få hotels tillbaka där ett rum är deluxe och ett annat rum nämner ”stad view” i beskrivningen. Till exempel dokument nedan med `Id` av `1` matchar frågan:

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

Anledningen är att `Rooms/Type` avser alla analyserade villkor i den `Rooms/Type` fältet i hela dokumentet och på samma sätt för `Rooms/Description`som visas i tabellerna nedan.

Hur `Rooms/Type` har lagrats för fulltextsökning:

| Termen i `Rooms/Type` | Dokument-ID: N |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Hur `Rooms/Description` har lagrats för fulltextsökning:

| Termen i `Rooms/Description` | Dokument-ID: N |
| --- | --- |
| gård | 2 |
| city | 1 |
| trädgård | 1 |
| Stora | 1 |
| motel | 2 |
| Utrymme | 1, 2 |
| standard | 1 |
| Suite | 1 |
| vy | 1 |

Så till skillnad från filtret ovan, som i praktiken säger ”matcha dokument där ett rum har `Type` lika med 'Deluxe rummet' och **samma rummet** har `BaseRate` färre än 100”, sökfrågan säger ”matchning dokument där `Rooms/Type`har termen ”deluxe” och `Rooms/Description` har frasen ”stad view”. Det finns ingen enskilda rum vars fält kan korreleras i det senare fallet.

> [!NOTE]
> Om du skulle vilja se stöder för korrelerade sökning som lagts till i Azure Search kan du rösta på [User Voice objektet](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Vägar i inverterad index och samlingar

Du kanske har märkt att det finns färre begränsningar för lambda-uttryck över komplexa samlingar än det finns för enkla samlingar som `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`och så vidare. Det beror på att Azure Search lagrar komplexa samlingar som faktiska samlingar av underordnade dokument medan enkla samlingar inte lagras som samlingar alls.

Anta exempelvis att ett Filtrerbart strängfält samling som `seasons` i ett index för en näthandelsföretagen. Några dokument som laddas upp till det här indexet kan se ut så här:

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

Värdena för den `seasons` fältet lagras i en struktur som kallas en **inverterad index**, som ser ut ungefär så här:

| Term | Dokument-ID: N |
| --- | --- |
| Spring | 1, 2 |
| summer | 1 |
| faller | 1, 2 |
| vinter | 2, 3 |

Den här datastrukturen har utformats för att besvara en fråga i hög hastighet: I vilka dokument visas en viss period? Besvara den här frågan fungerar mer som en vanlig likhetskontroll än en slinga över en samling. Faktum är det här är anledningen till att för sträng samlingar, Azure Search tillåter endast `eq` som en jämförelseoperator inuti en lambda-uttryck för `any`.

Bygga upp från likhet, därefter ska vi titta på hur det är möjligt att kombinera flera likhet kontroller på samma intervall variabel med `or`. Det fungerar tack vare algebra och [parti-och detaljhandeln tillhör kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Det här uttrycket:

    seasons/any(s: s eq 'winter' or s eq 'fall')

motsvarar att:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

och var och en av två `any` underordnade uttryck kan köras effektivt med vägar i inverterad indexet. Dessutom tack vare [negation lagen om kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation), det här uttrycket:

    seasons/all(s: s ne 'winter' and s ne 'fall')

motsvarar att:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

vilket är därför det är möjligt att använda `all` med `ne` och `and`.

> [!NOTE]
> Även om informationen är utanför omfattningen för det här dokumentet är dessa samma principer utöka till [avståndet och skärningspunkten tester för samlingar av geospatial punkterna](search-query-odata-geo-spatial-functions.md) samt. Exemplet visar hur, `any`:
>
> - `geo.intersects` Det går inte att negeras
> - `geo.distance` måste jämföras med `lt` eller `le`
> - uttryck måste kombineras med `or`, inte `and`
>
> Omvända-reglerna för `all`.

Fler uttryck tillåts när filtrering på datasamlingar typer som har stöd för den `lt`, `gt`, `le`, och `ge` operatörer, till exempel `Collection(Edm.Int32)` till exempel. Mer specifikt kan du använda `and` samt `or` i `any`, så länge de underliggande jämförelseuttryck kombineras till **intervall jämförelser** med `and`, som sedan är ytterligare kombinerade och som använder `or`. Den här strukturen för booleska uttryck kallas [Disjunktivt Normal formuläret (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), även kallat ”ORs av ANDs”. Däremot lambda-uttryck för `all` för dessa data typerna måste vara i [Conjunctive Normal formuläret (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), även kallat ”ANDs av ORs”. Azure Search kan sådana jämförelser i intervallet eftersom den kan utföra dem med hjälp av inverterad index effektivt, precis som du kan göra snabba termen sökning efter strängar.

Sammanfattningsvis följer tumregel för vad som är tillåtet i ett lambda-uttryck:

- Inuti `any`, *positivt kontroller* alltid tillåts, t.ex. likhet, intervallet jämförelser, `geo.intersects`, eller `geo.distance` jämfört med `lt` eller `le` (se ”vilket” som t.ex. likhet när det gäller kontrollerar avståndet).
- Inuti `any`, `or` tillåts alltid. Du kan använda `and` endast för-datatyper som kan uttrycka kontroll och bara om du använder ORs av ANDs (DNF).
- Inuti `all`, reglerna återförs--endast *negativa kontroller* är tillåtna, du kan använda `and` alltid, och du kan använda `or` endast för intervallet kontrollerar uttryckt som ANDs av ORs (CNF).

I praktiken finns dessa typer av filter som du är mest sannolikt använder ändå. Det är fortfarande bra att förstå gränserna för vad du kan göra om.

Specifika exempel på vilka typer av filter tillåts och som inte finns i [hur du skriver giltig samling filter](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Nästa steg  

- [Felsökning av OData-samling filter i Azure Search](search-query-troubleshoot-collection-filters.md)
- [Filter i Azure Search](search-filters.md)
- [OData-uttrycket Språköversikt för Azure Search](query-odata-filter-orderby-syntax.md)
- [Referens för OData-uttryckssyntax för Azure Search](search-query-odata-syntax-reference.md)
- [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
