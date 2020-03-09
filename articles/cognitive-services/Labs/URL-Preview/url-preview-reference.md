---
title: Project URL för hands versions referens
titlesuffix: Azure Cognitive Services
description: Referens för för hands versionen av Project URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393952"
---
# <a name="project-url-preview-v7-reference"></a>V7 referens för för hands version av Project URL

URL-förhands granskning stöder korta beskrivningar av webb resurser för blogg inlägg, forum diskussioner, förhands gransknings sidor osv. URL: en kan vara vilken typ av Internet resurs som helst: webb sida, nyheter, bild eller video. Frågan måste vara en absolut URL med ett http-eller https-schema. relativa URL: er eller andra scheman som ftp://stöds inte.

Program som använder URL-förhands granskning skickar webbegäranden till slut punkten med en URL som ska förhandsgranskas i en frågeparameter. Begäran måste innehålla rubriken *OCP-APIM-Subscription-Key* .

JSON-svaret kan parsas för förhands gransknings informationen: namn, beskrivning av resursen, *isFamilyFriendly*och länkar som ger åtkomst till en representativ avbildning och till den fullständiga resursen online.

Du måste endast använda data från för hands versionen av URL: en för att Visa förhands granskningar och miniatyr bilder som är länkade till käll platserna, i slutanvändarens URL-delning på sociala medier, chat-robot eller liknande erbjudanden. Du får inte kopiera, lagra eller cachelagra data som du tar emot från för hands versionen av Project URL. Du måste respektera alla begär Anden om du vill inaktivera för hands versionerna som du kan ta emot från webbplats eller innehålls ägare.

## <a name="endpoint"></a>Endpoint
Skicka en begäran till följande slut punkt om du vill begära förhands gransknings resultat för URL. Använd sidhuvudena och URL-parametrarna för att definiera ytterligare specifikationer.

Slut punkt GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

Begäran måste använda HTTPS-protokollet och inkludera följande frågeparameter:

q – frågan som identifierar URL: en som ska förhandsgranskas

Följande avsnitt innehåller teknisk information om svars objekt, frågeparametrar och rubriker som påverkar Sök resultaten.

