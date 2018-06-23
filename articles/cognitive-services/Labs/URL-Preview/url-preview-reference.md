---
title: Projektet URL Preview referens - kognitiva Microsoft-tjänster | Microsoft Docs
description: Referens för Project URL förhandsgranskningsslutpunkten.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: adc2f83f703e740e40d9ba4fd3ed08ba429e5d97
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354180"
---
# <a name="project-url-preview-v7-reference"></a>Project URL Preview v7-referens

URL: en Preview stöder kort beskrivning av webbresurser för blogginlägg, forum diskussioner, förhandsgranskningssidor, osv.  URL: en kan vara vilken typ av Internet-resurs: webbsida, nyheter, image eller video. Frågan måste vara en absolut URL med en HTTP- eller https-schema; relativa URL: er eller andra system som ftp: / / stöds inte.

Program som använder URL Preview skicka webbförfrågningar till slutpunkten med en URL för förhandsgranskning i en frågeparameter.  Begäran måste innehålla den *Ocp-Apim-prenumeration-nyckeln* huvud.   

JSON-svar kan parsas preview-information: namn, beskrivning av resursen, *isFamilyFriendly*, och länkar som ger åtkomst till en representativ bild och fullständig resursen.

Endast data från URL: en förhandsgranskning måste du använda för att visa förhandsgranskning kodavsnitt och miniatyrbilder som är länkade till sina källplatser i initierats av slutanvändaren URL delning på sociala medier, chatt bot eller liknande erbjudanden. Du måste inte kopiera, spara eller cachelagra alla data som du får från Project URL Preview. Du måste respektera alla begäranden om att inaktivera förhandsversioner som du kan få från webbplatsen eller innehållsägare.

## <a name="endpoint"></a>Slutpunkt
Skicka en begäran om du vill begära URL förhandsgranska resultaten till följande slutpunkt. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt GET: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

Begäran måste använda HTTPS-protokollet och inkluderar följande frågeparameter:

 q - frågan som identifierar URL: en för förhandsgranskning 

Följande avsnitt innehåller teknisk information om svaret objekten, frågeparametrar och huvuden som påverkar sökresultatet. 
  
