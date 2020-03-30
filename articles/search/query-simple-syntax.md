---
title: Enkel frågesyntax
titleSuffix: Azure Cognitive Search
description: Referens för den enkla frågesyntax som används för fulltextsökningsfrågor i Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152677"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Enkel frågesyntax i Azure Cognitive Search

Azure Cognitive Search implementerar två Lucene-baserade frågespråk: [Enkel frågetolkare](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) och [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). I Azure Cognitive Search utesluter den enkla frågesyntaxen alternativ för fuzzy/slop.  

> [!NOTE]
> Den enkla frågesyntaxen används för **search** frågeuttryck som skickas i sökparametern för [API:et för sökdokument,](https://docs.microsoft.com/rest/api/searchservice/search-documents) som inte ska förväxlas med [syntaxen OData](query-odata-filter-orderby-syntax.md) som används för [parametern $filter](search-filters.md) för det API:et. Dessa olika syntaxer har sina egna regler för att konstruera frågor, fly strängar och så vidare.
>
> Azure Cognitive Search ger en alternativ [fullständig Lucene-frågesyntax](query-lucene-syntax.md) för mer komplexa frågor i sökparametern. **search** Mer information om frågetolkningsarkitektur och fördelar med varje syntax finns [i Hur fulltextsökning fungerar i Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Så här anropar du enkel tolkning

Enkel syntax är standard. Åkarop är bara nödvändigt om du återställer syntaxen från fullständig till enkel. Om du uttryckligen vill `queryType` ange syntaxen använder du sökparametern. Giltiga värden `simple|full`inkluderar `simple` , med `full` som standard och för Lucene. 

## <a name="query-behavior-anomalies"></a>Frågebeteendeavvikelser

All text med ett eller flera termer betraktas som en giltig startpunkt för körning av frågor. Azure Cognitive Search matchar dokument som innehåller något eller alla termer, inklusive eventuella variationer som hittas under analysen av texten. 

Så enkelt som det här låter finns det en aspekt av frågekörning i Azure Cognitive Search som *kan* ge oväntade resultat, öka i stället för att minska sökresultaten när fler termer och operatorer läggs till i indatasträngen. Om den här expansionen verkligen inträffar beror på `searchMode` att en NOT-operator inkluderas, i kombination med en parameterinställning som avgör hur NOT tolkas i termer av OCH eller eller beteenden. Med tanke `searchMode=Any`på standard-, och NOT-operatorn beräknas åtgärden som `"New York" NOT Seattle` en ELLER-åtgärd, så att returnerar alla städer som inte är Seattle.  

Vanligtvis är det mer troligt att du ser dessa beteenden i användarinteraktionsmönster för program som söker över innehåll, där användarna är mer benägna att inkludera en operatör i en fråga, i motsats till e-handelswebbplatser som har mer inbyggda navigeringsstrukturer. Mer information finns i [OPERATORN INTE](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Booleska operatorer (OCH, ELLER, INTE) 

Du kan bädda in operatorer i en frågesträng för att skapa en omfattande uppsättning villkor mot vilka matchande dokument hittas. 

### <a name="and-operator-"></a>OCH operatör`+`

OPERATORn AND är ett plustecken. Du `wifi+luxury` söker till exempel efter `wifi` `luxury`dokument som innehåller både och .

### <a name="or-operator-"></a>ELLER-operatör`|`

OPERATORn ELLER är ett lodrät fält eller pipe-tecken. Du `wifi | luxury` söker till exempel efter `wifi` `luxury` dokument som innehåller antingen eller båda.

<a name="not-operator"></a>

### <a name="not-operator--"></a>INTE-operator`-`

OPERATORN NOT är ett minustecken. Till exempel `wifi –luxury` kommer att söka `wifi` efter dokument som har `luxury` termen och /eller `searchMode`inte har (och / eller styrs av ).

> [!NOTE]  
>  Alternativet styr om en term med operatorn INTE är ANDed eller ORed med `+` `|` de andra termerna i frågan i avsaknad av en eller operatör. `searchMode` Återkalla `searchMode` som kan ställas `any` in på `all`antingen (standard) eller . Om du `any`använder , kommer det att öka återkallande av `-` frågor genom att inkludera fler resultat, och som standard kommer att tolkas som "ELLER inte". Till exempel `wifi -luxury` matchar dokument som antingen `wifi` innehåller termen eller de `luxury`som inte innehåller termen . Om du `all`använder , kommer det att öka precisionen i frågor genom att inkludera färre resultat, och som standard - kommer att tolkas som "OCH INTE". Till exempel `wifi -luxury` matchar dokument som `wifi` innehåller termen och inte innehåller termen "lyx". Detta är utan tvekan ett `-` mer intuitivt beteende för operatören. Därför bör du `searchMode=all` överväga `searchMode=any` att använda i stället för om du vill optimera sökningar `-` efter precision i stället för återkallande, *och* användarna använder ofta operatören i sökningar.

## <a name="suffix-operator"></a>Suffixoperator

Suffixoperatorn är en `*`asterisk . Till exempel `lux*` kommer att söka efter dokument `lux`som har en term som börjar med , ignorera fall.  

## <a name="phrase-search-operator"></a>Operator för sök av fraser

Frasoperatorn omger en fras `" "`med citattecken . Till exempel, `Roach Motel` medan (utan citattecken) `Roach` skulle söka `Motel` efter dokument `"Roach Motel"` som innehåller och / eller var som helst i valfri ordning, (med citattecken) kommer bara att matcha dokument som innehåller hela frasen tillsammans och i den ordningen (textanalys fortfarande gäller).

## <a name="precedence-operator"></a>Prioritetsoperator

Prioritetsoperatorn omger strängen inom `( )`parentes . Till exempel `motel+(wifi | luxury)` kommer att söka efter dokument `wifi` som `luxury` innehåller motell sikt och antingen eller (eller båda).  

## <a name="escaping-search-operators"></a>Flyr sökoperatorer  

 För att använda ovanstående symboler som en verklig del av söktexten, bör de fly genom att prefix dem med ett omvänt snedstreck. Till exempel `luxury\+hotel` kommer att `luxury+hotel`resultera i termen . För att göra det enkelt för de mer typiska fallen finns det två undantag från denna regel där det inte behövs att fly:  

- INTE-operatorn `-` behöver bara fly om det är det första tecknet efter blanksteg, inte om det är mitt i en term. Till exempel `wi-fi` är en enda term; medan GUIDs `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`(t.ex. ) behandlas som en enda token.
- Suffixoperatorn `*` behöver bara fly om det är det sista tecknet före blanksteg, inte om det är mitt i en term. Behandlas till `wi*fi` exempel som en enda token.

> [!NOTE]  
>  Även om du flyr håller ihop token, kan textanalys dela upp dem, beroende på analysläget. Mer [information&#41;finns i Språkstöd &#40;Azure Cognitive Search REST API.](index-add-language-analyzers.md)  

## <a name="see-also"></a>Se även  

+ [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [OData-uttryckssyntax](query-odata-filter-orderby-syntax.md) 
