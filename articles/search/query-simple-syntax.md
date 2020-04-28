---
title: Enkel frågesyntax
titleSuffix: Azure Cognitive Search
description: Referens för enkel frågesyntax som används för fullständiga texts öknings frågor i Azure Kognitiv sökning.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: dfd75ad2c6ae246bfe6ee8b983744b3db07a841f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194949"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Enkel frågesyntax i Azure Kognitiv sökning

Azure Kognitiv sökning implementerar två Lucene-baserade frågespråk: [enkel Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) och [Lucene Query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Den enkla parsern är mer flexibel och kommer att försöka tolka en begäran även om den inte är helt sammansatt. På grund av den här flexibiliteten är det standardinställningen för frågor i Azure Kognitiv sökning. 

Den enkla syntaxen används för frågeuttryck som `search` skickas i parametern för en sökning av dokument- [begäran](https://docs.microsoft.com/rest/api/searchservice/search-documents), som inte förväxlas med OData- [syntaxen](query-odata-filter-orderby-syntax.md) som används för parametern $filter- [uttryck](search-filters.md) i samma Sök dokument-API. Parametrarna `search` och `$filter` har olika syntax, med egna regler för att skapa frågor, undantags strängar och så vidare.

Även om den enkla parsern är baserad på den enkla klassen [Apache Lucene parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , kan implementeringen i Azure kognitiv sökning utesluta suddig sökning. Om du behöver en [suddig sökning](search-query-fuzzy.md) eller andra avancerade fråge formulär bör du ta hänsyn till den alternativa [fullständiga Lucene](query-lucene-syntax.md) -frågesyntaxen i stället.

## <a name="invoke-simple-parsing"></a>Anropa enkel parsning

Enkel syntax är standardvärdet. Anrop är bara nödvändigt om du återställer syntaxen från fullständig till enkel. Använd `queryType` Sök parametern för att uttryckligen ange syntaxen. Giltiga värden är `queryType=simple` eller `queryType=full`, där `simple` är standard, och `full` anropar [fullständig Lucene-Frågedesigner](query-lucene-syntax.md) för mer avancerade frågor. 

## <a name="syntax-fundamentals"></a>Grundläggande syntax

All text med en eller flera villkor betraktas som en giltig start punkt för frågekörningen. Azure Kognitiv sökning matchar dokument som innehåller några eller alla villkor, inklusive eventuella variationer som påträffas under analys av texten.

Så enkelt som det här ljudet finns det en aspekt av frågekörning i Azure Kognitiv sökning som *kan* producera oväntade resultat, vilket ökar i stället för att minska Sök resultaten när fler villkor och operatorer läggs till i Indatasträngen. Huruvida den här utökningen inträffar beror på inkludering av en NOT-Operator, kombinerat med en **searchMode** parameter inställning som avgör hur inte tolkas i termer av och eller beteende. Mer information finns i [not-operator](#not-operator).

### <a name="precedence-operators-grouping"></a>Prioritets operatorer (gruppering)

Du kan använda parenteser för att skapa under frågor, inklusive operatorer inom den parentetiska instruktionen. Söker till exempel `motel+(wifi|luxury)` efter dokument som innehåller termen "Motel" och antingen "WiFi" eller "lyxen" (eller båda).

Fält grupperingen liknar varandra men omfångerar grupperingen till ett enda fält. `hotelAmenities:(gym+(wifi|pool))` Söker till exempel fältet "hotelAmenities" för "gymmet" och "WiFi", eller "gymmet" och "pool".  

### <a name="escaping-search-operators"></a>Hoppar över Sök operatorer  

I den enkla syntaxen innehåller Sök operatorer följande tecken:`+ | " ( ) ' \`  

Om något av dessa tecken ingår i en token i indexet kan du kringgå det genom att prefixet med ett enda omvänt snedstreck (`\`) i frågan. Anta till exempel att du har använt en anpassad analys för hela termen tokenisering och att indexet innehåller strängen "lyxen + hotell". Om du vill få en exakt matchning för denna token infogar du ett `search=luxury\+hotel`escape-tecken:. 

För att göra det enklare för vanliga fall finns det två undantag till den här regeln där inga undantag krävs:  

+ Operatorn `-` not behöver bara föregås om det är det första tecken efter ett blank steg. Om `-` visas i mitten (t. ex. i `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) kan du hoppa över undantag.

+ Suffixets operator `*` måste bara föregås om det är det sista före ett blank steg. Om `*` visas i mitten (t. ex. i `4*4=16`) behövs inga undantags tecken.

> [!NOTE]  
> Som standard tar standard Analyzer bort och avbryter ord för bindestreck, blank steg, et-tecken och andra tecken under [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis). Om du behöver specialtecken som ska finnas kvar i frågesträngen kan du behöva en analys som bevarar dem i indexet. Vissa alternativ är Microsofts [språk analys](index-add-language-analyzers.md)verktyg, som bevarar avstavade ord eller en anpassad analys för mer komplexa mönster. Mer information finns i [del termer, mönster och specialtecken](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Koda osäkra och reserverade tecken i URL: er

Kontrol lera att alla osäkra och reserverade tecken är kodade i en URL. Till exempel är ' # ' ett osäkert värde eftersom det är ett fragment/ankare-ID i en URL. Specialtecknet måste vara kodad till `%23` om den används i en URL. "&" och "=" är exempel på reserverade tecken när de begränsar parametrar och anger värden i Azure Kognitiv sökning. Mer information finns i [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

Osäkra tecken är ``" ` < > # % { } | \ ^ ~ [ ]``. Reserverade tecken är `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Storleks gränser för fråga

 Det finns en gräns för hur många frågor du kan skicka till Azure Kognitiv sökning. Mer specifikt kan du ha högst 1024-satser (uttryck avgränsade med och, eller, och så vidare). Det finns också en gräns på ungefär 32 KB på storleken på en enskild term i en fråga. Om programmet genererar Sök frågor program mässigt rekommenderar vi att du utformar det på ett sådant sätt att det inte genererar frågor om obegränsad storlek.  

## <a name="boolean-search"></a>Boolesk sökning

Du kan bädda in booleska operatorer (och, eller, inte) i en frågesträng för att skapa en omfattande uppsättning villkor mot vilka matchande dokument hittas. 

### <a name="and-operator-"></a>AND-operator`+`

Operatorn och är ett plus tecken. Söker till exempel `wifi + luxury` efter dokument som innehåller både `wifi` och. `luxury`

### <a name="or-operator-"></a>OR-operator`|`

Operatorn OR är ett lodrätt streck eller ett vertikalstreck. Söker till exempel `wifi | luxury` efter dokument som innehåller antingen `wifi` eller `luxury` eller båda.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT-operator`-`

Operatorn NOT är ett minus tecken. Söker till exempel `wifi –luxury` efter dokument som har `wifi` termen och/eller inte har. `luxury`

Parametern **searchMode** i en förfrågan styr om en term med operatorn inte är ANDed eller Ored med andra villkor i frågan (förutsatt att det inte finns någon `+` eller `|` operator på de andra villkoren). Giltiga värden är `any` eller `all`.

`searchMode=any`ökar åter kallelsen av frågor genom att inkludera fler resultat och tolkas som standard `-` som "eller inte". `wifi -luxury` Kommer till exempel att matcha dokument som innehåller den `wifi` eller de som inte innehåller någon term `luxury`.

`searchMode=all`ökar precisionen för frågor genom att inkludera färre resultat och som standard tolkas som "och inte". `wifi -luxury` Kommer till exempel att matcha dokument som innehåller termen `wifi` och som inte innehåller termen "lyxen". Det här är utan tvekan ett mer intuitivt beteende `-` för operatorn. Därför bör du överväga att använda `searchMode=all` i stället `searchMode=any` för om du vill optimera sökningarna efter precision i stället för att återkalla, *och* användarna använder `-` ofta operatorn i sökningar.

När du bestämmer dig för en **searchMode** -inställning bör du tänka på användar interaktions mönstren för frågor i olika program. Användare som söker efter information är mer sannolika att inkludera en operatör i en fråga, i stället för e-handelsplatser som har fler inbyggda navigerings strukturer.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Prefixs ökning

Suffixets operator är en asterisk `*`. Hittar till exempel `lingui*` "språklig" eller "Linguini", vilket ignorerar Skift läge. 

I likhet med filter söker en prefix-fråga efter en exakt matchning. Det finns därför ingen relevans Poäng (alla resultat får ett Sök resultat på 1,0). Prefix frågor kan vara långsamma, särskilt om indexet är stort och prefixet består av ett litet antal tecken. 

Om du vill köra en fråga för suffix, som matchar den sista delen av sträng, använder du en [sökning med jokertecken](query-lucene-syntax.md#bkmk_wildcard) och fullständig Lucene-syntax.

## <a name="phrase-search-"></a>Fras sökning`"`

En terms ökning är en fråga för en eller flera villkor, där någon av villkoren betraktas som en matchning. En fras sökning är en exakt fras som omges av citat tecken `" "`. Till exempel, medan `Roach Motel` (utan citat tecken) söker efter dokument som `Roach` innehåller och/ `Motel` eller var som helst i `"Roach Motel"` vilken ordning som helst (med citat tecken) kommer bara att matcha dokument som innehåller hela frasen och i den ordningen (text analys gäller fortfarande).

## <a name="see-also"></a>Se även  

+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Fråga exempel för enkel sökning](search-query-simple-examples.md)
+ [Fråga exempel för fullständig Lucene-sökning](search-query-lucene-examples.md)
+ [REST API för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [OData-uttryckssyntax](query-odata-filter-orderby-syntax.md) 