Information om huvuden som ska tas med begäranden finns [huvuden](#headers).  
  
Information om frågeparametrar förfrågningar måste innehålla finns [fråga parametrar](#query-parameters).  
  
För information om JSON objekt att svaret innehåller, se [svar objekt](#response-objects).

Maximal URL-längd är 2 048 tecken. För att säkerställa att URL-längd inte överskrider gränsen, ska den maximala längden på din frågeparametrar vara mindre än 1 500 tecken. Om URL: en överskrider 2 048 tecken, returnerar servern 404 hittades inte.  

Information om tillåtna användning och visa resultat finns [använder och visa krav](use-display-requirements.md). 

> [!NOTE]
> Vissa begärandehuvuden som är viktiga för andra sökningar API: er påverkar inte URL-förhandsgranskning
> - Pragma – anroparen inte har kontroll över om URL: en förhandsgranskning använder cache
> - Användaragent – för tillfället Url Preview API inte ger olika svar för samtal som härrör från PC, bärbar dator eller mobil.

> Dessutom vissa parametrar är inte för närvarande meningsfulla för URL: en förhandsgranskning API men får användas i framtiden för förbättrad globalisering. 
 
## <a name="headers"></a>Sidhuvuden  
Följande är sidhuvuden som en förfrågan och svar kan innehålla.  
  
|Sidhuvud|Beskrivning|  
|------------|-----------------|   
|<a name="market" />BingAPIs marknaden|Svarsrubrik.<br /><br /> Marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Till exempel en-US.|  
|<a name="traceid" />BingAPIs TraceId|Svarsrubrik.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår avbilda detta ID. Om det inte går att identifiera och lösa problemet, inkludera detta ID tillsammans med den information som du anger supportgruppen.|  
|<a name="subscriptionkey" />OCP-Apim-prenumeration-nyckel|Nödvändiga begärandehuvudet.<br /><br /> Nyckeln prenumeration som du fick när du registrerat dig för den här tjänsten i [kognitiva Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Valfria huvud för begäran och svar.<br /><br /> Bing använder den här rubriken för att ge användarna en konsekvent beteende i Bing-API-anrop. Bing flygningar ofta nya funktioner och förbättringar och klient-ID används som en nyckel för att tilldela trafik på olika flygplan. Om du inte använder samma klient-ID för en användare över flera förfrågningar, kan sedan Bing tilldela användaren till flera motstridiga flygplan. Tilldelas till flera motstridiga flygplan kan leda till ett inkonsekvent användarupplevelsen. Om andra begäran har en annan svarta tilldelning än först, till exempel vara upplevelsen oväntat. Bing kan också använda klient-ID för att skräddarsy Webbresultat klienten ID Sök tidigare, vilket ger en bättre upplevelse för användaren.<br /><br /> Bing använder också den här rubriken för att förbättra resultatet rangordning genom att analysera aktiviteten som genererats av ett klient-ID. Förbättringar av betydelse hjälpa med bättre resultat som levereras med Bing-API: er och i sin tur aktiverar högre klicka igenom priser för API-klienten.<br /><br />Följande är de grundläggande användningsregler som gäller för det här sidhuvudet.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing skapas klient-ID.<br /><br/>Om du inte använder det här huvudet i begäran Bing genererar ett ID och returnerar i X-MSEdge-ClientID-Svarsrubrik. Den enda gången som du inte bör inkludera det här huvudet i en begäran är första gången användaren använder din app på enheten.<br /><br/></li><li>Använda klient-ID för varje begäran i Bing-API som gör att din app för denna användare på enheten.<br /><br/></li><li>**Obs:** måste du se till att detta klient-ID inte är linkable till alla authenticatable kontoinformation.</li><br/><li>Spara klient-ID. För att bevara ID i en webbläsaren att använda en beständig HTTP-cookie för detta ID används i alla sessioner. Använd inte en sessions-cookie. För andra appar, till exempel mobilappar, använder du enhetens beständig lagring för att bevara ID.<br /><br/>Nästa gång användaren använder din app på enheten, hämta klient-ID som du sparade.</li></ul><br /> **Obs:** Bing svar kanske eller kanske inte med det här sidhuvudet. Om svaret innehåller det här sidhuvudet, avbilda klient-ID och använda det för alla efterföljande Bing-förfrågningar för användare på enheten.<br /><br /> **Obs:** om du inkluderar X-MSEdge-ClientID, får inte innehålla cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfria begärandehuvudet.<br /><br /> Klientenheten IPv4 eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder Platsinformationen för att fastställa säker sökbeteendet.<br /><br />  Obfuscate inte adressen (till exempel genom att ändra den sista oktetten 0). Obfuscating adress resultaten på den plats som inte var som helst nära enhetens verkliga plats, vilket kan medföra Bing betjänar felaktiga resultat.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Frågeparametrar  
Begäran kan innehålla följande Frågeparametrar. Finns den nödvändiga kolumnen för obligatoriska parametrar. Du måste URL koda Frågeparametrar. Frågan måste vara en absolut URL med en HTTP- eller https-schema; Vi stöder inte relativa URL: er eller andra system som ftp: / /
  
  
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Marknaden var resultatet ska hämtas. <br /><br />En lista över möjliga marknaden värden, se [marknaden koder](#market-codes).<br /><br /> **Obs:** API för Certifikatwebbadress Preview stöder för närvarande endast USA geografi och engelska.<br /><br />|Sträng|Ja|  
|<a name="query" />Q|URL: en för förhandsgranskning|Sträng|Ja|  
|<a name="responseformat" />responseFormat|Medietypen för att använda för svaret. Följande är möjliga skiftlägeskänsliga värden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Standardvärdet är JSON. För information om JSON objekt att svaret innehåller, se [svar objekt](#response-objects).<br /><br />  Om du anger JsonLd innehåller svarstexten LD JSON-objekt som innehåller sökresultaten. Information om JSON-LD finns [JSON-LD](http://json-ld.org/).|Sträng|Nej|
|<a name="safesearch"/>säker sökning|Ogiltigt innehåll för vuxna eller oäkta innehåll blockeras med felkoden 400, och *isFamilyFriendly* flaggan returneras inte. <p>För juridiska vuxet innehåll, görs nedan. Statuskoden returnerar 200, och *isFamilyFriendly* -flaggan inställd på false.<ul><li>säker sökning = strict: rubrik, beskrivning, URL och image kommer inte att returneras.</li><li>säker sökning = måttlig; Hämta rubrik, URL och beskrivning, men inte beskrivande avbildningen.</li><li>säker sökning = off; Hämta alla svar objekt/element – namn, URL, beskrivning, bild.</li></ul> |Sträng|Krävs inte. </br> Standardinställningen är säker sökning = strikt.| 

## <a name="response-objects"></a>Svaret objekt  
Schemat för svar är antingen en [webbsida] eller ErrorResponse som webb-API för sökning. Om begäran inte det översta objektet är den [ErrorResponse](#errorresponse) objekt.


|Objekt|Beskrivning|  
|------------|-----------------|  
|[Webbsida](#webpage)|Övre nivå JSON-objekt som innehåller attribut för förhandsgranskning.|  

  
### <a name="error"></a>Fel  
Definierar de fel som inträffade.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Koden|Felkoden som identifierar kategori av fel. En lista över möjliga koder finns [felkoder](#error-codes).|Sträng|  
|<a name="error-message" />meddelande|En beskrivning av felet.|Sträng|  
|<a name="error-moredetails" />moreDetails|En beskrivning som ger ytterligare information om felet.|Sträng|  
|<a name="error-parameter" />Parametern|Frågeparametern i begäran som orsakade felet.|Sträng|  
|<a name="error-subcode" />Delkoden|Den felkod som identifierar felet. Till exempel om `code` är InvalidRequest, `subCode` kanske ParameterInvalid eller ParameterInvalidValue. |Sträng|  
|<a name="error-value" />värdet|Parametern fråga-värde som inte är giltig.|Sträng|  
  

### <a name="errorresponse"></a>ErrorResponse  
Det huvudobjekt som svaret innehåller när begäran misslyckas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Tips för typen.|Sträng|  
|<a name="errors" />Fel|En lista med fel som beskriver de orsakerna till begäran inte.|[Fel](#error)]|   
  

### <a name="webpage"></a>Webbsida  
Definierar information om en webbsida i förhandsgranskningen.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|
|namn|En rubrik, inte nödvändigtvis HTML-rubrik|Sträng|
|url|Den URL som faktiskt har crawlats (begäran kan ha följt omdirigeringar)|Sträng|  
|description|Kort beskrivning av den sidan och innehåll|Sträng|  
|isFamilyFriendly|Mest korrekta för objekt i web-index. realtid hämtar gör denna identifiering utifrån enbart URL och inte sidinnehåll|boolesk|
|primaryImageOfPage/contentUrl|URL till en representativ bild ska ingå i förhandsversionen|Sträng| 


### <a name="identifiable"></a>Identifierbar
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|id|Resurs-ID|Sträng|
 

## <a name="error-codes"></a>Felkoder

Följande är möjliga http-statuskoder som returnerar en begäran.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|200|Lyckades.|  
|400|En av frågeparametrarna är saknas eller är inte giltigt.| 
|400|ServerError, underkod ResourceError: Det gick inte att nå begärd URL|
|400|ServerError, underkod ResourceError: den begärda URL: en returnerade inte koden (inklusive om den returnerade HTTP 404)|
|400|InvalidRequest, underkod blockerad: den begärda URL: en kan innehålla innehåll för vuxna och som har blockerats| 
|401|Nyckeln prenumeration saknas eller är inte giltig.|  
|403|Användaren har autentiserats (till exempel de som används en giltig prenumeration nyckel), men de har inte behörighet till den begärda resursen.<br /><br /> Bing kan också returnera denna status om anroparen överskridit sina frågor per månad kvoten.|  
|410|Begäran används HTTP i stället för HTTPS-protokollet. HTTPS är det enda protokollet som stöds.|  
|429|Anroparen har överskridit sina frågor per andra kvoten.|  
|500|Oväntat serverfel.|

Om begäran inte, svaret innehåller ett [ErrorResponse](#errorresponse) objekt som innehåller en lista över [fel](#error) objekt som beskriver vad som orsakade felet. Om felet är relaterat till en parameter i `parameter` fältet identifierar parametern som är problemet. Och om felet är relaterat till ett parametervärde den `value` identifierar det värde som inte är giltigt för fältet.

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

Följande är möjliga fel kod och underordnade fel kodvärden.

|Kod|Delkoden|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskoden 400.<br/><br/>Om du använder HTTP-protokollet i stället för HTTPS Bing returnerar HttpNotAllowed och HTTP-statuskoden är 410.
|RateLimitExceeded|Inga underordnade koder|Bing returnerar RateLimitExceeded när du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvoten.<br/><br/>Om du överskrider QPS Bing returnerar HTTP-statuskod 429 och om du överskrider QPM Bing returnerar 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan verifiera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller nyckeln prenumerationen är inte giltigt.<br/><br/>Redundans inträffar om du anger fler än en autentiseringsmetod.<br/><br/>Om felet InvalidAuthorization är 401 HTTP-statuskoden.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om nyckeln för prenumerationen har inaktiverats eller har upphört att gälla. <br/><br/>Om felet InsufficientAuthorization är 403 HTTP-statuskoden.

## <a name="next-steps"></a>Nästa steg
- [C#-Snabbstart](csharp.md)
- [Java-Snabbstart](java-quickstart.md)
- [JavaScript-Snabbstart](javascript.md)
- [Noden Snabbstart](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)

