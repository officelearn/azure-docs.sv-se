---
title: Stöd för resursdelning mellan ursprung ( CORS) | Microsoft-dokument
description: Lär dig hur du aktiverar CORS-support för Microsoft Azure Storage Services.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bb296db0d97382deac984369704777de5d5cb362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65147694"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Cors-stöd (Cross-Origin Resource Sharing) för Azure Storage Services
Från och med version 2013-08-15 stöder Azure-lagringstjänsterna CORS (Cross-Origin Resource Sharing) för Blob-, Table-, Queue- och File-tjänsterna. CORS är en HTTP-funktion som gör att ett webbprogram som körs under en domän kan komma åt resurser i en annan domän. Webbläsare implementerar en säkerhetsbegränsning som kallas [samma princip](https://www.w3.org/Security/wiki/Same_Origin_Policy) som förhindrar att en webbsida anropar API:er i en annan domän. CORS är ett säkert sätt att tillåta en domän (ursprungsdomänen) att anropa API:er i en annan domän. Se [CORS-specifikationen](https://www.w3.org/TR/cors/) för mer information om CORS.

Du kan ange CORS-regler individuellt för var och en av lagringstjänsterna genom att anropa [Ange Blob-tjänstegenskaper,](https://msdn.microsoft.com/library/hh452235.aspx)Ange egenskaper för [kötjänst](https://msdn.microsoft.com/library/hh452232.aspx)och [Ange tabelltjänstegenskaper](https://msdn.microsoft.com/library/hh452240.aspx). När du har angett CORS-reglerna för tjänsten utvärderas en korrekt auktoriserad begäran mot tjänsten från en annan domän för att avgöra om den är tillåten enligt de regler som du har angett.

> [!NOTE]
> Observera att CORS inte är en autentiseringsmekanism. Alla begäranden som görs mot en lagringsresurs när CORS är aktiverat måste antingen ha en korrekt autentiseringssignatur eller göras mot en offentlig resurs.
> 
> 

## <a name="understanding-cors-requests"></a>Förstå CORS-begäranden
En CORS-begäran från en ursprungsdomän kan bestå av två separata begäranden:

* En preflight-begäran som frågar efter de CORS-begränsningar som tjänsten har infört. Preflight-begäran krävs om inte begäransmetoden är en [enkel metod](https://www.w3.org/TR/cors/), vilket betyder GET, HEAD eller POST.
* Den faktiska begäran, som görs mot önskad resurs.

### <a name="preflight-request"></a>Begäran om preflight
Preflight-begäran frågar de CORS-begränsningar som har upprättats för lagringstjänsten av kontoägaren. Webbläsaren (eller annan användaragent) skickar en OPTIONS-begäran som innehåller begäranden, metoden och ursprungsdomänen. Lagringstjänsten utvärderar den avsedda åtgärden baserat på en förkonfigurerad uppsättning CORS-regler som anger vilka ursprungsdomäner, begäransmetoder och begäranden som kan anges på en faktisk begäran mot en lagringsresurs.

Om CORS är aktiverat för tjänsten och det finns en CORS-regel som matchar preflight-begäran, svarar tjänsten med statuskod 200 (OK) och innehåller de åtkomstkontrollhuvuden som krävs i svaret.

Om CORS inte är aktiverat för tjänsten eller om ingen CORS-regel matchar preflight-begäran, kommer tjänsten att svara med statuskod 403 (Förbjudet).

Om OPTIONS-begäran inte innehåller de nödvändiga CORS-huvudena (rubrikerna Origin och Access-Control-Request-Method) svarar tjänsten med statuskod 400 (Felaktig begäran).

Observera att en preflight-begäran utvärderas mot tjänsten (Blob, Kö och Tabell) och inte mot den begärda resursen. Kontoägaren måste ha aktiverat CORS som en del av kontotjänstegenskaperna för att begäran ska lyckas.

### <a name="actual-request"></a>Faktisk begäran
När preflight-begäran har accepterats och svaret returneras skickar webbläsaren den faktiska begäran mot lagringsresursen. Webbläsaren kommer att neka den faktiska begäran omedelbart om preflight-begäran avvisas.

Den faktiska begäran behandlas som en vanlig begäran mot lagringstjänsten. Förekomsten av Origin-huvudet anger att begäran är en CORS-begäran och tjänsten kommer att kontrollera de matchande CORS-reglerna. Om en matchning hittas läggs åtkomstkontrollhuvudena till i svaret och skickas tillbaka till klienten. Om ingen matchning hittas returneras inte CORS-åtkomstkontrollhuvudena.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Aktivera CORS för Azure Storage-tjänsterna
CORS-regler anges på servicenivå, så du måste aktivera eller inaktivera CORS för varje tjänst (Blob, Kö och Tabell) separat. Som standard inaktiveras CORS för varje tjänst. Om du vill aktivera CORS måste du ange lämpliga tjänstegenskaper med version 2013-08-15 eller senare och lägga till CORS-regler i tjänstegenskaperna. Mer information om hur du aktiverar eller inaktiverar CORS för en tjänst och hur du anger CORS-regler finns i Ange egenskaper för [Blob-tjänst,](https://msdn.microsoft.com/library/hh452235.aspx) [Ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx)och [Ange tabelltjänstegenskaper](https://msdn.microsoft.com/library/hh452240.aspx).

Här är ett exempel på en enda CORS-regel, som anges via en ange tjänstegenskaper:

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

Varje element som ingår i CORS-regeln beskrivs nedan:

* **AllowedOrigins**: Ursprungsdomäner som tillåts göra en begäran mot lagringstjänsten via CORS. Ursprungsdomänen är den domän som begäran kommer från. Observera att ursprunget måste vara en exakt skiftlägeskänslig matchning med det ursprung som användarens ålder skickar till tjänsten. Du kan också använda jokertecknet '*' för att tillåta alla ursprungsdomäner att göra förfrågningar via CORS. I exemplet ovan kan domänerna\/http: /www.contoso.com och\/http: /www.fabrikam.com göra förfrågningar mot tjänsten med CORS.
* **AllowedMethods**: De metoder (HTTP-begäran verb) som ursprungdomänen kan använda för en CORS-begäran. I exemplet ovan tillåts endast PUT-begäranden och GET-begäranden.
* **AllowedHeaders**: De begäranden som ursprungsdomänen kan ange på CORS-begäran. I exemplet ovan tillåts alla metadatarubriker som börjar med x-ms-meta-data, x-ms-meta-target och x-ms-meta-abc. Observera att jokertecknet '*' anger att alla rubriker som börjar med det angivna prefixet är tillåtna.
* **ExposedHeaders**: Svarsrubrikerna som kan skickas i svaret på CORS-begäran och exponeras av webbläsaren för begärandeutfärdaren. I exemplet ovan instrueras webbläsaren att exponera alla rubriker som börjar med x-ms-meta.
* **MaxAgeInSeconds**: Den maximala tid som en webbläsare bör cachelagra preflight OPTIONS-begäran.

Azure-lagringstjänsterna har stöd för att ange prefixerade huvuden för både **allowedheaders-** och **ExposedHeaders-elementen.** Om du vill tillåta en kategori med rubriker kan du ange ett gemensamt prefix till den kategorin. Om du till exempel anger *x-ms-meta** som ett prefixerat huvud upprättas en regel som matchar alla rubriker som börjar med x-ms-meta.

Följande begränsningar gäller för CORS-reglerna:

* Du kan ange upp till fem CORS-regler per lagringstjänst (Blob, Tabell och Kö).
* Den maximala storleken på alla CORS-reglerinställningar på begäran, exklusive XML-taggar, bör inte överstiga 2 KB.
* Längden på ett tillåtet huvud, ett översänt huvud eller tillåtet ursprung får inte överstiga 256 tecken.
* Tillåtna rubriker och exponerade rubriker kan vara antingen:
  * Litterala rubriker, där det exakta rubriknamnet anges, till exempel **x-ms-meta-bearbetad**. Högst 64 litterala rubriker kan anges på begäran.
  * Prefixerade rubriker, där ett prefix av huvudet tillhandahålls, till exempel **x-ms-meta-data***. Om du anger ett prefix på det här sättet kan eller exponeras alla rubriker som börjar med det angivna prefixet. Högst två prefixerade rubriker kan anges på begäran.
* De metoder (eller HTTP-verb) som anges i elementet **AllowedMethods** måste överensstämma med de metoder som stöds av Azure Storage Service API:er. Metoder som stöds är DELETE, GET, HEAD, MERGE, POST, OPTIONS och PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Förstå CORS-regelutvärderingslogik
När en lagringstjänst tar emot en preflight- eller faktisk begäran utvärderas den begäran baserat på de CORS-regler som du har upprättat för tjänsten via lämplig ange tjänstegenskaper. CORS-regler utvärderas i den ordning som de angavs i begäranden för åtgärden Ange tjänstegenskaper.

CORS-reglerna utvärderas på följande sätt:

1. Först kontrolleras ursprungsdomänen för begäran mot de domäner som anges för elementet **AllowedOrigins.** Om ursprungsdomänen ingår i listan, eller om alla domäner tillåts med jokertecknet '*', fortsätter utvärderingen av reglerna. Om ursprungsdomänen inte inkluderas misslyckas begäran.
2. Därefter kontrolleras metoden (eller HTTP-verbet) för begäran mot de metoder som anges i elementet **AllowedMethods.** Om metoden ingår i listan fortsätter utvärderingen av reglerna. Om inte, misslyckas begäran.
3. Om begäran matchar en regel i ursprungsdomänen och dess metod väljs den regeln för att bearbeta begäran och inga ytterligare regler utvärderas. Innan begäran kan lyckas kontrolleras dock alla rubriker som anges på begäran mot rubrikerna i elementet **AllowedHeaders.** Om de skickade rubrikerna inte matchar de tillåtna rubrikerna misslyckas begäran.

Eftersom reglerna bearbetas i den ordning de finns i begärandeorganet rekommenderar bästa praxis att du anger de mest restriktiva reglerna för ursprung först i listan, så att dessa utvärderas först. Ange regler som är mindre restriktiva – till exempel en regel för att tillåta alla ursprung – i slutet av listan.

### <a name="example--cors-rules-evaluation"></a>Exempel – Utvärdering av CORS-regler
I följande exempel visas ett partiellt begärandeorgan för en åtgärd för att ange CORS-regler för lagringstjänsterna. Se [Ange egenskaper för Blob-tjänst,](https://msdn.microsoft.com/library/hh452235.aspx)Ange egenskaper för [kötjänst](https://msdn.microsoft.com/library/hh452232.aspx)och [Ange tabelltjänstegenskaper](https://msdn.microsoft.com/library/hh452240.aspx) för mer information om hur du konstruerar begäran.

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

Tänk sedan på följande CORS-begäranden:

| Förfrågan |  |  | Svar |  |
| --- | --- | --- | --- | --- |
| **Metod** |**Ursprung** |**Rubriker för begäran** |**Regelmatchning** |**Resultat** |
| **Sätta** |http:\//www.contoso.com |x-ms-blob-content-typ |Första regeln |Lyckades |
| **Få** |http:\//www.contoso.com |x-ms-blob-content-typ |Andra regeln |Lyckades |
| **Få** |http:\//www.contoso.com |x-ms-klient-begäran-id |Andra regeln |Fel |

Den första begäran matchar den första regeln – ursprungsdomänen matchar det tillåtna ursprunget, metoden matchar de tillåtna metoderna och huvudet matchar de tillåtna rubrikerna – och lyckas så.

Den andra begäran matchar inte den första regeln eftersom metoden inte matchar de tillåtna metoderna. Det matchar dock den andra regeln, så det lyckas.

Den tredje begäran matchar den andra regeln i dess ursprungsdomän och -metod, så inga ytterligare regler utvärderas. *X-ms-client-request-id-huvudet* tillåts dock inte av den andra regeln, så begäran misslyckas, trots att semantiken i den tredje regeln skulle ha tillåtit den att lyckas.

> [!NOTE]
> Även om det här exemplet visar en mindre restriktiv regel före en mer restriktiv regel, är det i allmänhet bäst att först ange de mest restriktiva reglerna.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Förstå hur rubriken Vary är inställt
*Vary-huvudet* är ett standard-HTTP/1.1-huvud som består av en uppsättning sidfält för begäran som ger webbläsaren eller användaragenten råd om de kriterier som servern har valt för att bearbeta begäran. *Vary-huvudet* används huvudsakligen för cachelagring av proxyservrar, webbläsare och CDN-nätverk, som använder det för att avgöra hur svaret ska cachelagras. Mer information finns i specifikationen för [Vary-huvudet](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

När webbläsaren eller en annan användaragent cachelagrar svaret från en CORS-begäran cachelagras ursprungsdomänen som tillåtet ursprung. När en andra domän utfärdar samma begäran om en lagringsresurs medan cacheminnet är aktivt hämtar användaragenten den cachelagrade ursprungsdomänen. Den andra domänen matchar inte den cachelagrade domänen, så begäran misslyckas när den annars skulle lyckas. I vissa fall anger Azure Storage Vary-huvudet till **Origin** för att instruera användaragenten att skicka den efterföljande CORS-begäran till tjänsten när den begärnde domänen skiljer sig från det cachelagrade ursprunget.

Azure Storage anger *Vary-huvudet* till **Origin** för faktiska GET/HEAD-begäranden i följande fall:

* När begäran ursprung exakt matchar det tillåtna ursprunget definieras av en CORS-regel. För att vara en exakt matchning kanske CORS-regeln inte innehåller ett jokertecken .
* Det finns ingen regel som matchar begärans ursprung, men CORS är aktiverat för lagringstjänsten.

Om en GET/HEAD-begäran matchar en CORS-regel som tillåter alla ursprung, anger svaret att alla ursprung är tillåtna och användaragentcachen tillåter efterföljande begäranden från alla ursprungsdomäner medan cachen är aktiv.

Observera att för begäranden som använder andra metoder än GET/HEAD kommer lagringstjänsterna inte att ange Vary-huvudet, eftersom svar på dessa metoder inte cachelagras av användaragenter.

Följande tabell visar hur Azure-lagring kommer att svara på GET/HEAD-begäranden baserat på de tidigare nämnda fallen:

| Förfrågan | Kontoinställning och resultat av regelutvärdering |  |  | Svar |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Ursprungsrubrik presenterad på begäran** |**CORS-regler som angetts för den här tjänsten** |**Matchningsregel finns som tillåter alla origins(*)** |**Matchningsregel finns för exakt ursprungsmatchning** |**Svaret inkluderar Vary-huvuduppsättningen till Origin** |**Svaret inkluderar Access-Control-Tillåtet-Ursprung: "*"** |**Svaret inkluderar Åtkomstkontroll-Exponerade-Rubriker** |
| Inga |Inga |Inga |Inga |Inga |Inga |Inga |
| Inga |Ja |Inga |Inga |Ja |Inga |Inga |
| Inga |Ja |Ja |Inga |Inga |Ja |Ja |
| Ja |Inga |Inga |Inga |Inga |Inga |Inga |
| Ja |Ja |Inga |Ja |Ja |Inga |Ja |
| Ja |Ja |Inga |Inga |Ja |Inga |Inga |
| Ja |Ja |Ja |Inga |Inga |Ja |Ja |

## <a name="billing-for-cors-requests"></a>Fakturering för CORS-förfrågningar
Lyckade preflight-begäranden faktureras om du har aktiverat CORS för någon av lagringstjänsterna för ditt konto (genom att anropa [Ange Blob-tjänstegenskaper,](https://msdn.microsoft.com/library/hh452235.aspx) [Ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx)eller Ange [tabelltjänstegenskaper](https://msdn.microsoft.com/library/hh452240.aspx)). För att minimera avgifter, överväg att ställa in **MaxAgeInSeconds** elementet i CORS-reglerna till ett stort värde så att användaragenten cachelagrar begäran.

Misslyckade preflight-begäranden kommer inte att faktureras.

## <a name="next-steps"></a>Nästa steg
[Ange egenskaper för Blob-tjänst](https://msdn.microsoft.com/library/hh452235.aspx)

[Ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx)

[Egenskaper för ange tabelltjänst](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C Specifikation för resursdelning över ursprung](https://www.w3.org/TR/cors/)

