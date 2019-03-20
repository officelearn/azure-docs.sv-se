---
title: Cross-Origin Resource Sharing (CORS) Support | Microsoft Docs
description: Lär dig hur du aktiverar CORS-stöd för Microsoft Azure Storage-tjänster.
services: storage
author: cbrooksmsft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: bb1f4861f3867c592ecab86e85d3a4dfbab6738e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002950"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Cross-Origin Resource Sharing (CORS) Support för Azure Storage-tjänster
Från och med version 2013-08-15, stöder Azure storage-tjänster Cross-Origin Resource Sharing (CORS) för tjänsterna Blob, tabell, kö och filen. CORS är en HTTP-funktion som gör ett webbprogram som körs i en domän att komma åt resurser i en annan domän. Webbläsare implementerar en säkerhetsbegränsning som kallas [princip om samma ursprung](https://www.w3.org/Security/wiki/Same_Origin_Policy) som förhindrar att en webbsida från anropa API: er i en annan domän. CORS erbjuder ett säkert sätt att tillåta en domän (ursprungsdomänen) att anropa API: er i en annan domän. Se den [CORS-specifikationen](https://www.w3.org/TR/cors/) mer information om CORS.

Du kan ange CORS-regler individuellt för var och en av storage-tjänster genom att anropa [ange egenskaper för Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx), och [ange tabellen tjänstegenskaper](https://msdn.microsoft.com/library/hh452240.aspx). När du har angett CORS-regler för tjänsten kommer en korrekt auktoriserad begäran som görs mot tjänsten från en annan domän att utvärderas för att avgöra om den är tillåten enligt de regler som du har angett.

> [!NOTE]
> Observera att CORS inte är en autentiseringsmetod. Alla förfrågningar som görs mot en lagringsresurs när CORS är aktiverat måste antingen ha en korrekt autentisering signatur eller måste göras mot en offentlig resurs.
> 
> 

## <a name="understanding-cors-requests"></a>Förstå CORS-förfrågningar
En CORS-förfrågan från en ursprungsdomän får bestå av två separata förfrågningar:

* En preflight-begäran, som frågar CORS-begränsningar i tjänsten. Preliminära begäran behövs om metoden för begäran är en [enkel metod](https://www.w3.org/TR/cors/), vilket innebär att GET, HEAD eller POST.
* Den faktiska begäran som görs mot önskad resurs.

### <a name="preflight-request"></a>Preliminära begäran
Preliminära begäran frågorna CORS-begränsningarna som har upprättats för storage-tjänsten som ägare. Webbläsare (eller andra användaragent) skickar du en OPTIONS-begäran som innehåller begärandehuvuden, och det ursprungliga domän. Storage-tjänsten utvärderar åtgärden baserat på en förkonfigurerad uppsättning CORS-regler som anger vilka ursprungsdomäner och metodbegäranden begärandehuvuden kan anges för en faktiska begäran mot en lagringsresurs.

Om CORS har aktiverats för tjänsten och det finns en CORS-regel som matchar den preliminära begäran, tjänsten svarar med statuskod 200 (OK) och innehåller nödvändiga Access Control-huvuden i svaret.

Om CORS inte har aktiverats för tjänsten eller inga CORS-regler matchar preflight-begäran, kommer tjänsten svara med statuskod 403 (förbjudet).

Om OPTIONS-begäran inte innehåller nödvändiga CORS-huvuden (ursprung och Access-Control-begäran-Method rubriker), kommer tjänsten svara med statuskod 400 (felaktig begäran).

Observera att en begäran om preliminära utvärderas mot tjänsten (Blob, Queue och Table) och inte mot den begärda resursen. Ägare måste ha aktiverat CORS som en del av tjänsten kontoegenskaperna för begäran om att lyckas.

### <a name="actual-request"></a>Faktiska begäran
När preliminära förfrågan har godkänts och svaret returneras kan skickar webbläsaren den faktiska begäran mot till lagringsresursen. Webbläsaren kommer att neka den faktiska begäran omedelbart om preliminära begäran avvisas.

Den faktiska begäran behandlas som normala begäran mot lagringstjänsten. Förekomst av rubriken ursprung anger att begäran är en CORS-begäran och tjänsten kontrollerar matchande CORS-regler. Om en matchning hittas, Access Control-huvuden läggs till svaret och skickas tillbaka till klienten. Om en matchning hittas returneras inte CORS Access-Control-huvuden.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Aktivera CORS för Azure Storage-tjänster
CORS-regler är inställda på tjänstnivå, så du måste aktivera eller inaktivera CORS för varje tjänst (Blob, Queue och Table) separat. Som standard inaktiveras CORS för varje tjänst. Om du vill aktivera CORS, måste du ange egenskaper för lämplig tjänst som använder version 2013-08-15 eller senare, och Lägg till CORS-regler i Tjänstegenskaperna för. Mer information om hur du aktiverar eller inaktiverar CORS för en tjänst och hur du ställer in CORS-regler, se [ange egenskaper för Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx), och [ange Table Service Egenskaper för](https://msdn.microsoft.com/library/hh452240.aspx).

Här är ett exempel på en enda CORS-regel som angetts via en ange egenskaper för filtjänsten:

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

Varje element som ingår i CORS-regel beskrivs nedan:

* **AllowedOrigins**: Ursprungsdomäner som tillåts att göra en begäran mot lagringstjänsten via CORS. Den ursprungliga domänen är den domän som förfrågan kommer från. Observera att ursprunget måste vara en skiftlägeskänslig matchning med ursprung som användaren ålder skickar till tjänsten. Du kan också använda jokertecknet ”*” så att alla ursprungsdomäner att göra förfrågningar via CORS. I exemplet ovan domänerna [ http://www.contoso.com ](http://www.contoso.com) och [ http://www.fabrikam.com ](http://www.fabrikam.com) kan göra förfrågningar till tjänsten med hjälp av CORS.
* **AllowedMethods**: De metoder (HTTP-förfrågningsverb) som ursprungsdomänen kan använda för en CORS-förfrågan. I exemplet ovan tillåts endast PUT- och GET-begäranden.
* **AllowedHeaders**: Sidhuvuden för begäran som ursprungsdomänen kan ange vid CORS-förfrågan. I exemplet ovan får alla rubriker för arbetsflödesmetadata som börjar med x-ms-metadata, x-ms-meta-mål och x-ms-meta-abc. Observera att jokertecknet ”*” anger att alla rubrik som börjar med det angivna prefixet tillåts.
* **ExposedHeaders**: De svarshuvuden som kan skickas som svar på CORS-förfrågan och visas i webbläsaren för utfärdaren av förfrågan. I exemplet ovan instrueras webbläsaren att exponera alla rubrik som börjar med x-ms-metadata.
* **MaxAgeInSeconds**: Den längsta tid att en webbläsare får cachelagra den preliminära OPTIONS-begäran.

Azure storage-tjänster stöder konfiguration av återskrivningscachens sidhuvuden för både den **AllowedHeaders** och **ExposedHeaders** element. Du kan ange ett vanligt prefix till den kategorin för att tillåta en kategori med rubriker. Till exempel ange *x-ms-meta** som ett prefix huvud upprättar en regel som matchar alla rubriker som börjar med x-ms-metadata.

Följande begränsningar gäller för CORS-regler:

* Du kan ange upp till fem CORS-regler per lagringstjänst (Blob, tabell och kö).
* Den maximala storleken för alla CORS-regler inställningar på begäran, exklusive XML-taggar, bör inte överskrida 2 KB.
* Längden på en tillåtna sidhuvud, exponerade rubrik eller tillåtna ursprung får innehålla högst 256 tecken.
* Tillåtna huvuden och exponerade rubriker kan vara antingen:
  * Literal rubriker, där det exakta rubriknamnet anges, till exempel **x-ms-meta-bearbetas**. Högst 64 literal sidhuvuden kan anges i begäran.
  * Prefixet rubriker, där ett prefix på rubriken anges, till exempel ** x-ms-meta-data ***. Ange ett prefix i det här sättet tillåter eller visar alla rubriker som börjar med det angivna prefixet. Högst två sidhuvuden kan anges i begäran.
* De metoder (eller HTTP-verb) som anges i den **AllowedMethods** elementet måste uppfylla de metoder som stöds av Azure storage-tjänstens API: er. Metoder som stöds är DELETE, GET, HEAD, MERGE, POST, alternativ och PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Förstå utvärderingslogiken för CORS-regel
När en tjänst tar emot en begäran om preliminära eller verklig, utvärderas den begäran som baserat på CORS-regler som du har angett för tjänsten via åtgärden Ange egenskaper. CORS-reglerna utvärderas i den ordning som de har ställts in i begärandetexten för åtgärden Ange egenskaper för filtjänsten.

CORS-regler utvärderas enligt följande:

1. Först ursprungsdomänen för begäran kontrolleras mot de domäner som anges för den **AllowedOrigins** element. Om den ursprungliga domänen ingår i listan eller alla domäner tillåts med jokertecknet ”*”, regler utvärdering fortsätter. Om den ursprungliga domänen inte finns, misslyckas denna begäran.
2. Därefter metod (eller HTTP-verb) för begäran kontrolleras mot de metoder som anges i den **AllowedMethods** element. Om metoden ingår i listan, fortsätter regler utvärdering; annars misslyckas begäran.
3. Om begäran matchar en regel i sin ursprungliga domän och dess metod, väljs den regeln att bearbeta begäran och inga ytterligare regler utvärderas. Innan begäran kan fungera, men alla rubriker som anges på begäran kontrolleras mot de rubriker som anges i den **AllowedHeaders** element. Om de rubriker som skickas inte överensstämmer med tillåtna huvuden, misslyckas denna begäran.

Eftersom reglerna bearbetas i den ordning som de finns i begärandetexten, rekommenderar bästa praxis att du anger de mest restriktiva reglerna med avseende på ursprung först i listan så att dessa utvärderas först. Ange regler som är mindre restriktivt – till exempel en regel som tillåter alla ursprung – i slutet av listan.

### <a name="example--cors-rules-evaluation"></a>Exempel – CORS-regler utvärdering
I följande exempel visas en partiell förfrågans brödtext för en åtgärd för att ange CORS-regler för storage-tjänster. Se [ange egenskaper för Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx), och [ange tabellen tjänstegenskaper](https://msdn.microsoft.com/library/hh452240.aspx) för information om hur du skapar begäran.

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

Därefter ska du tänka på följande CORS-begäranden:

| Förfrågan |  |  | Svar |  |
| --- | --- | --- | --- | --- |
| **Metod** |**Ursprung** |**Rubriker för begäran** |**Regeln matchar** |**Resultatet** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Första regeln |Lyckades |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Andra regeln |Lyckades |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Andra regeln |Fel |

Den första begäran matchar den första regeln – den ursprungliga domänen matchar tillåtna ursprung, metoden matchar tillåtna metoder och rubriken matchar tillåtna huvuden – och därför lyckas.

Andra begäran matchar inte den första regeln eftersom metoden inte matchar de tillåtna metoderna. Den, men matchar den andra regeln, så att det lyckas.

Tredje begäran matchar den andra regeln i sin ursprungliga domän och metod, så att inga ytterligare regler utvärderas. Men den *huvudet för x-ms-client-request-id* tillåts inte av den andra regeln, så att begäran misslyckas trots semantiken för tredje regeln skulle användas till att lyckas.

> [!NOTE]
> Även om det här exemplet visar en mindre begränsande regel innan en mer begränsande, i allmänhet är det bästa sättet att visa de mest restriktiva reglerna först.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Förstå hur rubriken kan variera har angetts
Den *variera* rubriken är en standard HTTP/1.1-rubrik som består av en uppsättning begäran huvudfält som rekommenderar webbläsare eller användaren agent om de kriterier som valdes av servern för att bearbeta begäran. Den *variera* huvud används främst för cachelagring av proxyservrar, webbläsare och CDN, som använder den för att fastställa hur svaret ska cachelagras. Mer information finns i specifikationen för den [variera rubrik](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

När webbläsaren eller en annan användaragenten cachelagrar svaret från en CORS-förfrågan, cachelagras ursprungsdomänen som tillåtna ursprung. När en domän skickar samma begäran om en resurs för lagring när cachen är aktiv, hämtar användaragenten cachelagrade ursprungsdomänen. Den andra domänen matchar inte den cachelagra domänen, så misslyckas med begäran när det annars skulle lyckas. I vissa fall kan Azure Storage anger variera rubriken till **ursprung** att instruera användaragenten att skicka efterföljande CORS-begäran till tjänsten när du begär domänen skiljer sig från det cachelagrade ursprunget.

Azure Storage-uppsättningar på *variera* sidhuvud till **ursprung** för faktiska GET/HEAD-begäranden i följande fall:

* När begäran ursprunget exakt matchar den tillåtna ursprung som definieras av en CORS-regel. För att vara en exakt matchning CORS-regel inte innehåller jokertecken ' * ' tecken.
* Det finns ingen regel som matchar begäran ursprung, men CORS är aktiverat för storage-tjänsten.

Svaret anger att alla ursprung är tillåtna och agenten användarcachen tillåter efterföljande förfrågningar från alla ursprungsdomänen när cachen är aktiv i fall där en GET/HEAD-begäran matchar en CORS-regel som tillåter alla ursprung.

Observera att för begäranden med andra metoder än GET/HEAD, lagringstjänsterna ställer inte in rubriken kan variera eftersom svar på dessa metoder inte cachelagras av användaragenter.

Följande tabell visar hur Azure storage kommer att besvara GET/HEAD-begäranden baserat på de tidigare nämnda fall:

| Förfrågan | Kontoinställningen och resultatet av utvärderingen av distributionsregeln |  |  | Svar |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Ursprung rubriken på begäran** |**CORS-regler som angetts för den här tjänsten** |**Det finns matchande regel som tillåter alla origins(*)** |**Matchande regel som finns för exakta ursprung matchning** |**Svaret innehåller variera rubrikuppsättning till ursprung** |**Svaret innehåller Access-Control-tillåts-ursprung ”: *”** |**Svaret innehåller Access-Control-exponeras-huvuden** |
| Nej |Nej |Nej |Nej |Nej |Nej |Nej |
| Nej |Ja |Nej |Nej |Ja |Nej |Nej |
| Nej |Ja |Ja |Nej |Nej |Ja |Ja |
| Ja |Nej |Nej |Nej |Nej |Nej |Nej |
| Ja |Ja |Nej |Ja |Ja |Nej |Ja |
| Ja |Ja |Nej |Nej |Ja |Nej |Nej |
| Ja |Ja |Ja |Nej |Nej |Ja |Ja |

## <a name="billing-for-cors-requests"></a>Faktureringen för CORS-förfrågningar
Lyckad preflight-begäranden, faktureras om du har aktiverat CORS om storage-tjänster för ditt konto (genom att anropa [ange egenskaper för Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx), eller [Ange egenskaper för tabellen](https://msdn.microsoft.com/library/hh452240.aspx)). Överväg att ställa in för att minimera kostnader, den **MaxAgeInSeconds** element i din CORS-regler till ett stort värde så att användaragenten cachelagrar begäran.

Misslyckade preliminära förfrågningar faktureras inte.

## <a name="next-steps"></a>Nästa steg
[Ange egenskaper för Blob Service](https://msdn.microsoft.com/library/hh452235.aspx)

[Ange egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452232.aspx)

[Ange egenskaper för tabell](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C Cross-Origin Resource Sharing specifikation](https://www.w3.org/TR/cors/)

