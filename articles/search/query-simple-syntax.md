---
title: Enkel frågesyntax
titleSuffix: Azure Cognitive Search
description: Referens för den enkla frågesyntax som används för fulltextsökningsfrågor i Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258872"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Enkel frågesyntax i Azure Cognitive Search

Azure Cognitive Search implementerar två Lucene-baserade frågespråk: [Enkel frågetolkare](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) och [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). 

I Azure Cognitive Search utesluter den enkla frågesyntaxen suddiga sökåtgärder. Använd i stället den fullständiga Lucene-syntaxen för [fuzzy search](search-query-fuzzy.md).

> [!NOTE]
> Den enkla frågesyntaxen används för **search** frågeuttryck som skickas i sökparametern för [API:et för sökdokument,](https://docs.microsoft.com/rest/api/searchservice/search-documents) som inte ska förväxlas med [syntaxen OData](query-odata-filter-orderby-syntax.md) som används för [parametern $filter](search-filters.md) för det API:et. Dessa olika syntaxer har sina egna regler för att konstruera frågor, fly strängar och så vidare.
>
> Azure Cognitive Search ger en alternativ [fullständig Lucene-frågesyntax](query-lucene-syntax.md) för mer komplexa frågor i sökparametern. **search** Mer information om frågetolkningsarkitektur och fördelar med varje syntax finns [i Hur fulltextsökning fungerar i Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="invoke-simple-parsing"></a>Anropa enkel tolkning

Enkel syntax är standard. Åkarop är bara nödvändigt om du återställer syntaxen från fullständig till enkel. Om du uttryckligen vill `queryType` ange syntaxen använder du sökparametern. Giltiga värden `queryType=simple` `queryType=full`inkluderar `simple` eller , var `full` är standard, och anropar den [fullständiga Lucene-frågetolken](query-lucene-syntax.md) för mer avancerade frågor. 

## <a name="syntax-fundamentals"></a>Syntax fundamenta

All text med ett eller flera termer betraktas som en giltig startpunkt för körning av frågor. Azure Cognitive Search matchar dokument som innehåller något eller alla termer, inklusive eventuella variationer som hittas under analysen av texten.

Så enkelt som det här låter finns det en aspekt av frågekörning i Azure Cognitive Search som *kan* ge oväntade resultat, öka i stället för att minska sökresultaten när fler termer och operatorer läggs till i indatasträngen. Om den här expansionen verkligen inträffar beror på att en NOT-operator inkluderas, i kombination med en parameterinställning för **searchMode** som avgör hur INTE tolkas i termer av OCH eller eller beteenden. Mer information finns i [OPERATORN INTE](#not-operator).

### <a name="precedence-operators-grouping"></a>Prioritetsoperatorer (gruppering)

Du kan använda parenteser för att skapa underfrågor, inklusive operatorer i den överordnade satsen. Till exempel `motel+(wifi||luxury)` kommer att söka efter dokument som innehåller "motell" sikt och antingen "wifi" eller "lyx" (eller båda).

Fältgruppering är liknande men omfattar grupperingen till ett enda fält. Till exempel `hotelAmenities:(gym+(wifi||pool))` söker fältet "hotelAmenities" för "gym" och "wifi", eller "gym" och "pool".  

### <a name="escaping-search-operators"></a>Flyr sökoperatorer  

För att kunna använda någon av sökoperatorerna som en del av söktexten, escape`\`tecknet genom att prefixa det med ett enda omvänt snedstreck ( ). För en jokerteckensökning på `https://`, `://` där är en del av `search=https\:\/\/*`frågesträngen, anger du till exempel . På samma sätt kan ett förrymt `\+1 \(800\) 642\-7676`telefonnummermönster se ut så här.

Specialtecken som kräver att du flyr innehåller följande:`- * ? \ /`  

För att göra det enkelt för de mer typiska fallen finns det två undantag från denna regel där det inte behövs att fly:  

+ INTE-operatorn `-` behöver bara fly om det är det första tecknet efter blanksteg, inte om det är mitt i en term. Följande GUID är till exempel giltigt utan `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`escape-tecknet: .

+ Suffixoperatorn `*` behöver bara fly om det är det sista tecknet före blanksteg, inte om det är mitt i en term. Kräver till `4*4=16` exempel inte ett omvänt snedstreck.

> [!NOTE]  
> Även om flykten håller token tillsammans, [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis) under indexering kan ta bort dem. Den vanliga Lucene-analysatorn tar till exempel bort och bryter ord på bindestreck, blanktecken och andra tecken. Om du behöver specialtecken i frågesträngen kan du behöva en analysator som bevarar dem i indexet. Några alternativ är Microsofts av [analysatorer för](index-add-language-analyzers.md)naturligt språk , som bevarar avstavade ord eller en anpassad analysator för mer komplexa mönster. Mer information finns i [Partiella termer, mönster och specialtecken](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Koda osäkra och reserverade tecken i webbadresser

Kontrollera att alla osäkra och reserverade tecken kodas i en URL. #' är till exempel ett osäkert tecken eftersom det är en fragment-/ankaridentifierare i en URL. Tecknet måste kodas `%23` till om det används i en URL. '&' och '=' är exempel på reserverade tecken när de avgränsar parametrar och anger värden i Azure Cognitive Search. Mer information finns i [RFC1738: Url (Uniform Resource Locators)](https://www.ietf.org/rfc/rfc1738.txt) för mer information.

Osäkra tecken ``" ` < > # % { } | \ ^ ~ [ ]``är . Reserverade `; / ? : @ = + &`tecken är .

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Begränsningar för frågestorlek

 Det finns en gräns för storleken på frågor som du kan skicka till Azure Cognitive Search. Specifikt kan du ha högst 1024-satser (uttryck åtskilda av OCH, ELLER och så vidare). Det finns också en gräns på cirka 32 KB för storleken på en enskild term i en fråga. Om ditt program genererar sökfrågor programmässigt rekommenderar vi att du utformar det på ett sådant sätt att det inte genererar frågor av obegränsad storlek.  

## <a name="boolean-search"></a>Boolesk sökning

Du kan bädda in booleska operatorer (OCH, ELLER, INTE) i en frågesträng för att skapa en omfattande uppsättning villkor mot vilka matchande dokument hittas. 

### <a name="and-operator-"></a>OCH operatör`+`

OPERATORn AND är ett plustecken. Du `wifi+luxury` söker till exempel efter `wifi` `luxury`dokument som innehåller både och .

### <a name="or-operator-"></a>ELLER-operatör`|`

OPERATORn ELLER är ett lodrät fält eller pipe-tecken. Du `wifi | luxury` söker till exempel efter `wifi` `luxury` dokument som innehåller antingen eller båda.

<a name="not-operator"></a>

### <a name="not-operator--"></a>INTE-operator`-`

OPERATORN NOT är ett minustecken. Till exempel `wifi –luxury` kommer att söka `wifi` efter dokument som har `luxury`termen och /eller inte har .

Parametern **searchMode** på en frågebegäran styr om en term med operatorn INTE är ANDed eller `+` ORed med andra termer i frågan (förutsatt att det inte finns någon eller `|` operator på de andra termerna). Giltiga värden `any` `all`inkluderar eller .

`searchMode=any`ökar återkallandet av frågor genom att inkludera `-` fler resultat, och som standard tolkas som "ELLER INTE". Till exempel `wifi -luxury` matchar dokument som antingen `wifi` innehåller termen eller de `luxury`som inte innehåller termen .

`searchMode=all`ökar precisionen i frågor genom att inkludera färre resultat, och som standard - kommer att tolkas som "OCH INTE". Till exempel `wifi -luxury` matchar dokument som `wifi` innehåller termen och inte innehåller termen "lyx". Detta är utan tvekan ett `-` mer intuitivt beteende för operatören. Därför bör du `searchMode=all` överväga `searchMode=any` att använda i stället för om du vill optimera sökningar `-` efter precision i stället för återkallande, *och* användarna använder ofta operatören i sökningar.

När du bestämmer dig för en **searchMode-inställning** bör du tänka på användarinteraktionsmönster för frågor i olika program. Användare som söker efter information är mer benägna att inkludera en operatör i en fråga, i motsats till e-handelswebbplatser som har mer inbyggda navigeringsstrukturer.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Prefixsökning

Suffixoperatorn är en `*`asterisk . Till exempel `lingui*` hittar "språkliga" eller "linguini", ignorera fall. 

I likhet med filter söker en prefixfråga efter en exakt matchning. Därför finns det ingen relevanspoängning (alla resultat får en sökpoäng på 1,0). Prefixfrågor kan vara långsamma, särskilt om indexet är stort och prefixet består av ett litet antal tecken. 

Om du vill köra en suffixfråga, som matchar den sista delen av strängen, använder du en [jokerteckensökning](query-lucene-syntax.md#bkmk_wildcard) och den fullständiga Lucene-syntaxen.

## <a name="phrase-search-"></a>Frassökning`"`

En termsökning är en fråga för ett eller flera termer, där något av villkoren betraktas som en matchning. En frassökning är en exakt fras `" "`som omges av citattecken . Till exempel, `Roach Motel` medan (utan citattecken) `Roach` skulle söka `Motel` efter dokument `"Roach Motel"` som innehåller och / eller var som helst i valfri ordning, (med citattecken) kommer bara att matcha dokument som innehåller hela frasen tillsammans och i den ordningen (textanalys fortfarande gäller).

## <a name="see-also"></a>Se även  

+ [Frågeexempel för enkel sökning](search-query-simple-examples.md)
+ [Frågeexempel för fullständig Lucene-sökning](search-query-lucene-examples.md)
+ [Sökdokument &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene-frågesyntax](query-lucene-syntax.md)
+ [OData-uttryckssyntax](query-odata-filter-orderby-syntax.md) 
