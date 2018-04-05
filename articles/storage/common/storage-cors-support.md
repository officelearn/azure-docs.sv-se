---
title: Stöd för Cross-Origin Resource delning (CORS) | Microsoft Docs
description: Lär dig hur du aktiverar CORS-stöd för Microsoft Azure Storage-tjänster.
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.openlocfilehash: 8d189d3ec3e6081dd37b912824f287cd75f39b35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Cross-Origin Resource Sharing (CORS) stöd för Azure Storage-tjänster
Från och med version 2013-08-15, stöd Azure-lagringstjänster för Cross-Origin Resource Sharing (CORS) för tjänster Blob, tabell, kö och fil. CORS är en HTTP-funktion som gör att ett webbprogram som körs i en domän kan komma åt resurser i en annan domän. Webbläsare som implementerar en säkerhetsbegränsningar som kallas [samma ursprung princip](http://www.w3.org/Security/wiki/Same_Origin_Policy) som förhindrar att en webbsida från anropa API: er i en annan domän. CORS ger ett säkert sätt att tillåta en domän (ursprungsdomän) att anropa API: er i en annan domän. Finns det [CORS-specifikationen](http://www.w3.org/TR/cors/) information om CORS.

Du kan ange CORS-regler individuellt för varje storage-tjänster genom att anropa [ange egenskaper för Blob-tjänsten](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kön Service](https://msdn.microsoft.com/library/hh452232.aspx), och [ange tjänsten Tabellegenskaper](https://msdn.microsoft.com/library/hh452240.aspx). När du har angett CORS-regler för tjänsten kommer en korrekt autentiserad begäran mot tjänsten från en annan domän att utvärderas för att avgöra om det är tillåtet enligt de regler som du har angett.

> [!NOTE]
> Observera att CORS inte är en autentiseringsmetod. Alla förfrågningar som görs mot en lagringsresurs när CORS är aktiverat måste antingen ha en korrekt autentisering signatur eller måste göras mot en offentlig resurs.
> 
> 

## <a name="understanding-cors-requests"></a>Förstå CORS-begäranden
En CORS-begäran från en ursprungsdomän kan bestå av två separata begäranden:

* En preflight-begäran som frågar CORS-begränsningar i tjänsten. Preflight-begäran måste anges om metoden begäran är en [enkel metod](http://www.w3.org/TR/cors/), vilket innebär att GET, HEAD eller POST.
* Den faktiska begäran som görs mot en resurs.

### <a name="preflight-request"></a>Preflight-begäran
Frågor preflight-begäran CORS-begränsningar som har upprättats för lagring av kontoägaren. Skickar en OPTIONS-begäran som innehåller begärandehuvuden, metod och ursprung domän webbläsaren (eller andra användaragent). Lagringstjänsten utvärderar operationen baserat på en förkonfigurerad uppsättning CORS-regler som anger vilka ursprungsdomäner och metodbegäranden begärandehuvuden kan anges för en faktiska begäran mot en lagringsresurs.

Om CORS är aktiverat för tjänsten och det finns en CORS-regel som matchar begärande-preflight-tjänsten svarar med statuskod 200 (OK) och innehåller nödvändiga Access Control-huvuden i svaret.

Om CORS inte har aktiverats för tjänsten eller inga CORS-regeln matchar preflight-begäran, svarar tjänsten med statuskod 403 (förbjuden).

Om OPTIONS-begäran inte innehåller de nödvändiga CORS huvudena (ursprung och-begäran-metoden för åtkomstkontroll rubriker), svarar tjänsten med statuskod: 400 (felaktig begäran).

Observera att en preflight-begäran ska utvärderas mot tjänsten (Blob, köer och tabell) och inte mot den begärda resursen. Ägare måste ha aktiverat CORS som en del av tjänsten kontoegenskaperna för att begäran ska lyckas.

### <a name="actual-request"></a>Faktiska begäran
När preflight-begäran har godkänts och svaret returneras skickar webbläsaren den faktiska begäran mot storage-resursen. Webbläsaren nekar den faktiska begäran omedelbart om preflight-begäran avvisas.

Den faktiska begäranden behandlas som normal begäran mot storage-tjänst. Förekomsten av ursprung huvudet anger att begäran är en CORS-begäran och tjänsten kontrollerar matchande CORS-regler. Om en matchning hittas, Access Control-huvuden läggs till i svaret och skickas tillbaka till klienten. Om en matchning hittas returneras inte CORS-åtkomstkontroll-huvuden.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Hur du aktiverar CORS för Azure Storage-tjänster
CORS-regler är inställda på tjänstnivå, så du behöver aktivera eller inaktivera CORS för varje tjänst (Blob, köer och tabellen) separat. Som standard inaktiveras CORS för varje tjänst. Om du vill aktivera CORS du måste ange egenskaperna lämplig tjänst som använder version 2013-08-15 eller senare, och Lägg till CORS-regler i egenskaperna för tjänsten. Mer information om hur du aktiverar eller inaktiverar CORS för en tjänst och hur du konfigurerar CORS-regler, se [ange egenskaper för Blob-tjänsten](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kön Service](https://msdn.microsoft.com/library/hh452232.aspx), och [ange Tabelltjänsten Egenskaper för](https://msdn.microsoft.com/library/hh452240.aspx).

Här är ett exempel på en enda CORS-regel som angetts via en åtgärden Ange egenskaper för tjänsten:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Varje element som ingår i regeln CORS beskrivs nedan:

* **AllowedOrigins**: ursprungsdomäner som tillåts att begära mot lagringstjänsten via CORS. Den ursprung är den domän som begäran kommer från. Observera att ursprung måste vara en exakt skiftlägeskänslig matchning med ursprung som användaren ålder skickar till tjänsten. Du kan också använda jokertecknet ' *' så att alla ursprungsdomäner att göra förfrågningar via CORS. I exemplet ovan är domänerna [ http://www.contoso.com ](http://www.contoso.com) och [ http://www.fabrikam.com ](http://www.fabrikam.com) kan göra förfrågningar till tjänsten med hjälp av CORS.
* **AllowedMethods**: metoder (http-begäran verb) som den ursprungliga domänen kan använda för en CORS-begäran. I exemplet ovan tillåts bara PUT- och GET-begäranden.
* **AllowedHeaders**: huvuden för begäran som den ursprungliga domänen får ange på CORS-begäran. I exemplet ovan tillåts alla metadata huvuden som börjar med x-ms-metadata, x-ms-meta-mål- och x-ms-meta-abc. Observera att jokertecknet ' *' anger att alla huvud som börjar med det angivna prefixet tillåts.
* **ExposedHeaders**: svarshuvuden som kan skickas som svar på begäran CORS och visas av webbläsaren att utfärdaren begäran. I exemplet ovan instrueras webbläsaren att exponera en rubrik som börjar med x-ms-metadata.
* **MaxAgeInSeconds**: den maximala tiden att webbläsaren ska cachelagrar Preflight-alternativ för begäran.

Azure-lagringstjänster stöd för att ange prefix huvuden för både den **AllowedHeaders** och **ExposedHeaders** element. Du kan ange ett gemensamt prefix till den kategorin för att tillåta en kategori av huvuden. Ange till exempel *x-ms-meta** som ett prefix huvud upprättar en regel som matchar alla huvuden som börjar med x-ms-metadata.

Följande begränsningar gäller CORS-regler:

* Du kan ange upp till fem CORS-regler per lagringstjänsten (Blob, tabeller och köer).
* Den maximala storleken för alla CORS regler inställningar på begäran, exklusive XML-taggar får inte överstiga 2 KB.
* Längden på ett tillåtna sidhuvud, exponerade sidhuvud eller tillåtna ursprung får innehålla högst 256 tecken.
* Tillåtna sidhuvuden och exponerade huvuden kan vara antingen:
  * Literalen sidhuvud, där exakt huvudets namn tillhandahålls som **x-ms-meta-bearbetas**. Högst 64 literal huvuden kan anges i begäran.
  * Prefixet sidhuvud, där prefixet huvudet tillhandahålls som ** x-ms-meta-data ***. Ange ett prefix i det här sättet kan eller visar de huvuden som börjar med det angivna prefixet. Högst två prefix huvuden kan anges i begäran.
* Metoder (eller HTTP-verb) anges i den **AllowedMethods** element måste följa de metoder som stöds av Azure storage-tjänst API: er. Metoder som stöds är DELETE, GET, HEAD, MERGE, POST, alternativ och PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Förstå CORS regellogik utvärdering
När storage-tjänst tar emot en begäran preliminära eller faktiska, utvärderar begäran baserat på CORS-regler som du har skapat för tjänsten via åtgärden Ange egenskaper för tjänsten. CORS-regler utvärderas i den ordning som de har ställts in i begärandetexten för åtgärden Ange egenskaper för tjänsten.

CORS-regler utvärderas enligt följande:

1. Först ursprungsdomän i begäran kontrolleras mot de domäner som anges för den **AllowedOrigins** element. Om ursprungsdomänen ingår i listan, eller alla domäner som är tillåtna för jokertecknet ' *', regler utvärdering fortsätter. Om den ursprungliga domänen inte finns, misslyckas denna begäran.
2. Därefter metod (eller HTTP-verb) för begäran kontrolleras mot de metoder som anges i den **AllowedMethods** element. Om metoden ingår i listan, fortsätter regler utvärdering; annars misslyckas begäran.
3. Om begäran matchar en regel i sin ursprungliga domän och dess metod, väljs regeln att bearbeta begäran och inga ytterligare regler utvärderas. Innan begäran kan lyckas men huvuden som anges i begäran kontrolleras mot de rubriker som anges i den **AllowedHeaders** element. Om huvuden som skickas inte matchar de tillåtna rubrikerna, misslyckas denna begäran.

Eftersom regler bearbetas i den ordning som de finns i frågans brödtext rekommenderar bästa praxis att du anger de mest restriktiva reglerna med avseende på ursprung först i listan så att dessa utvärderas först. Ange regler som är mindre restriktivt – till exempel en regel som tillåter alla ursprung – i slutet av listan.

### <a name="example--cors-rules-evaluation"></a>Exempel – CORS regler utvärdering
I följande exempel visas en partiell begärantext för en åtgärd för att ange CORS-regler för storage-tjänster. Se [ange egenskaper för Blob-tjänsten](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kön Service](https://msdn.microsoft.com/library/hh452232.aspx), och [ange egenskaper för tabellen Service](https://msdn.microsoft.com/library/hh452240.aspx) information om hur du skapar begäran.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Överväg därefter följande ansökningar om CORS:

| Förfrågan |  |  | Svar |  |
| --- | --- | --- | --- | --- |
| **Metoden** |**Origin** |**Huvuden för begäran** |**Regeln matchar** |**Resultat** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Första regeln |Lyckades |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Andra regeln |Lyckades |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Andra regeln |Fel |

Den första begäranden matchar den första regeln – den ursprungliga domänen matchar tillåtna ursprung, metoden matchar tillåtna metoder och huvudet matchar tillåtna huvuden – och så lyckas.

Andra begäran matchar inte den första regeln eftersom metoden inte matchar de tillåtna metoderna. Den, men matchar den andra regeln, så den lyckas.

Tredje begäran matchar den andra regeln i dess ursprungsdomän och metod, så inga ytterligare regler utvärderas. Men den *x-ms-client-request-id-huvudet* tillåts inte av den andra regeln, så begäran misslyckas trots semantiken för tredje regeln skulle användas till att lyckas.

> [!NOTE]
> Även om det här exemplet illustrerar en mindre begränsande regel innan en mer begränsande, vanligtvis det bästa sättet är att visa de mest restriktiva reglerna först.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Förstå hur Vary-huvud har angetts
Den *variera* huvud är en standard HTTP/1.1-rubrik som består av en uppsättning begäran huvudfält som meddela agenten webbläsare eller användare om de kriterier som valts av servern vid bearbetning av begäran. Den *variera* huvud används huvudsakligen för cachelagring av proxyservrar, webbläsare och andra CDN-lösningar, som använder den för att fastställa hur svaret ska cachelagras. Mer information finns i specifikationen för den [Vary-huvud](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

När webbläsaren eller en annan användaragent cachelagrar svaret från en CORS-begäran, cachelagras ursprungsdomänen som tillåtna ursprung. När en domän skickar samma begäran om en lagringsresurs när cachen är aktiv, hämtar användaragenten cachelagrade ursprungliga domän. Den andra domänen matchar inte domänen cachelagrade, så misslyckas med begäran när den annars skulle lyckas. I vissa fall anger Azure Storage Vary-huvud till **ursprung** instruera användaragent skicka efterföljande CORS-begäran till tjänsten när domänen begärande skiljer sig från det cachelagrade ursprunget.

Azure Storage-anger den *variera* sidhuvud till **ursprung** för faktiska GET/HEAD-begäranden i följande fall:

* När begäran ursprung matchar exakt tillåtna ursprung definieras av en regel för CORS. För att vara en exakt matchning CORS regeln inte innehåller jokertecken ' * ' tecken.
* Det finns ingen regel som matchar begäran ursprung, men CORS är aktiverat för storage-tjänst.

I de fall där en GET/HEAD-begäran matchar en CORS-regel som tillåter alla ursprung, anger svaret att alla ursprung är tillåtna och agent användarcachen tillåter efterföljande förfrågningar från alla ursprungsdomän när cachen är aktiv.

Observera att för begäranden med andra metoder än GET/HEAD, storage-tjänster kommer inte att ange Vary-huvud eftersom svar på dessa metoder inte cachelagras av användaragenter.

Följande tabell visar hur Azure storage svarar på GET/HEAD-begäranden baserat på de tidigare nämnda fall:

| Förfrågan | Kontoinställningen och resultat av utvärderingen av distributionsregeln |  |  | Svar |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Ursprung huvudet finns på begäran** |**CORS-regler som angetts för denna tjänst** |**Det finns matchande regel som tillåter alla origins(*)** |**Matchande regel som finns för ursprung exakt matchning** |**Svaret innehåller Vary-huvudet inställt till ursprung** |**Svaret innehåller Access Control-tillåtna-ursprung ”: *”** |**Svaret innehåller Access-Control-exponeras-rubriker** |
| Nej |Nej |Nej |Nej |Nej |Nej |Nej |
| Nej |Ja |Nej |Nej |Ja |Nej |Nej |
| Nej |Ja |Ja |Nej |Nej |Ja |Ja |
| Ja |Nej |Nej |Nej |Nej |Nej |Nej |
| Ja |Ja |Nej |Ja |Ja |Nej |Ja |
| Ja |Ja |Nej |Nej |Ja |Nej |Nej |
| Ja |Ja |Ja |Nej |Nej |Ja |Ja |

## <a name="billing-for-cors-requests"></a>Fakturering för CORS-begäranden
Lyckad preflight begär debiteras om du har aktiverat CORS för lagringstjänster för ditt konto (genom att anropa [ange egenskaper för Blob-tjänsten](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kön Service](https://msdn.microsoft.com/library/hh452232.aspx), eller [Ange egenskaper för tabellen](https://msdn.microsoft.com/library/hh452240.aspx)). För att minimera kostnader, bör du ställa av **MaxAgeInSeconds** element i din CORS-regler till ett högt värde så att användaragenten cachelagrar begäran.

Kommer inte att debiteras misslyckade preflight-begäranden.

## <a name="next-steps"></a>Nästa steg
[Ange egenskaper för Blob-tjänst](https://msdn.microsoft.com/library/hh452235.aspx)

[Ange egenskaper för kön](https://msdn.microsoft.com/library/hh452232.aspx)

[Ange egenskaper för tabellen](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C Cross-Origin Resource Sharing specifikation](http://www.w3.org/TR/cors/)

