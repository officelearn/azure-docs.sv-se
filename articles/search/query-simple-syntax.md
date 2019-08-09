---
title: Enkel frågesyntax – Azure Search
description: Referens för enkel frågesyntax som används för fullständiga texts öknings frågor i Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
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
ms.openlocfilehash: 41a9c87731dcb6a2cb31e9120a0170b892c58b6f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884096"
---
# <a name="simple-query-syntax-in-azure-search"></a>Enkel frågesyntax i Azure Search
Azure Search implementerar två Lucene-baserade frågespråk: [Simple Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) och [Lucene Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). I Azure Search utesluter den enkla frågesyntaxen de oskarpa/lutnings alternativen.  

> [!NOTE]  
>  Azure Search innehåller en alternativ [Lucene-frågesyntax](query-lucene-syntax.md) för mer komplexa frågor. Mer information om hur du analyserar arkitektur och fördelar med varje syntax finns i [hur full texts ökning fungerar i Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Så här anropar du enkel parsning

Enkel syntax är standardvärdet. Anrop är bara nödvändigt om du återställer syntaxen från fullständig till enkel. Använd `queryType` Sök parametern för att uttryckligen ange syntaxen. Giltiga värden är `simple|full`, med `simple` som standard, och `full` för Lucene. 

## <a name="query-behavior-anomalies"></a>Avvikelser i fråge beteende

All text med en eller flera villkor betraktas som en giltig start punkt för frågekörningen. Azure Search kommer att matcha dokument som innehåller några eller alla villkor, inklusive eventuella variationer som påträffas under analys av texten. 

Så enkelt som det här ljudet finns det en aspekt av frågekörning i Azure Search som *kan* ge oväntade resultat, vilket ökar i stället för att minska Sök resultaten när fler villkor och operatorer läggs till i Indatasträngen. Huruvida den här utökningen inträffar beror på inkludering av en not-Operator, kombinerat `searchMode` med en parameter inställning som avgör hur inte tolkas i termer av och eller eller beteenden. Med tanke på standardvärdet, `searchMode=Any`och en not-Operator, beräknas åtgärden som en-eller-åtgärd, som `"New York" NOT Seattle` returnerar alla städer som inte är Seattle.  

Normalt sett är det mer troligt att du ser dessa beteenden i användar interaktions mönster för program som söker efter innehåll, där användarna är mer sannolika att inkludera en operatör i en fråga, i stället för e-handelsplatser som har fler inbyggda navigerings strukturer. Mer information finns i [not-operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Booleska operatorer (och, eller, inte) 

Du kan bädda in operatorer i en frågesträng för att skapa en omfattande uppsättning villkor mot vilka matchande dokument hittas. 

### <a name="and-operator-"></a>AND-operator`+`

Operatorn och är ett plus tecken. Söker till exempel `wifi+luxury` efter dokument som innehåller både `wifi` och `luxury`.

### <a name="or-operator-"></a>OR-operator`|`

Operatorn OR är ett lodrätt streck eller ett vertikalstreck. Söker till exempel `wifi | luxury` efter dokument som innehåller antingen `wifi` eller `luxury` eller båda.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT-operator`-`

Operatorn NOT är ett minus tecken. Söker till exempel `wifi –luxury` efter dokument som `wifi` har termen och/eller inte har `luxury` (och/eller som styrs av `searchMode`).

> [!NOTE]  
>  Alternativet styr om en term med operatorn not ANDed eller Ored med andra termer i frågan i avsaknad av en `+` or `|` -operator. `searchMode` Återkallande som `searchMode` kan ställas in på `any` antingen (standard) `all`eller. Om du använder `any`kommer det att öka åter kallelsen av frågor genom att inkludera fler resultat och tolkas som standard `-` som "eller inte". `wifi -luxury` Kommer till exempel att matcha dokument som innehåller `wifi` den eller de som inte innehåller någon term `luxury`. Om du använder `all`kommer det att öka precisionen för frågor genom att ta med färre resultat och som standard tolkas det som "och inte". `wifi -luxury` Kommer till exempel att matcha dokument som innehåller termen `wifi` och som inte innehåller termen "lyxen". Det här är utan tvekan ett mer intuitivt beteende `-` för operatorn. Därför bör du överväga att använda `searchMode=all` `searchMode=any` i stället för om du vill optimera sökningarna efter precision i stället för att återkalla, `-` och användarna använder ofta operatorn i sökningar.

## <a name="suffix-operator"></a>Suffix-operator

Suffixets operator är en asterisk `*`. Söker till exempel `lux*` efter dokument som har en term som börjar med `lux`och som ignorerar Skift läge.  

## <a name="phrase-search-operator"></a>Fras Sök operator

Fras operatorn innesluter en fras inom citat tecken `" "`. Till exempel, medan `Roach Motel` (utan citat tecken) söker efter dokument som `Roach` innehåller och/ `Motel` eller var som helst i `"Roach Motel"` vilken ordning som helst, (med citat tecken) kommer bara att matcha dokument som innehåller hela frasen tillsammans och i det order (text analys gäller fortfarande).

## <a name="precedence-operator"></a>Prioritets operator

Prioritets operatorn innesluter strängen inom parentes `( )`. Kan till exempel `motel+(wifi | luxury)` söka efter dokument som innehåller Motel-termen och antingen `wifi` eller `luxury` (eller båda).  

## <a name="escaping-search-operators"></a>Hoppar över Sök operatorer  

 För att kunna använda ovanstående symboler som den faktiska delen av Sök texten, bör de föregås av prefixet med ett omvänt snedstreck. Till exempel `luxury\+hotel` kommer att resultera i termen `luxury+hotel`. För att göra det enklare för vanliga fall finns det två undantag till den här regeln där inga undantag krävs:  

- Operatorn `-` not behöver bara vara undantagen om det är det första tecken efter blank steg, inte om det är mitt i en term. Till exempel `wi-fi` är en enda period, medan GUID ( `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`till exempel) behandlas som en enda token.
- Suffixets operator `*` måste bara föregås om det är det sista innan blank steg används, inte om det är mitt i en term. Behandlas till exempel `wi*fi` som en enskild token.

> [!NOTE]  
>  Även om undantag bevarar token tillsammans, kan text analys dela upp dem, beroende på analys läget. Mer information finns i avsnittet [ &#40;språk&#41; support Azure Search Service REST API](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Se också  

+ [Sök efter &#40;dokument Azure Search tjänst REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [OData-uttryckssyntax](query-odata-filter-orderby-syntax.md) 