Information om rubriker som begär Anden ska inkludera finns i [sidhuvud](#headers).

Information om frågeparametrar som begär Anden ska inkludera finns i [frågeparametrar](#query-parameters).

Information om JSON-objekten som svaret innehåller finns i [Response Objects](#response-objects).

Maximal längd på fråge-URL är 2 048 tecken. För att se till att URL-längden inte överskrider gränsen ska den maximala längden för dina frågeparametrar innehålla färre än 1 500 tecken. Om URL: en överskrider 2 048 tecken returnerar servern 404 som inte hittas.

Information om tillåten användning och visning av resultat finns i [använda och Visa krav](use-display-requirements.md).

> [!NOTE]
> Vissa begärandehuvuden som är meningsfulla för andra Sök-API: er påverkar inte URL-förhands granskning
> - Pragma – anroparen har inte kontroll över om förhands granskning av URL använder cache
> - User-Agent – för tillfället tillhandahåller URL för för hands versions-API inte olika svar för anrop från PC, laptop eller Mobile.
> 
> Dessutom är vissa parametrar för närvarande inte meningsfulla för URL för för hands versionen, men kan användas i framtiden för förbättrad globalisering.

## <a name="headers"></a>sidhuvud
Följande är huvuden som en begäran och ett svar kan innehålla.

|Sidfot|Beteckning|
|------------|-----------------|
|<a name="market" />BingAPIs-marknad|Svars huvud.<br /><br /> Marknaden som används av begäran. Formatet är \<languageCode\>-\<countryCode\>. Till exempel en-US.|
|<a name="traceid" />BingAPIs-TraceId|Svars huvud.<br /><br /> ID för logg posten som innehåller information om begäran. Avbilda detta ID när ett fel uppstår. Om du inte kan fastställa och lösa problemet inkluderar du detta ID tillsammans med den andra information som du tillhandahåller support teamet för.|
|<a name="subscriptionkey" />OCP-APIM-Subscription-Key|Begär ande huvud för begäran.<br /><br /> Den prenumerations nyckel som du fick när du registrerade dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Valfritt sidhuvud för begäran och svar.<br /><br /> Bing använder den här rubriken för att ge användarna konsekventa beteenden i Bing API-anrop. Bing använder ofta nya funktioner och förbättringar och använder klient-ID: t som en nyckel för att tilldela trafik på olika flygningar. Om du inte använder samma klient-ID för en användare över flera begär Anden, kan Bing tilldela användaren till flera flygningar i konflikt. Att tilldelas flera flygningar i konflikt kan leda till inkonsekvent användar upplevelse. Om den andra begäran till exempel har en annan flyg tilldelning än den första, kan upplevelsen vara oväntad. Bing kan också använda klient-ID: t för att skräddarsy webb resultat till klient-ID: t för Sök historiken, vilket ger en rikare upplevelse för användaren.<br /><br /> Bing använder även den här rubriken för att hjälpa till att förbättra resultat genom att analysera den aktivitet som genereras av ett klient-ID. De viktiga förbättringarna bidrar till bättre kvalitet på resultaten som levereras av Bing-API: er och ger i sin tur högre hastighet för klickningar för API-konsumenten.<br /><br />Följande är de grundläggande användnings regler som gäller för den här rubriken.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing-genererat klient-ID.<br /><br/>Om du inte tar med den här rubriken i begäran genererar Bing ett ID och returnerar det i svars huvudet X-MSEdge-ClientID. Den enda tiden du inte bör ta med den här rubriken i en begäran är första gången användaren använder appen på den enheten.<br /><br/></li><li>Använd klient-ID för varje Bing API-begäran som din app gör för den här användaren på enheten.<br /><br/></li><li>**Uppmärksamhet:** Du måste se till att det här klient-ID: t inte kan länka till information som kan autentiseras för användar konton.</li><br/><li>Behåll klient-ID: t. Om du vill behålla ID: t i en webbapp använder du en beständig HTTP-cookie för att se till att ID: t används i alla sessioner. Använd inte en sessions-cookie. För andra appar, till exempel Mobile Apps, använder du enhetens beständiga lagring för att bevara ID: t.<br /><br/>Nästa gång användaren använder appen på enheten hämtar du det klient-ID som du har sparat.</li></ul><br /> **Obs:** Bing-svar kan eventuellt innehålla rubriken. Om svaret innehåller den här rubriken fångar du klient-ID och använder det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs:** Om du inkluderar X-MSEdge-ClientID, får du inte inkludera cookies i begäran.|
|<a name="clientip" />X-MSEdge-ClientIP|Valfritt huvud för begäran.<br /><br /> Klient enhetens IPv4-eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder plats informationen för att fastställa säker Sök funktion.<br /><br /> Obfuscate inte adressen (till exempel genom att ändra den sista oktetten till 0). Dölja-adressen leder till att platsen inte är överallt nära enhetens faktiska plats, vilket kan leda till att Bing ger felaktiga resultat.|

## <a name="query-parameters"></a>Frågeparametrar
Begäran kan innehålla följande frågeparametrar. Se kolumnen obligatorisk för obligatoriska parametrar. Du måste URL-koda parametrarna för frågan. Frågan måste vara en absolut URL med ett http-eller https-schema. Vi stöder inte relativa URL: er eller andra scheman som ftp://

|Namn|Värde|Bastyp|kunna|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|Marknaden där resultatet kommer från. <br /><br />En lista över möjliga marknads värden finns i marknads koder.<br /><br /> **Obs:** URL: en för hands versions-API stöder för närvarande endast amerikanska geografi-och Engelskt språk.<br /><br />|Nollängd|Ja|
|<a name="query" />q|URL till för hands version|Nollängd|Ja|
|<a name="responseformat" />responseFormat|Den medietyp som ska användas för svaret. Följande är möjliga Skift läges känsliga värden.<br /><ul><li>UTGÖR</li><li>JSONLD</li></ul><br /> Standardvärdet är JSON. Information om JSON-objekt som svaret innehåller finns i [Response Objects](#response-objects).<br /><br />Om du anger JsonLd innehåller svars texten JSON-LD objekt som innehåller Sök resultaten. Information om JSON-LD finns i [JSON-LD](https://json-ld.org/).|Nollängd|Nej|
|<a name="safesearch"/>safeSearch|Otillåtet innehåll i vuxna, eller pirattillverkade innehåll, blockeras med felkod 400 och flaggan *isFamilyFriendly* returneras inte. <p>För innehåll som är olämpligt för barn, nedan är beteendet. Status koden returnerar 200 och *isFamilyFriendly* -flaggan har angetts till false.<ul><li>safeSearch = Strict: rubrik, beskrivning, URL och bild kommer inte att returneras.</li><li>safeSearch = måttlig; Hämta rubrik, URL och beskrivning, men inte en beskrivande bild.</li><li>safeSearch = off; Hämta alla svars objekt/element – rubrik, URL, beskrivning och bild.</li></ul> |Nollängd|Krävs inte. </br> Standardvärdet är safeSearch = Strict.|

## <a name="response-objects"></a>Svars objekt
Svars schema är antingen en [webb sida] eller ErrorResponse, som i Webbsökning-API: et. Om begäran Miss lyckas är objektet på den översta nivån [ErrorResponse](#errorresponse) -objektet.

|Jobbobjektet|Beteckning|
|------------|-----------------|
|[Historik](#webpage)|JSON-objekt på översta nivån som innehåller attribut för för hands versionen.|

### <a name="error"></a>Fels
Definierar det fel som inträffat.

|Brevpost|Beteckning|Bastyp|
|-------------|-----------------|----------|
|<a name="error-code" />kod|Felkoden som identifierar fel kategorin. En lista över möjliga koder finns i [felkoder](#error-codes).|Nollängd|
|<a name="error-message" />meddelande|En beskrivning av felet.|Nollängd|
|<a name="error-moredetails" />moreDetails|En beskrivning som ger ytterligare information om felet.|Nollängd|
|<a name="error-parameter" />parameter|Frågeparametern i den begäran som orsakade felet.|Nollängd|
|<a name="error-subcode" />under kod|Felkoden som identifierar felet. Om `code` till exempel är InvalidRequest kan `subCode` vara ParameterInvalid eller ParameterInvalidValue. |Nollängd|
|<a name="error-value" />värde|Värdet för frågeparametern är inte giltigt.|Nollängd|

### <a name="errorresponse"></a>ErrorResponse
Objektet på den översta nivån som svaret innehåller när begäran Miss lyckas.

|Namn|Värde|Bastyp|
|----------|-----------|----------|
|_type|Typ tips.|Nollängd|
|<a name="errors" />fel|En lista med fel som beskriver orsakerna till att begäran misslyckades.|[Fel](#error)[]|

### <a name="webpage"></a>Historik
Definierar information om en webb sida som förhands granskning.

|Namn|Värde|Bastyp|
|----------|-----------|----------|
|Namn|Sid rubriken, inte nödvändigt vis HTML-titeln|Nollängd|
|adresser|Den URL som faktiskt crawlades (begäran kan ha följt omdirigeringar)|Nollängd|
|Beteckning|Kort beskrivning av sidan och innehållet|Nollängd|
|isFamilyFriendly|Mest exakt för objekt i webb indexet; Real tids hämtningar utför den här identifieringen enbart utifrån URL: en och inte sid innehållet|booleskt|
|primaryImageOfPage/contentUrl|URL till en representativ bild som ska tas med i förhands granskningen|Nollängd|

### <a name="identifiable"></a>Särskilj
|Namn|Värde|Bastyp|
|-------------|-----------------|----------|
|identitet|Ett resurs-ID|Nollängd|

## <a name="error-codes"></a>Felkoder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.

|Status kod|Beteckning|
|-----------------|-----------------|
|200|Resultatet.|
|400|En av frågeparametrar saknas eller är ogiltig.|
|400|ServerError, under kod ResourceError: den begärda URL: en kunde inte nås|
|400|ServerError, under kod ResourceError: den begärda URL: en returnerade inte någon lyckad kod (inklusive om den returnerade HTTP 404)|
|400|InvalidRequest, under kod blockerad: den begärda URL: en kan innehålla innehåll som inte är vuxna och blockerades|
|401|Prenumerations nyckeln saknas eller är ogiltig.|
|403|Användaren är autentiserad (t. ex. att de använde en giltig prenumerations nyckel) men de har inte behörighet till den begärda resursen.<br /><br /> Bing kan också returnera denna status om anroparen överskred sina frågor per månads kvot.|
|410|Begäran använde HTTP i stället för HTTPS-protokollet. HTTPS är det enda protokoll som stöds.|
|429|Anroparen överskred sina frågor per sekund kvot.|
|500|Oväntat Server fel.|

Om begäran Miss lyckas innehåller svaret ett [ErrorResponse](#errorresponse) -objekt som innehåller en lista med [fel](#error) objekt som beskriver vad som orsakade felet. Om felet är relaterat till en parameter, identifierar `parameter` fältet den parameter som är problemet. Om felet är relaterat till ett parameter värde identifierar `value` fältet det värde som inte är giltigt.

```json
{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidRequest",
      "subCode": "ParameterMissing",
      "message": "Required parameter is missing.",
      "parameter": "q"
    }
  ]
}

{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidAuthorization",
      "subCode": "AuthorizationMissing",
      "message": "Authorization is required.",
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Följande är möjliga felkoder och underordnade fel kod värden.

|Rikt|Under kod|Beteckning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP-statuskod är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockeras|Bing returnerar InvalidRequest när någon del av begäran är ogiltig. Till exempel saknas en obligatorisk parameter eller också är ett parameter värde ogiltigt.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue är HTTP-status koden 400.<br/><br/>Om du använder HTTP-protokollet i stället för HTTPS returnerar Bing HttpNotAllowed och HTTP-statuskod är 410.
|RateLimitExceeded|Inga under koder|Bing returnerar RateLimitExceeded varje gång du överskrider dina frågor per sekund (frågor per sekund) eller frågor per månad (QPM)-kvot.<br/><br/>Om du överskrider frågor per sekund returnerar Bing HTTP-statuskod 429 och om du överskrider QPM returnerar Bing 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel saknas `Ocp-Apim-Subscription-Key` huvud eller så är prenumerations nyckeln inte giltig.<br/><br/>Redundans inträffar om du anger fler än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization är HTTP-status koden 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerations nyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization är HTTP-status koden 403.

## <a name="next-steps"></a>Nästa steg
- [C#Start](csharp.md)
- [Java snabb start](java-quickstart.md)
- [Snabb start för Java Script](javascript.md)
- [Snabb start för nod](node-quickstart.md)
- [Python-snabb start](python-quickstart.md)
