---
title: Projektet URL-förhandsgranskning referens – Microsoft Cognitive Services | Microsoft Docs
description: Referens för projektet URL-förhandsgranskning-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 46c011d62b6ae51f5f7d292345e6ece0e27a8541
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865883"
---
# <a name="project-url-preview-v7-reference"></a>Project URL-förhandsgranskning v7-referens

URL-förhandsgranskning har stöd för korta beskrivningar av webbresurser för blogginlägg, forumdiskussioner, förhandsgranskningssidor, osv.  URL: en kan vara valfri Internet-resursen: webbsida, nyheter, bild eller video. Frågan måste vara en absolut URL med en http eller https-schema; relativa URL: er eller andra scheman som ftp: / / stöds inte.

Program som använder URL-förhandsgranskning skickar webbförfrågningar till slutpunkten med en URL för att förhandsgranska i en frågeparameter.  Begäran måste innehålla den *Ocp-Apim-Subscription-Key* rubrik.   

JSON-svar kan parsas för informationen om förhandsversion: namn, beskrivning av resursen, *isFamilyFriendly*, och länkar som ger åtkomst till en representativ avbildning och att den fullständiga resursen.

Du måste använda endast data från URL-förhandsgranskning för att visa förhandsgranskning kodfragment och miniatyrbilder som är länkade till sina källplatser i slutet användarinitierad URL delning på sociala medier, chattrobot eller liknande erbjudanden. Du måste inte kopiera, lagra eller cachelagra alla data som du får från URL-förhandsgranskning för projektet. Du måste respektera ett begärande om att inaktivera förhandsversioner som kan visas från webbplats eller innehållsägare.

## <a name="endpoint"></a>Slutpunkt
Om du vill begära URL-förhandsgranskning resultat, skicka en begäran till följande slutpunkt. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt GET: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

Begäran måste använda HTTPS-protokollet och omfattar följande Frågeparametern:

 frågor och - frågan som identifierar URL: en för att förhandsgranska 

Följande avsnitt innehåller teknisk information om svarsobjekt, frågeparametrar och rubriker som påverkar sökresultaten. 
  
