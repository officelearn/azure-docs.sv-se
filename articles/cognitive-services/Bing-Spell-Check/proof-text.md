---
title: Vad är API för Bing-stavningskontroll?
titlesuffix: Azure Cognitive Services
description: API för Bing-stavningskontroll använder maskininlärning och statistisk maskinöversättning för kontextbaserad stavningskontroll.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 4caa05ffa96dbc15922fed85edfdefdb68ead68b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361719"
---
# <a name="what-is-bing-spell-check-api"></a>Vad är API för Bing-stavningskontroll?

Med API för Bing-stavningskontroll kan du utföra kontextbaserad kontroll av grammatik och stavning.

Vad är skillnaden mellan vanliga stavningskontroller och tredje generationens Bing-stavningskontroll? Vanliga stavningskontroller kontrollerar stavning och grammatik mot ordlistebaserade regeluppsättningar som regelbundet uppdateras och utökas. Med andra ord är stavningskontrollen bara lika bra som den ordlista den arbetar med, och den språkredaktion som underhåller ordlistan. Du känner igen den här typen av stavningskontroll från Microsoft Word och andra ordbehandlingsprogram.

Bing har i stället utvecklat en webbaserad stavningskontroll som använder maskininlärning och statistisk maskinöversättning för att dynamiskt lära upp en ständigt växande och mycket sammanhangsberoende algoritm. Stavningskontrollen baseras på en omfattande korpus av webbsökningar och dokument.

Den här stavningskontrollen klarar alla ordbehandlingsscenarier:

- Känner igen slang och talspråk
- Känner igen vanliga namnfel i kontext
- Korrigerar avstavningsproblem med en flagga
- Kan korrigera homofoner i kontext, och andra fel som är svåra att upptäcka
- Har stöd för nya varumärken, digital underhållning och populära uttryck allt eftersom de dyker upp
- Ord som låter likadant men skiljer sig åt i betydelse och stavning, till exempel ”jul” och ”hjul”.

## <a name="spell-check-modes"></a>Stavningskontrollslägen

Detta API har två korrigeringslägen, `Proof` och `Spell`.  Prova några exempel [här](https://azure.microsoft.com/services/cognitive-services/spell-check/).
### <a name="proof---for-documents-scenario"></a>Korrektur – för dokumentscenarier
Standardläget är `Proof`. Stavningsläget `Proof` ger de mest omfattande kontrollerna, och lägger till versaler, grundläggande interpunktion och andra funktioner när du skapar dokument. Det är bara tillgängligt för en-US (amerikansk engelska), es-ES (spanska), pt-BR (portugisiska) (Obs! endast i betaversion för spanska och portugisiska). För övriga marknader anger du lägesfrågeparametern till Stavning. 
<br /><br/>**Ob!**  Om frågetexten är längre än 4 096 tecken kortas den av till 4 096 tecken innan den bearbetas. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Stavningskontroll – för webb-/sökningsscenarier
`Spell` är mer aggressivt för att returnera bättre sökresultat. Läget `Spell` hittar de flesta stavfel men hittar inte en del grammatikfel som `Proof` fångar upp, som versaler/gemener och upprepade ord.
<br /></br>**Ob!** Maximal frågelängd visas nedan. Om frågan överskrider teckenbegränsningen visas resultatet som om frågan inte har ändrats.
<ul><li>130 tecken för språkkod för en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, den, zh, ko. </li>
<li>65 tecken för övriga språk</li></ul>

## <a name="market-setting"></a>Marknadsinställning
Marknad måste anges i frågeparametern i frågans webbadress. Annars använder stavningskontrollen standardmarknaden baserat på IP-adress.


## <a name="post-vs-get"></a>POST eller HÄMTA

Detta API har stöd för HTTP POST eller HTTP GET. Vilket du ska använda beror på längden på den text som du ska korrekturläsa. Om strängarna alltid är högst än 1 500 tecken använder du GET (hämtning). Om du vill kunna använda strängar på upp till 10 000 tecken använder du POST (publicering). Textsträngen får innehålla giltiga UTF-8-tecken.

I följande exempel visas en POST-begäran för kontroll av stavning och grammatik i en textsträng. Exemplet innehåller frågeparametern [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) (läge) för fullständighetens skull (den kunde ha utelämnats eftersom standard för `mode` är Korrektur). Frågeparametern [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) innehåller den sträng som ska korrigeras.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Om du använder HTTP GET måste du även ange frågeparametern `text` i webbadressens frågesträng
  
Nedan visas svaret på den tidigare begäran. Svaret innehåller ett [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck)-objekt. 
  
```  
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
I fältet [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) visas en lista över de stavnings- och grammatikfel som hittats i [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text)-strängen. I fältet `token` finns det ord som ska bytas ut. Du använder den nollbaserade förskjutningen i fältet `offset` för att hitta token i `text`-strängen. Sedan ersätter du ordet på den platsen med ordet i fältet `suggestion`. 

Om fältet `type` är RepeatedToken kan du ändå ersätta token med `suggestion`, men behöver troligen också att ta bort avslutande blanksteget.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt med din första begäran genom att läsa [Göra din första begäran](quickstarts/csharp.md).

Bekanta dig med [referensen för API för Bing-stavningskontroll](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference). Referensen innehåller en lista över de slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat, samt definitionerna av svarsobjekten. 

Läs [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna för användning av sökresultat.
