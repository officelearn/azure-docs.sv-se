---
title: Enkel frågesyntax
titleSuffix: Azure Cognitive Search
description: Referens för enkel frågesyntax som används för fullständiga texts öknings frågor i Azure Kognitiv sökning.
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
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152677"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Enkel frågesyntax i Azure Kognitiv sökning

Azure Kognitiv sökning implementerar två Lucene-baserade frågespråk: [enkel Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) och [Lucene Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). I Azure Kognitiv sökning utesluter den enkla frågesyntaxen de oskarpa/lutnings alternativen.  

> [!NOTE]
> Den enkla frågesyntaxen används för frågeuttryck som skickas i **Sök** -parametern i [sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) -API: t, inte att förväxlas med [OData-syntaxen](query-odata-filter-orderby-syntax.md) som används för parametern [$filter](search-filters.md) för detta API. Dessa olika syntaxer har sina egna regler för att skapa frågor, undantags strängar och så vidare.
>
> Azure Kognitiv sökning innehåller en alternativ [fullständig Lucene-frågesyntax](query-lucene-syntax.md) för mer komplexa frågor i **Sök** parametern. Mer information om hur du analyserar arkitektur och fördelar med varje syntax finns i [hur full texts ökning fungerar i Azure kognitiv sökning](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Så här anropar du enkel parsning

Enkel syntax är standardvärdet. Anrop är bara nödvändigt om du återställer syntaxen från fullständig till enkel. Om du uttryckligen vill ange syntaxen använder du Sök parametern `queryType`. Giltiga värden är `simple|full`, med `simple` som standard och `full` för Lucene. 

## <a name="query-behavior-anomalies"></a>Avvikelser i fråge beteende

All text med en eller flera villkor betraktas som en giltig start punkt för frågekörningen. Azure Kognitiv sökning matchar dokument som innehåller några eller alla villkor, inklusive eventuella variationer som påträffas under analys av texten. 

Så enkelt som det här ljudet finns det en aspekt av frågekörning i Azure Kognitiv sökning som *kan* producera oväntade resultat, vilket ökar i stället för att minska Sök resultaten när fler villkor och operatorer läggs till i Indatasträngen. Huruvida den här utökningen inträffar beror på inkludering av en NOT-Operator, kombinerat med en `searchMode` parameter inställning som avgör hur inte tolkas i termer av och eller beteenden. Med tanke på standardvärdet, `searchMode=Any`och en NOT-Operator, beräknas åtgärden som en-eller-åtgärd, så att `"New York" NOT Seattle` returnerar alla städer som inte är Seattle.  

Normalt sett är det mer troligt att du ser dessa beteenden i användar interaktions mönster för program som söker efter innehåll, där användarna är mer sannolika att inkludera en operatör i en fråga, i stället för e-handelsplatser som har fler inbyggda navigerings strukturer. Mer information finns i [not-operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Booleska operatorer (och, eller, inte) 

Du kan bädda in operatorer i en frågesträng för att skapa en omfattande uppsättning villkor mot vilka matchande dokument hittas. 

### <a name="and-operator-"></a>Operatorn och `+`

Operatorn och är ett plus tecken. `wifi+luxury` kan till exempel söka efter dokument som innehåller både `wifi` och `luxury`.

### <a name="or-operator-"></a>ELLER operatörs `|`

Operatorn OR är ett lodrätt streck eller ett vertikalstreck. `wifi | luxury` kan till exempel söka efter dokument som innehåller antingen `wifi` eller `luxury` eller både och.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operatorn NOT `-`

Operatorn NOT är ett minus tecken. `wifi –luxury` kan till exempel söka efter dokument som har `wifi`s villkoret och/eller inte har `luxury` (och/eller kontrol leras av `searchMode`).

> [!NOTE]  
>  Alternativet `searchMode` styr om en term med operatorn NOT är ANDed eller ORed med andra termer i frågan om det inte finns någon `+` eller `|`s operator. Kom ihåg att `searchMode` kan ställas in på antingen `any` (standard) eller `all`. Om du använder `any`kommer det att öka åter kallelsen av frågor genom att inkludera fler resultat, och `-` tolkas som standard som "eller inte". `wifi -luxury` kommer till exempel att matcha dokument som innehåller termen `wifi` eller de som inte innehåller termen `luxury`. Om du använder `all`kommer det att öka precisionen för frågor genom att ta med färre resultat och tolkas som standard som "och inte". `wifi -luxury` kommer till exempel att matcha dokument som innehåller termen `wifi` och som inte innehåller termen "lyxen". Detta är ett mer intuitivt beteende för `-` operatören. Därför bör du överväga att använda `searchMode=all` i stället för `searchMode=any` om du vill optimera sökningar efter precision i stället för att återkalla, *och* användarna ofta använder `-` operatören i sökningar.

## <a name="suffix-operator"></a>Suffix-operator

Suffixets operator är en asterisk `*`. `lux*` kan till exempel söka efter dokument som har en term som börjar med `lux`, vilket ignorerar Skift läge.  

## <a name="phrase-search-operator"></a>Fras Sök operator

Fras operatorn innesluter en fras inom citat tecken `" "`. Exempel: när `Roach Motel` (utan citat tecken) söker efter dokument som innehåller `Roach` och/eller `Motel` var som helst, så matchar `"Roach Motel"` (med citat tecken) bara dokument som innehåller hela frasen och i den ordningen (text analys gäller fortfarande).

## <a name="precedence-operator"></a>Prioritets operator

Prioritets operatorn omsluter strängen inom parenteser `( )`. `motel+(wifi | luxury)` kan till exempel söka efter dokument som innehåller Motel-termen och antingen `wifi` eller `luxury` (eller båda).  

## <a name="escaping-search-operators"></a>Hoppar över Sök operatorer  

 För att kunna använda ovanstående symboler som den faktiska delen av Sök texten, bör de föregås av prefixet med ett omvänt snedstreck. `luxury\+hotel` kommer till exempel att resultera i `luxury+hotel`. För att göra det enklare för vanliga fall finns det två undantag till den här regeln där inga undantag krävs:  

- Operatorn NOT `-` måste bara föregås om det är det första tecken efter blank steg, inte om det är mitt i en term. Till exempel är `wi-fi` en enda period. GUID (till exempel `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) behandlas som en enda token.
- Operatorn suffix `*` behöver bara föregås om det är det sista före och samma tecken som blank steg, inte om det är mitt i en term. `wi*fi` behandlas till exempel som en enda token.

> [!NOTE]  
>  Även om undantag bevarar token tillsammans, kan text analys dela upp dem, beroende på analys läget. Mer information finns i [språk &#40;stöd&#41; för Azure kognitiv sökning REST API](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Se även  

+ [Sök efter &#40;dokument Azure-kognitiv sökning REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [OData-uttryckssyntax](query-odata-filter-orderby-syntax.md) 