Läs om hur rubriker som förfrågningar ska innehålla [rubriker](#headers).  
  
Läs om hur Frågeparametrar som förfrågningar ska innehålla [frågeparametrar](#query-parameters).  
  
Information om JSON-objekt att svaret innehåller, finns i [svarsobjekten](#response-objects).

Maximal URL-längd är 2 048 tecken. För att säkerställa att din URL-längd inte överskrider gränsen, ska den maximala längden på dina frågeparametrar vara mindre än 1 500 tecken. Om URL: en överskrider 2 048 tecken, returnerar servern 404 hittades inte.  

Information om tillåtna användning och visning av resultat finns i [använder och visa kraven](use-display-requirements.md). 

> [!NOTE]
> Vissa begärandehuvuden som är viktiga för andra search API: er påverkar inte URL-förhandsgranskning
> - Pragma – anroparen har ingen kontroll över om URL-förhandsgranskning använder cache
> - Användaragent – för tillfället URL: en förhandsversion av API: et tillhandahåller inte olika svar för anrop som härrör från datorn, bärbar dator eller mobil.

> Dessutom vissa parametrar är inte för närvarande beskrivande för URL: en förhandsversion API, men kan användas i framtiden för förbättrad globalisering. 
 
## <a name="headers"></a>Sidhuvuden  
Följande är de rubriker som en begäran och svaret kan innehålla.  
  
|Sidhuvud|Beskrivning|  
|------------|-----------------|   
|<a name="market" />BingAPIs marknad|Svarshuvudet.<br /><br /> På marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Till exempel en-US.|  
|<a name="traceid" />BingAPIs TraceId|Svarshuvudet.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår, avbilda detta ID. Om det inte går att fastställa och lösa problemet, kan du ange följande ID tillsammans med annan information som du anger supporten.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Nödvändiga begärandehuvudet.<br /><br /> Prenumerationsnyckel som du fick när du registrerat dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|Valfria rubriken för begäran och svar.<br /><br /> Bing använder den här rubriken för att ge användarna konsekvent beteende i Bing API-anrop. Bing flygningar ofta nya funktioner och förbättringar och klient-ID används som en nyckel för att tilldela trafik på olika flyg. Om du inte använder samma klient-ID för en användare över flera förfrågningar, kan sedan Bing tilldela användaren till flera motstridiga flyg. Tilldelas till flera motstridiga flyg kan leda till en inkonsekvent användarupplevelse. Om andra begäran har en annan flygning uppdrag än först, till exempel vara upplevelsen oväntat. Bing kan också använda klient-ID för att skräddarsy Webbresultat till den klienten-ID: n sökhistorik, vilket ger en rikare upplevelse för användaren.<br /><br /> Bing använder också den här rubriken för att förbättra resultatet rangordning genom att analysera aktiviteten som genererats av en klient-ID. Förbättringar för relevans hjälpa med bättre resultat som levereras av Bing-API: er och i sin tur aktiverar högre klicka igenom priser för API-konsument.<br /><br />Följande är de grundläggande användningsregler som gäller för den här rubriken.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing skapas klient-ID.<br /><br/>Om du inte använder den här rubriken i begäran Bing genererar ett ID och returnerar det i rubriken X-MSEdge-ClientID. Den enda gången som du inte får innehålla den här rubriken i en begäran är första gången du använder din app på enheten.<br /><br/></li><li>Använda klient-ID för varje API för Bing-begäran där din app för den här användaren på enheten.<br /><br/></li><li>**Observera:** måste du se till att detta klient-ID inte är linkable till alla authenticatable kontoinformation.</li><br/><li>Spara klient-ID. Använda en beständig HTTP-cookie för ID: T används i alla sessioner för att bevara ID i en browser-appen. Använd inte en sessions-cookie. För andra appar, till exempel mobilappar, använder du enhetens beständig lagring för att bevara ID.<br /><br/>Nästa gång användaren använder appen på enheten, hämta klient-ID som du sparade.</li></ul><br /> **Obs:** Bing svar kanske eller kanske inte omfattar den här rubriken. Om svaret innehåller den här rubriken, avbilda klient-ID och använda det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs:** om du inkluderar X-MSEdge-ClientID, får inte innehålla cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfria rubriken.<br /><br /> Klientenheten IPv4 eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder Platsinformationen för att bestämma beteendet för säker sökning.<br /><br />  Förvräng inte adressen (till exempel genom att ändra den sista oktetten till 0). Dölja adress resultaten på den plats som inte var som helst nära enhetens verkliga plats, vilket kan resultera i Bing som betjänar felaktiga resultat.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Frågeparametrar  
Begäran kan innehålla följande Frågeparametrar. Se kolumnen krävs för obligatoriska parametrar. Du måste URL: en koda Frågeparametrar. Frågan måste vara en absolut URL med en http eller https-schema; Vi stöder inte relativa URL: er eller andra scheman som ftp: / /
  
  
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Marknaden var resultatet kommer från. <br /><br />En lista över möjliga marknaden värden finns i [marknaden koder](#market-codes).<br /><br /> **Obs:** URL: en förhandsversion av API: et stöder för närvarande endast USA geografi och engelska.<br /><br />|Sträng|Ja|  
|<a name="query" />frågor och|URL: en för att förhandsgranska|Sträng|Ja|  
|<a name="responseformat" />responseFormat|Medietyp för att använda för svaret. Här följer möjliga skiftlägeskänsliga värden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Standardvärdet är JSON. Information om JSON-objekt att svaret innehåller, finns i [Svarsobjekten](#response-objects).<br /><br />  Om du anger JsonLd innehåller svarstexten JSON-LD-objekt som innehåller sökresultaten. Information om JSON-LD finns [JSON-LD](http://json-ld.org/).|Sträng|Nej|
|<a name="safesearch"/>säker sökning|Ogiltig vuxet innehåll eller oäkta innehåll har blockerats med felkoden 400, och *isFamilyFriendly* flaggan returneras inte. <p>Är det som gäller för juridiska vuxna nedan. Statuskod returnerar 200, och *isFamilyFriendly* flaggan är inställd på false.<ul><li>safeSearch = strikt: rubrik, beskrivning, URL: en och avbildningen kommer inte att returneras.</li><li>safeSearch = måttlig; Hämta rubrik, URL: en och beskrivning, men inte beskrivande avbildningen.</li><li>safeSearch =. Få svar objekt/allt – namn, URL: en, beskrivning, bild.</li></ul> |Sträng|Krävs inte. </br> Som standard safeSearch = strikt.| 

## <a name="response-objects"></a>Svarsobjekt  
Svarsschemat är antingen en [webbsida] eller ErrorResponse, som i API för webbsökning. Om begäran misslyckas kan det översta objektet är den [ErrorResponse](#errorresponse) objekt.


|Objekt|Beskrivning|  
|------------|-----------------|  
|[Webbsida](#webpage)|Översta nivån JSON-objekt som innehåller attribut för förhandsversionen.|  

  
### <a name="error"></a>Fel  
Definierar de fel som inträffat.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kod|Felkoden som identifierar kategorin för fel. Läs en lista över möjliga koder [felkoder](#error-codes).|Sträng|  
|<a name="error-message" />meddelande|En beskrivning av felet.|Sträng|  
|<a name="error-moredetails" />moreDetails|En beskrivning som ger ytterligare information om felet.|Sträng|  
|<a name="error-parameter" />Parametern|Frågeparametern i begäran som orsakade felet.|Sträng|  
|<a name="error-subcode" />Obligatorisk|Felkoden som identifierar felet. Till exempel om `code` är InvalidRequest, `subCode` kanske ParameterInvalid eller ParameterInvalidValue. |Sträng|  
|<a name="error-value" />värde|Värde för Frågeparametern som inte var giltig.|Sträng|  
  

### <a name="errorresponse"></a>ErrorResponse  
Det översta objekt som svaret innehåller när begäran misslyckas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Typ-tipset.|Sträng|  
|<a name="errors" />fel|En lista över fel som beskriver orsaker varför begäran misslyckades.|[Fel](#error)]|   
  

### <a name="webpage"></a>Webbsida  
Definierar informationen om en webbsida i förhandsversion.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|
|namn|En rubrik, inte nödvändigtvis HTML-rubrik|Sträng|
|url|Den URL som faktiskt har crawlats (begäran kan ha följt omdirigeringar)|Sträng|  
|beskrivning|Kort beskrivning på sidan och innehåll|Sträng|  
|isFamilyFriendly|Mest korrekta för objekt i web-index. i realtid hämtar gör denna identifiering som endast baseras på URL: en och inte sidinnehåll|boolesk|
|primaryImageOfPage/contentUrl|URL: en till en representativ avbildning som ska ingå i förhandsversionen|Sträng| 


### <a name="identifiable"></a>Identifierbar
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|id|Resurs-ID|Sträng|
 

## <a name="error-codes"></a>Felkoder

Här följer möjliga HTTP-statuskoder som returnerar en begäran.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|200|Lyckades.|  
|400|En av frågeparametrarna är saknas eller är inte giltig.| 
|400|ServerError, underkod ResourceError: Det gick inte att nå den begärda Webbadressen|
|400|ServerError, underkod ResourceError: den begärda Webbadressen returnerade inte en framgångskod (inklusive om den returnerade HTTP 404)|
|400|InvalidRequest, underkod blockerad: den begärda URL: en kan innehålla vuxet innehåll och som har blockerats| 
|401|Prenumerationsnyckeln saknas eller är inte giltig.|  
|403|Användaren har autentiserats (till exempel de använde en giltig prenumerationsnyckel), men de inte har behörighet till den begärda resursen.<br /><br /> Bing kan också returnera denna status om anroparen överskridits deras frågor per månad kvot.|  
|410|Begäran används HTTP i stället för HTTPS-protokollet. HTTPS är det enda protokollet som stöds.|  
|429|Anroparen har överskridit sina frågor per sekund kvot.|  
|500|Oväntat serverfel.|

Om begäran misslyckas svaret innehåller en [ErrorResponse](#errorresponse) objekt som innehåller en lista över [fel](#error) objekt som beskriver vad som orsakade av fel. Om felet är relaterat till en parameter i `parameter` fältet identifierar den parameter som är problemet. Och om felet är relaterat till ett parametervärde den `value` fältet identifierar det värde som inte är giltig.

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

Här följer möjliga fel kod och underordnade fel kodvärden.

|Kod|Obligatorisk|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskod 400.<br/><br/>Bing returnerar HttpNotAllowed om du använder HTTP-protokollet i stället för HTTPS och HTTP-statuskoden är 410.
|RateLimitExceeded|Inga underordnade koder|Bing returnerar RateLimitExceeded varje gång du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Om du överskrider Indexlagring Bing returnerar HTTP-statuskod 429 och om du överskrider QPM Bing returnerar 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller prenumerationsnyckeln är inte giltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization, är HTTP-statuskod 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization, är HTTP-statuskod 403.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](csharp.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för JavaScript](javascript.md)
- [Snabbstart för noden](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)

