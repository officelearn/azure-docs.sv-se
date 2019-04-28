---
title: Enkel frågesyntax – Azure Search
description: Referens för den enkla frågesyntaxen som används för fulltextsökningsfrågor i Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
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
ms.openlocfilehash: 99729141e5e1478f45ad385cf671c44a8e08f21a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61316905"
---
# <a name="simple-query-syntax-in-azure-search"></a>Enkel frågesyntax i Azure Search
Azure Search implementerar två Lucene-baserat frågespråk: [Enklare Frågeparsern](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) och [Lucene Frågeparsern](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). I Azure Search utesluter den enkla frågesyntaxen fuzzy/uppsamlingstankar alternativ.  

> [!NOTE]  
>  Azure Search har ett alternativ [Lucene-frågesyntax](query-lucene-syntax.md) för mer komplexa frågor. Läs mer om frågan parsning av arkitekturen och fördelarna med varje syntax i [hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Hur du anropar enkel parsning

Enkel syntax är standardinställningen. Anrop krävs bara om du återställer syntax från fullständig till enkel. Uttryckligen ange syntaxen och den `queryType` sökparametern. Giltiga värden är `simple|full`, med `simple` som standard, och `full` för Lucene. 

## <a name="query-behavior-anomalies"></a>Fråga beteende avvikelser

Text med en eller flera termer anses vara en giltig startpunkt för frågekörning. Azure Search matchar dokument som innehåller en eller alla villkor, inklusive några ändringar hittades under analys av texten. 

Intuitivt det här låter säkert, det är en del av körningen av frågan i Azure Search som *kan* producerar oväntade resultat, ökar i stället för att minska sökning resulterar som flera villkor och operatörer läggs till indata sträng. Om detta verkligen sker beror på inkludering av operatorn en inte kombineras med en `searchMode` parameterinställningen som avgör hur inte tolkas som och eller eller beteenden. Får standardvärdet, `searchMode=Any`, och åtgärden för operatorn inte beräknas som en OR-åtgärd, så att `"New York" NOT Seattle` returnerar alla städer som inte är Seattle.  

Du är vanligtvis mycket mer troligt att dessa beteenden i interaktion användarmönster för program som söker igenom innehållet, där användarna som är mer troligt att inkludera en operatör i en fråga, till skillnad från e-handelswebbplatser som har fler inbyggda navigeringsstrukturer. Mer information finns i [NOT-operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Booleska operatorer (AND, OR, inte) 

Du kan bädda in operatörer i en frågesträng för att skapa en omfattande uppsättning villkor mot vilken matchande dokument påträffas. 

### <a name="and-operator-"></a>OCH operatör `+`

Operatorn och är ett plustecken. Till exempel `wifi+luxury` söker efter dokument som innehåller både `wifi` och `luxury`.

### <a name="or-operator-"></a>ELLER har frågor `|`

Operatorn eller är en lodrät stapel- eller vertikalstrecket. Till exempel `wifi | luxury` söker efter dokument som innehåller antingen `wifi` eller `luxury` eller båda.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT-operator `-`

Operatorn inte är ett minustecken. Till exempel `wifi –luxury` söker efter dokument som innehåller den `wifi` term och/eller har inte `luxury` (och/eller styrs av `searchMode`).

> [!NOTE]  
>  Den `searchMode` alternativet kontroller om en term med operatorn inte är and eller ORed med andra villkor i fråga om en `+` eller `|` operator. Kom ihåg att `searchMode` kan vara inställd på antingen `any` (standard) eller `all`. Om du använder `any`, ökas återkallande av frågor genom att inkludera fler resultat och som standard `-` tolkas som ”eller inte”. Till exempel `wifi -luxury` ska matcha dokument som antingen innehåller termen `wifi` eller som inte innehåller termen `luxury`. Om du använder `all`, den ökar precisionen för frågor genom att inkludera färre resultat och som standard - tolkas som ”och inte”. Till exempel `wifi -luxury` kommer att matcha dokument som innehåller termen `wifi` och inte innehåller termen ”Lyxig”. Det är utan tvekan en mer intuitiv användning som gäller för den `-` operator. Därför bör du använda `searchMode=all` i stället för `searchMode=any` om du vill optimera söker efter precision i stället för återkallar, *och* som ofta används för den `-` operator i sökningar.

## <a name="suffix-operator"></a>Suffixet operator

Operatorn suffixet är en asterisk `*`. Till exempel `lux*` söker efter dokument som har en term som börjar med `lux`, Ignorera skiftläge.  

## <a name="phrase-search-operator"></a>Fras sökoperatorn

Operatorn frasen placerar en fras i citattecken `" "`. Till exempel när `Roach Motel` (utan citattecken) söker efter dokument som innehåller `Roach` och/eller `Motel` var som helst i valfri ordning, `"Roach Motel"` (med citattecken) kommer bara att matcha dokument som innehåller den hel frasen tillsammans och som ordning (textanalys gäller fortfarande).

## <a name="precedence-operator"></a>Prioritet operator

Operatorn prioritet omsluter strängen inom parentes `( )`. Till exempel `motel+(wifi | luxury)` söker efter dokument som innehåller termen motel och antingen `wifi` eller `luxury` (eller båda).  

## <a name="escaping-search-operators"></a>Undantagstecken sökoperatorer  

 För att kunna använda ovan symboler som faktiskt en del av söktexten, bör de undantas genom dem med ett omvänt snedstreck. Till exempel `luxury\+hotel` leder termen `luxury+hotel`. För att kunna göra det enkelt för de vanliga fall, finns det två undantag till den här regeln där undantagstecken inte behövs:  

- Operatorn inte `-` behöver bara understrykningen om det är det första tecknet efter blanksteg, inte om det är mitt i en term. Till exempel `wi-fi` är en enskild term; medan GUID (till exempel `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) behandlas som en enskild token.
- Operatorn suffix `*` måste undantas endast om det är det sista tecknet innan blanksteg, inte om det är mitt i en term. Till exempel `wi*fi` behandlas som en enskild token.

> [!NOTE]  
>  Även om undantagstecken är fortfarande token tillsammans, textanalys kan delas upp dem, beroende på analysis-läge. Se [språkstöd &#40;Azure Search Service REST API&#41; ](index-add-language-analyzers.md) information.  

## <a name="see-also"></a>Se också  

+ [Söka efter dokument &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [OData-uttryckssyntax](query-odata-filter-orderby-syntax.md) 
